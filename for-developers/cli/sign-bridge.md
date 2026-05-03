# Sign Bridge (Browser Wallet Handoff)

The sign bridge is the CLI's way to use a browser-resident wallet (Keplr, MetaMask, Phantom, WalletConnect) for signing — without exporting the key, without setting up a chain-binary wallet, and without giving the CLI any signing material.

It works the same way `gh auth login --web` and `npm login --auth-type=web` do:

1. The CLI starts a tiny HTTP listener on `127.0.0.1:<port>` and prints a one-time PIN.
2. It opens your default browser to `https://bitbadges.io/sign?…` with the request encoded in the URL (or in a Redis-backed short code if the payload is large).
3. You verify the PIN matches the one in your terminal, review the request, and sign with whatever wallet you have connected.
4. The browser redirects to `127.0.0.1:<port>/callback?…` with the signature or transaction hash. The listener catches it, the CLI completes the flow, and you go back to your terminal.

The wallet never leaves the browser; the CLI only ever sees the resulting signature or tx hash. This is the headlining "I have my wallet in MetaMask, I want my agent on a headless box to act as me" workflow.

## When to use it

| You have… | Use this |
|---|---|
| A Cosmos key in `bitbadgeschaind keys` (or a hardware wallet that signs ADR-36) | Standard [`auth login`](auth-commands.md) flow with `bitbadgeschaind sign-arbitrary` |
| Just a browser wallet (Keplr / MetaMask / Phantom) | **Sign bridge** — the commands on this page |
| A custom programmatic signer (ethers/viem, custodial, hardware over a non-standard transport) | [`gen-tx-payload`](#gen-tx-payload) — emits SignDoc bytes ready to feed your signer |
| A throwaway wallet that you'll fund from the faucet for one tx | [`deploy --burner`](deploy-commands.md) |

The sign bridge and `--burner` are alternatives — pick one path on `deploy`. `--burner` produces a fresh keypair locally and signs with it; `--browser` hands the message to your real wallet via a browser tab.

## Three modes

Every sign-bridge command resolves to one of three URL `mode` values on the `/sign` page:

| Mode | Used by | What lands on `/sign` | What comes back |
|---|---|---|---|
| `login` | `auth login --browser` | The Blockin challenge message | Signature → CLI replays it on `/auth/verify` to mint a Full Access session cookie |
| `msg` | `sign-with-browser` | An arbitrary string to sign | Signature + address (+ pubkey for Cosmos) |
| `tx` | `deploy --browser`, `build … --deploy-with-browser` | A Cosmos `MsgCreateCollection` (or any single tx). For collection-creation msgs the page runs the same review pipeline as `/builder/preview`, so you see the full Preview / Review Items / Transferability / Permissions / Details / Compatibility / Alternatives sidebar before you sign. | Tx hash (or signed bytes if `--sign-only` is set) |

## The `/sign` page

What the user sees:

1. **A 6-digit PIN.** The CLI prints this same PIN to stderr; cross-verifying defends against landing on a phishing page that pretends to be from your CLI.
2. **A "review and trust" warning.** A signature here can authorize transactions, transfer ownership, or grant access on behalf of the connected wallet. The PIN proves the page was launched by your CLI — it does **not** vouch for what's inside.
3. **The full request.** For login/msg modes, the message is shown verbatim. For tx mode with a single creation msg, the page renders the same StreamlinedBuilderPreview the dashboard uses internally.
4. **A wallet-mismatch panel** if the connected wallet's address (bb1-converted) doesn't match the address the CLI expected. The Sign button stays disabled until the addresses match. There's an inline **Disconnect this wallet** button so you can switch wallets without leaving the page.
5. **A single full-width Sign button.** Click → wallet popup (Keplr/MetaMask/etc) → sign. The browser tab redirects back to your loopback listener; the CLI prints the result.

The page rejects any redirect target that isn't a `127.0.0.1` or `localhost` URL — RFC 8252 §7.3 native-app loopback exception, same posture `gh` and `gcloud` use.

## auth login --browser

Mint a Full Access session cookie by signing the Blockin challenge with a browser wallet.

```bash
bitbadges-cli auth login --browser \
  --address bb1...               # or 0x... for ETH
```

What happens:

1. The CLI fetches a fresh challenge from `/api/v0/auth/getChallenge` and captures the response cookie (the indexer binds the challenge nonce to that cookie).
2. It opens your browser to `/sign?mode=login&…`.
3. You sign with Keplr or MetaMask.
4. The CLI replays the captured cookie on `/auth/verify` with the signature, the indexer mints a Full Access session, and the new cookie lands in `~/.bitbadges/auth.json` keyed by network + address.

After this, every `bitbadges-cli api … --with-session` call attaches that cookie automatically — the same shape the headless `auth login --signature …` flow produces.

| Flag | Description |
|---|---|
| `--address <addr>` | Required. `bb1…` for Cosmos, `0x…` for ETH. |
| `--browser` | Use the bridge instead of an external signature. Mutually exclusive with `--signature`. |
| `--frontend-url <url>` | Override the frontend base (defaults vary by network). |
| `--no-open` | Print the sign URL instead of auto-launching the browser. Useful for SSH sessions, headless CI, or when you want to copy the URL into a different browser. |
| `--port <n>` | Pin the loopback listener to a specific port. Set this when your browser and your CLI live on different machines and you're running an SSH tunnel. |
| `--timeout <seconds>` | How long to wait for the wallet (default 300, max 1800). |
| `--2fa <code>` / `--2fa-backup <code>` | Account 2FA, if enabled. |

`--public-key` is **not** required when `--browser` is used; the bridge captures the pubkey from the wallet's signature response (Keplr's ADR-36 reply for Cosmos) and forwards it for you.

