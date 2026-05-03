# Auth Commands

The `bitbadges-cli auth` subcommand authenticates the CLI against the BitBadges indexer using the standard Blockin challenge → verify flow. It stores the resulting session cookie under `~/.bitbadges/auth.json` (mode `0600`, multi-account, multi-network), and `bitbadges-cli api --with-session` attaches that cookie on subsequent calls.

This is what an agent or operator runs once before hitting any indexer endpoint gated by `Full Access` — anything that mutates an account, manages keys, publishes signed data, etc. The plain API key is necessary on every call but not sufficient for those routes; the API key is the *app* scope, the session cookie is the *user* scope.

Available via both `bitbadges-cli auth` and `bb cli auth`.

## Wallet-agnostic by design

The CLI **never touches a private key**. Every `auth login` requires a signature produced by something else:

- `bitbadgeschaind sign-arbitrary` — the chain binary's offline ADR-36 signer. Recommended for fully headless agents.
- A browser wallet (MetaMask, Keplr) — copy the message out, sign in the browser, paste the signature back.
- A hardware wallet, custodial signer, HSM — anything that can produce an ADR-36 / EIP-191 signature over the challenge message.

That separation is what makes the same `auth` surface work for both an agent on a headless box and a developer signing on their laptop.

## Subcommands