## sign-with-browser

Hand an arbitrary message to the browser wallet and print the signature as JSON. Use this when you need a personal signature for something other than `/auth/verify` — a third-party SIWBB challenge, an attestation message, etc.

```bash
bitbadges-cli sign-with-browser \
  --message "I authorize XYZ at $(date -u +%FT%TZ)" \
  --expected-address bb1...
```

Output:

```json
{
  "signature": "LIq6qlnCklLzUoHj…",
  "address": "bb1lmrs9a9ydq6z32chl0kdfc0ww2zmvx95uh7dk3",
  "publicKey": "AhUwD2T89QJN+vHUlp4gx0uWT2Cmp5W2KISKu6n2w+TE",
  "chain": "Cosmos"
}
```

Input shapes (any one):

- `--message <text>` — inline string
- `--message-file <path>` — file path (`-` for stdin)
- Positional `<input>` — inline message, `-` for stdin, or `@path` for a file

Common flags:

| Flag | Description |
|---|---|
| `--expected-address <addr>` | Disable the Sign button until the connected wallet matches. |
| `--frontend-url <url>` | Frontend base override. |
| `--no-open` / `--port <n>` / `--timeout <seconds>` | Same as `auth login --browser`. |

For ETH addresses, `publicKey` is omitted — `personal_sign` signatures are recoverable from `(signature, message, address)`.

## deploy --browser

Broadcast a single tx via the user's connected wallet. Parallel to [`deploy --burner`](deploy-commands.md): pick one path.

```bash
bitbadges-cli deploy --browser \
  --msg-file collection.json \
  --manager bb1...
```

Output:

```json
{
  "success": true,
  "path": "browser",
  "mode": "sign-and-broadcast",
  "txHash": "F2318CD6EBA6AF3A930AC1AB0C94CFB86283B4A7C18B4E8E90350169C8F3D1A4",
  "chain": "cosmos"
}
```

The frontend's `TxModal` does the heavy lifting: account number, sequence, gas, and fees are auto-fetched from the indexer at sign time. You don't need to pre-populate any of those in the msg JSON — the bridge passes the canonical msg through and `TxModal` fills the mechanical state.

For ETH wallets, the same flow works: the connected MetaMask wallet signs an `MsgEthereumTx`-wrapped Cosmos tx via Privy, broadcasts, and the cosmos hash comes back to the CLI. Set `--manager` to the bb1-equivalent of your ETH address.

| Flag | Description |
|---|---|
| `--browser` | Required to pick this path. Mutually exclusive with `--burner`. |
| `--manager <addr>` | The lasting owner of the new collection. Required for `--burner`; recommended for `--browser` (otherwise defaults to the connected wallet's address). |
| `--expected-address <addr>` | Wallet-mismatch guard for the `/sign` page. Defaults to `--manager`. |
| `--sign-only` | Don't broadcast — return signed tx bytes for caller-controlled broadcast. See [Sign-only mode](#sign-only-mode). |
| `--frontend-url`, `--no-open`, `--port`, `--timeout` | Bridge plumbing, same as `auth login --browser`. |

### Sign-only mode

Add `--sign-only` and the bridge stops right after the wallet signs — no broadcast. The CLI receives base64-encoded TxRaw bytes you can submit on your own schedule.

```bash
bitbadges-cli deploy --browser --sign-only \
  --msg-file collection.json \
  --manager bb1...
```

Output:

```json
{
  "success": true,
  "path": "browser",
  "mode": "sign-only",
  "signedTx": "Cu0MCgN…",
  "chain": "cosmos"
}
```

When this is useful:

- **Custodial submitters** that take signed bytes and broadcast on a schedule.
- **Retry / backoff control** — your code decides when to POST and how many times.
- **Batch flows** — collect N signed txs, broadcast them in one push.
- **Dry-run-then-commit** — sign now, hold the bytes, broadcast once an off-chain condition is met.

The signed bytes are the same shape as `tx_bytes` for `/api/v0/broadcast`. POST with `{"tx_bytes": <bytes>, "mode": "BROADCAST_MODE_SYNC"}`.

## build … --deploy-with-browser

Compose `bitbadges-cli build <preset>` and `deploy --browser` in one shot, parallel to `--deploy-with-burner`:

```bash
bitbadges-cli build vault \
  --name "my-vault" \
  --description "..." \
  --image "https://example.com/img.png" \
  --manager bb1... \
  --backing-coin BADGE \
  --deploy-with-browser
```

The build runs first, the resulting msg JSON is handed to the bridge, and the wallet signs + broadcasts. `--sign-only` is also accepted here.

## gen-tx-payload

For programmatic signers that aren't a chain binary and aren't a browser wallet — custom EVM wallets (ethers/viem), custodial signers, hardware wallets that take SignDoc bytes directly — `gen-tx-payload` emits a fully-populated payload envelope you can feed into your own signing code.

```bash
# Cosmos signer (cosmjs / hardware wallet)
bitbadges-cli build vault … --json-only \
  | bitbadges-cli gen-tx-payload --from bb1... --gas 600000

# EVM-only signer (ethers / viem on the BitBadges EVM chain)
bitbadges-cli gen-tx-payload \
  --msg-file collection.json \
  --from 0x39A1F08F4B6da42bcFB8EBC8c92A1D04978ad0bD \
  --gas 600000

# Both, in one envelope
bitbadges-cli gen-tx-payload \
  --msg-file collection.json \
  --from bb1... \
  --with-evm-tx \
  --gas 600000
```

Output (Cosmos + EVM example, abbreviated):

```json
{
  "chain": "cosmos+evm",
  "chainId": "bitbadges-1",
  "sender": {
    "address": "bb1lmrs9a9ydq6z32chl0kdfc0ww2zmvx95uh7dk3",
    "accountNumber": "7",
    "sequence": "3",
    "publicKey": "AhUwD2T89QJN+vHUlp4gx0uWT2Cmp5W2KISKu6n2w+TE"
  },
  "evmAddress": "0x39A1F08F4B6da42bcFB8EBC8c92A1D04978ad0bD",
  "fee": { "amount": "0", "denom": "ubadge", "gas": "600000" },
  "memo": "",
  "messages": [{ "typeUrl": "/tokenization.MsgCreateCollection", "value": { … } }],
  "signDirect":  { "bodyBytes": "Cr…", "authInfoBytes": "Cl…", "signBytes": "Athauu8a…" },
  "legacyAmino": { "bodyBytes": "Cr…", "authInfoBytes": "Cl…", "signBytes": "9bJ06ZSK…" },
  "evmTx": {
    "to": "0x0000000000000000000000000000000000001001",
    "data": "0x059dfe13…",
    "value": "0",
    "functionName": "createCollection",
    "chainId": 50024,
    "gasLimit": "600000"
  },
  "broadcastEndpoint": "https://api.bitbadges.io/api/v0/broadcast"
}
```

### What gets generated

| Field | When it's present | What it's for |
|---|---|---|
| `signDirect.signBytes` | `--from bb1…` (or any time a Cosmos pubkey is on chain) | What you sign for `SIGN_MODE_DIRECT` (the Cosmos default) |
| `legacyAmino.signBytes` | Same as above | Hardware wallets that already speak amino |
| `signDirect.bodyBytes` / `authInfoBytes` | Same as above | Plug into `TxRaw{bodyBytes, authInfoBytes, signatures: [sig]}` after signing |
| `evmTx` | `--from 0x…` OR `--evm-from 0x…` OR `--with-evm-tx` | Drop into `wallet.sendTransaction({to, data, value, chainId, gasLimit})` for ethers/viem |
| `broadcastEndpoint` | Always | Where to POST the assembled `TxRaw` |

### Three flow shapes

| Use case | Flag combo | Output |
|---|---|---|
| Cosmos signer (cosmjs, hardware) | `--from bb1…` | signDirect + legacyAmino |
| EVM-only signer (custom EVM wallet, agent script) | `--from 0x…` | evmTx |
| Either-or (build once, sign whichever stack is easier) | `--from bb1… --with-evm-tx` | signDirect + legacyAmino + evmTx |

Public-key handling: for `--from bb1…` the indexer-fetched pubkey populates `signDirect`. For `--from 0x…`, the EVM tx is self-signing — no separate pubkey is required (the wallet's secp256k1 key signs the EIP-1559 tx directly). If you want signDirect for an ETH user, the account must have a publicKey on chain (typically achieved by sending one prior tx).

### Offline / air-gapped

Pass `--no-fetch` with explicit `--account-number`, `--sequence`, and (for Cosmos signing) `--public-key`:

```bash
bitbadges-cli gen-tx-payload \
  --msg-file collection.json \
  --from bb1... \
  --no-fetch \
  --account-number 7 \
  --sequence 3 \
  --public-key AhUwD2T89QJN+…
```

Useful when the indexer isn't reachable from the signing host (air-gapped HSM rigs, segmented networks, etc).

### Supported message types

`gen-tx-payload` covers every `tokenization.Msg*` type — i.e. every output of `bitbadges-cli build <preset>`. Non-tokenization message types (custom Cosmos modules, IBC, gov) are out of scope; emit those with your existing proto-encoding stack and the rest of this CLI's tooling won't interfere.

## Threat model

The sign bridge is layered defense:

1. **Loopback-only return URLs.** The `/sign` page rejects any redirect target that isn't `127.0.0.1` or `localhost`. Without this, an attacker could craft a `bitbadges.io/sign?…&return=https://evil.com` link, get a victim to sign, and harvest the signature. Loopback-only means the listener has to be on the same machine as the browser.
2. **PIN cross-verification.** A 6-digit code shown on both stderr and `/sign`. Defends against the "I have multiple browser tabs open and I'm not sure which one is mine" failure mode, plus phishing where someone DMs a sign URL claiming to be from your CLI.
3. **Wallet-mismatch block.** The page disables the Sign button (and shows a Disconnect-this-wallet shortcut) when `chain.address` doesn't match the address the CLI declared. Stops the user from signing with the wrong account by accident.
4. **State nonce.** A random per-request token echoed back on the redirect. Mismatched state → 403 + page rejects.
5. **Single-shot listener.** The CLI's loopback HTTP server accepts exactly one valid callback. Subsequent requests get `410 Gone`.
6. **The wallet's own confirmation popup.** For tx mode, the wallet shows the actual tx contents one more time before signing. The user has to click through the wallet popup *and* the page button — two gates, both showing what's actually being signed.
7. **The "review and trust" warning beneath the PIN.** PIN proves the page came from your CLI. The warning reminds the user that proof of source ≠ proof of safety; review the contents.

What's deliberately **not** in scope:

- **Untrusted CLI.** If you run an attacker-supplied CLI, the loopback listener is the attacker. The bridge can't help here — the threat model assumes you trust the CLI you ran.
- **Compromised browser extension.** A malicious wallet extension can lie about what it's signing. That's a wallet-trust question, orthogonal to the bridge.
- **EVM multi-tx atomicity.** One `/sign` request handles one tx. Multi-tx flows on EVM (e.g., ERC20 approve + transferFrom) become two `deploy --browser` invocations; we don't try to atomic-batch in the browser.

## SSH-tunneled dev setups

When the CLI runs on a remote dev server but your browser is on your laptop (e.g. you `ssh trevormil-server` with `LocalForward 3000`/`3001` and access `localhost:3000` from the laptop), the browser's `127.0.0.1:<port>` reaches your **laptop's** loopback — not the server's. The default random ephemeral port the bridge picks isn't reachable.

Two fixes:

**Pin the listener port and forward it.** Add a `LocalForward` for a stable port to your laptop's `~/.ssh/config`:

```
Host trevormil-server
    LocalForward 4849 localhost:4849
```

Then run with `--port 4849`:

```bash
bitbadges-cli auth login --browser --address bb1... --port 4849
```

**Or run the CLI on the laptop directly** and point it at the remote indexer:

```bash
bitbadges-cli auth login --browser --address bb1... \
  --frontend-url http://localhost:3000 \
  --url http://localhost:3001/api/v0
```

This is also the realistic prod scenario: the wallet and the CLI live on the same machine; the indexer is remote.

## Reference

- [auth-commands.md](auth-commands.md) — full Blockin / `auth login` reference
- [deploy-commands.md](deploy-commands.md) — `--burner` (the alternative to `--browser`)
- [build-commands.md](build-commands.md) — every `--deploy-with-burner` / `--deploy-with-browser` preset