| Command | Purpose |
|---|---|
| [`auth login`](#auth-login) | One-shot: fetch challenge, post your signature, store the cookie |
| [`auth challenge`](#auth-challenge) | Print a challenge for two-step / external-signer flows |
| [`auth verify`](#auth-verify) | Two-step counterpart to `login` |
| [`auth status`](#auth-status) | List stored sessions; `--check` revalidates server-side |
| [`auth use`](#auth-use) | Set the active address for a network |
| [`auth whoami`](#auth-whoami) | Print the active address for the resolved network |
| [`auth logout`](#auth-logout) | Sign out and remove the local record |
| [`auth path`](#auth-path) | Print the path of the auth store |

## Common flags

Every `auth` subcommand accepts the same network resolution flags as `bitbadges-cli api`:

| Flag | Description |
|---|---|
| `--testnet` | Use testnet API |
| `--local` | Use local API (`localhost:3001`) |
| `--url <url>` | Custom indexer base URL |
| `--api-key <key>` | Override `BITBADGES_API_KEY` for this call |

Networks are isolated in the store — a mainnet and a testnet session for the same address coexist independently.

## Headless flow (recommended for agents)

The canonical agentic flow is three commands. Each step is independent — the only state that crosses them is the `~/.bitbadges/auth.json` file.

```bash
# 1. Fetch a challenge. Saves the nonce cookie locally so step 3 can replay it.
MSG=$(bitbadges-cli auth challenge --address bb1...)

# 2. Sign the challenge with whatever signer you have. Headless Cosmos example:
SIG_JSON=$(bitbadgeschaind sign-arbitrary mykey "$MSG")

# 3. Post the signature. Stores the session cookie on success.
bitbadges-cli auth login \
  --address    "$(echo "$SIG_JSON" | jq -r .address)" \
  --signature  "$(echo "$SIG_JSON" | jq -r .signature)" \
  --public-key "$(echo "$SIG_JSON" | jq -r .pubKey)" \
  --message    "$MSG"

# 4. Make a Full Access request. --with-session attaches the cookie.
bitbadges-cli api accounts get-account --body '{"address":"bb1..."}' --with-session
```

> **Why three steps and not one?** The indexer binds each challenge nonce to the response cookie it set on `getChallenge`. Without persisting that cookie locally, a follow-up `auth verify` would hit `No sign-in request found`. `auth challenge` writes a pending entry (5-minute TTL) that `auth login` consumes.

For an interactive flow with an in-process signer, `auth login` will fetch a fresh challenge inline if no pending entry exists and `--message` is not passed.

## Browser-wallet flow (paste-in)

For users whose wallet only lives in a browser extension, the same three steps work — sign step 2 in the browser instead of with the chain binary:

```bash
# 1. Fetch and print the challenge
bitbadges-cli auth challenge --address 0x...

# 2. Open MetaMask → "Sign Message" → paste the printed message → copy the signature

# 3. Post it back
bitbadges-cli auth login \
  --address 0x... \
  --signature 0x...   # ETH addresses do not require --public-key
```

ETH addresses are detected by the `0x` prefix; Cosmos addresses by `bb1`. `--public-key` is required for Cosmos signatures (the indexer's CosmosDriver verifier needs it) and ignored for ETH.

## auth login

One-shot: fetches a challenge (or reuses a pending one), POSTs the signature, persists the cookie, and marks the address active for the network.

```bash
bitbadges-cli auth login \
  --address <addr> \
  --signature <sig> \
  [--public-key <b64>] \
  [--message <text> | --message-file <path>] \
  [--2fa <totp> | --2fa-backup <code>] \
  [network flags]
```

| Flag | Description |
|---|---|
| `--address <addr>` | Native address (`bb1...` for Cosmos, `0x...` for ETH). Required. |
| `--signature <sig>` | Hex or base64 signature over the challenge message. Required. |
| `--public-key <b64>` | Compressed pubkey, base64. Required for Cosmos. |
| `--message <text>` | Exact challenge message. Defaults to the saved pending entry. |
| `--message-file <path>` | Read the challenge message from a file (use `-` for stdin). |
| `--2fa <code>` | 6-digit TOTP code, if the account has 2FA enabled. |
| `--2fa-backup <code>` | Backup recovery code (alternative to `--2fa`). |

**Exit codes:**
- `0` — logged in, cookie stored
- `1` — signature rejected, network error, or other failure
- `2` — account requires 2FA and no `--2fa` / `--2fa-backup` was passed

## auth challenge

Fetch a Blockin challenge for an address. Prints the message your signer must sign and (by default) persists the response cookie locally so a follow-up `auth login` / `auth verify` can replay it.

```bash
bitbadges-cli auth challenge --address bb1... [--json] [--no-save-pending]
```

| Flag | Description |
|---|---|
| `--address <addr>` | Native address (`bb1...` or `0x...`). Required. |
| `--json` | Output `{message, nonce}` as JSON instead of the bare message. |
| `--no-save-pending` | Do not persist the challenge cookie locally. Advanced — use only if you intend to re-fetch the challenge yourself before verifying. |

## auth verify

Two-step counterpart to `auth login`. Identical surface; kept as a separate subcommand for muscle-memory parity with the `challenge → verify` flow.

```bash
bitbadges-cli auth verify \
  --address <addr> \
  --signature <sig> \
  [--public-key <b64>] \
  [--message <text> | --message-file <path>] \
  [--2fa <code> | --2fa-backup <code>]
```

## auth status

List stored sessions for the resolved network (or all networks with `--all`). With `--check`, additionally hits `/api/v0/auth/status` to confirm the cookie is still valid server-side.

```bash
bitbadges-cli auth status              # active session on mainnet
bitbadges-cli auth status --all        # every stored session, every network
bitbadges-cli auth status --check      # also revalidate server-side
```

Output lines look like:

```
mainnet   bb1abc...   Cosmos   expires=2026-05-10T03:14:15.000Z (valid) [server: signed-in]
```

## auth use

Set the active address for a network. `bitbadges-cli api --with-session` (without `--as-address`) uses whichever address is active for the resolved network.

```bash
bitbadges-cli auth use bb1abc...
bitbadges-cli auth use bb1xyz... --testnet
```

## auth whoami

Print the active address for the resolved network. Exits non-zero if no session is stored.

```bash
bitbadges-cli auth whoami
bitbadges-cli auth whoami --testnet
```

## auth logout

Sign out and remove the local record. Hits `/api/v0/auth/logout` server-side and deletes the entry from `auth.json`. If the server call fails (network down, cookie already expired), the local record is removed anyway.

```bash
bitbadges-cli auth logout                    # active session on mainnet
bitbadges-cli auth logout --address bb1...   # specific address
bitbadges-cli auth logout --all              # every session, every network
```

## auth path

Print the absolute path of the auth store. Useful for backups, audits, and shipping a session from a laptop to a headless agent box.

```bash
bitbadges-cli auth path
# /home/you/.bitbadges/auth.json
```

## Storage layout

`~/.bitbadges/auth.json` (mode `0600`):

```jsonc
{
  "version": 1,
  "networks": {
    "mainnet": {
      "active": "bb1abc...",
      "sessions": {
        "bb1abc...": {
          "address": "bb1abc...",
          "nativeAddress": "bb1abc...",
          "chain": "Cosmos",
          "cookieName": "bitbadges",
          "cookieValue": "...",
          "scopes": [{ "scopeName": "Full Access" }],
          "createdAt": 1746240000000,
          "expiresAt": 1746844800000,
          "indexerUrl": "https://api.bitbadges.io/api/v0"
        }
      },
      "pending": { /* short-lived challenge state, 5-min TTL */ }
    },
    "testnet": { /* ... */ },
    "local":   { /* ... */ }
  }
}
```

The store is multi-account by design. You can hold sessions for many addresses simultaneously and switch between them with `auth use`, or scope a single call with `bitbadges-cli api ... --as-address <addr>`.

## Using a stored session — `api --with-session`

`bitbadges-cli api` only attaches the cookie when you explicitly ask it to (silent session injection is a footgun):

```bash
# Use the active address for the resolved network
bitbadges-cli api accounts update-account-info \
  --body '{"username":"new"}' \
  --with-session

# Override which stored address to use
bitbadges-cli api accounts update-account-info \
  --body '{"username":"new"}' \
  --with-session --as-address bb1xyz...
```

If the resolved address has no stored session, the call exits 1 with a message pointing you at `auth login`.

## Two-factor authentication

If the account has 2FA enabled, the first `auth login` returns `requires2FA: true` and exits with code `2`. Re-run with the second factor:

```bash
bitbadges-cli auth login --address bb1... --signature ... --public-key ... \
  --2fa 123456                # TOTP code from your authenticator app
# or
bitbadges-cli auth login --address bb1... --signature ... --public-key ... \
  --2fa-backup AAAA-BBBB-CCCC # one of the backup codes from setup
```

The CLI replays the full cookie chain (Blockin nonce + indexer LB stickiness) through `/api/v0/2fa/offline/verify` for you — no extra setup needed beyond the flag.

## Notes and limitations

- **Scope is `Full Access`.** The indexer's `getChallenge` currently hard-codes the scope set; narrower-scope sessions (e.g. `Manage Claims` only) need a small indexer change. Tracked in the backlog.
- **Sessions auto-refresh on use.** The indexer runs `express-session` with `rolling: true` and a 7-day window — every authenticated request resets the cookie's expiry to "now + 7 days" server-side. The CLI captures the rolled `Set-Cookie` from each `--with-session` response and writes the new `expiresAt` back to `auth.json`, so a long-lived agent that makes any request inside the rolling window keeps its session indefinitely without re-logging in. Re-run `auth login` only after a stretch of full inactivity longer than the rolling window (or after explicit `auth logout`).
- **The pending challenge has a 5-minute TTL.** If you wait too long between `auth challenge` and `auth login`, the entry is stale and `login` will fetch a fresh challenge (which will not match the message you signed).
- **Shipping `auth.json` between machines is fine** — the cookie is just an HTTP cookie. Move the file with `scp` or equivalent; the receiving box can immediately `bitbadges-cli api --with-session`. Keep it `0600`.

## See also

- [Chain Commands — `sign-arbitrary`](chain-commands.md) — the recommended headless signer that pairs with `auth login`.
- [API Commands](api-commands.md) — every indexer route, including `--with-session` / `--as-address`.
- [CLI for AI Agents](for-ai-agents.md) — end-to-end agent workflow.
