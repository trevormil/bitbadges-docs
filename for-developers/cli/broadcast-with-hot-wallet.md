# Broadcast With Hot Wallet

`bitbadges-cli broadcast-with-hot-wallet` lets you create a new collection **without bringing your own Cosmos wallet**. The CLI generates a throwaway signer on demand, funds it (via the faucet or manually), signs the create-collection tx, and hands ownership of the new collection to an address you specify. The throwaway signer is discarded right after.

This is the easiest way for agents, one-shot scripts, and anyone who just wants to try BitBadges without the usual wallet-setup ceremony to ship a collection on-chain.

## The Mental Model

When you create a collection with this command, two addresses are involved:

1. **The hot wallet** — an ephemeral signer the CLI generates fresh every run. It exists only to put the create-collection transaction on-chain.
2. **The owner** — the address you pass via `--manager`. This is the real, long-lived owner of the collection. Every subsequent update, manager change, or permission edit has to come from this address.

The hot wallet has **no lasting authority** over the collection it creates. It signs once and is done. Everything about ownership, permissions, and future updates is captured by `--manager`.

## Quickstart (Local Chain)

Pipe the output of any template that produces a new collection straight into the broadcast command:

```bash
bitbadges-cli builder templates subscription \
    --interval monthly --price 10 --denom USDC \
    --recipient bb1your-payout-address... \
    --name "My Subscription" --json-only \
  | bitbadges-cli broadcast-with-hot-wallet \
    --msg-stdin \
    --manager bb1your-real-address... \
    --local --fund faucet
```

You'll see:

```
Generated hot wallet bb1abc...
Recovery file: /home/you/.bitbadges/hot-wallets/2026-04-15T13-21-43-900Z-bb1abc....json
Requesting dust from faucet at http://localhost:3001 ...
Waiting for account on-chain (up to 60s)...
Broadcasting tx (fee=0ubadge, gas=400000)...
{
  "success": true,
  "ephemeralAddress": "bb1abc...",
  "recoveryPath": "/home/you/.bitbadges/hot-wallets/2026-04-15T13-21-43-900Z-bb1abc....json",
  "txHash": "A6020AAD2E63...",
  "collectionId": "28"
}
```

## Flags

| Flag | Default | Description |
|---|---|---|
| `--msg-file <path>` | — | Read the create-collection JSON from a file. |
| `--msg-stdin` | — | Read the JSON from stdin. Use this when piping from `builder templates …`. |
| `--manager <bb1…>` | **required** | Address that will own the created collection. Refuses to run without it — orphaning a collection on the throwaway signer would lose it forever. |
| `--fund <faucet\|manual>` | `faucet` | How to get dust into the hot wallet. `faucet` hits the indexer's faucet endpoint. `manual` prints the address and waits for you to fund it yourself (useful on mainnet where the faucet won't hand out enough for real fees). |
| `--fee <amount>` | `0` | Fee amount in base units. Defaults to zero — the chain currently accepts zero-fee txs. Bump if you want to prioritize your tx. |
| `--fee-denom <denom>` | `ubadge` | Fee denom. |
| `--gas <number>` | `400000` | Gas limit. |
| `--new` | — | Always create a fresh hot wallet; skip the picker. |
| `--reuse <selector>` | — | Reuse a saved hot wallet by address or recovery file path. |
| `--non-interactive` | off | Never prompt. If polling or funding stalls, save state and exit so the run can be resumed later. Forced when stdout is not a TTY. |
| `--poll-timeout <seconds>` | `60` | How long to wait for the faucet or manual funding to land before asking what to do next. |
| `--network <name>` | `mainnet` | `mainnet`, `testnet`, or `local`. Sets the chain + indexer endpoints + bech32 prefix. |
| `--local` / `--testnet` | — | Shortcuts for `--network local` / `--network testnet`. |
| `--url <url>` | — | Custom indexer URL (overrides network). |

The `--fund faucet` mode requires `BITBADGES_API_KEY` to be set for `--network mainnet` and `--network testnet`. On `--local`, no key is needed.

## Supported Transaction Types

Only **create-collection** transactions are supported — specifically `MsgCreateCollection` and `MsgUniversalUpdateCollection` with a new collection ID. Every other transaction type is rejected up front. The reason is simple: the throwaway signer doesn't hold any lasting state, so using it to sign an update, transfer, or approval (which all need a real persistent identity) would be meaningless.

If you pass a different transaction type, the command exits without touching the faucet and tells you which path to use instead.

## The Wallet Picker

When you run the command in an interactive terminal and you already have saved hot wallets for the chosen network, the CLI shows you a picker:

```
⚠  HOT WALLET — throwaway burner keys, stored in plaintext.
   Files live under /home/you/.bitbadges/hot-wallets/ with mode 0600.
   Anyone with read access to that folder can spend the dust in these wallets.
   The collection itself is owned by --manager, not the hot wallet, so these keys
   are disposable by design. Don't reuse them for anything of value.

Pick a hot wallet for this broadcast (network: local):

  [0]  ✨ Create a new hot wallet
  [1]  bb1abc...  bal: 1000ubadge  status: funded     2026-04-15T13:21:43.900Z
  [2]  bb1def...  bal: 0ubadge     status: broadcast  2026-04-15T13:20:14.541Z
```

Selecting an existing wallet re-uses it: the CLI re-fetches the on-chain account number and sequence, skips the faucet step if the wallet is already funded, and goes straight to signing. Selecting a wallet that has already broadcast a tx prompts you to confirm — it's unusual and usually a mistake.

Pass `--new` to always skip the picker, or `--reuse <address>` to bypass it by picking a specific wallet. In non-TTY environments (CI, agents, piped stdin) the picker is skipped automatically and you always get a fresh wallet unless you pass `--reuse`.

## Recovery & Safety

Every hot wallet is written to disk **in plaintext** under `~/.bitbadges/hot-wallets/` (or `$BITBADGES_CONFIG_DIR/hot-wallets/` if you've set that). Files are written with mode 0600 so only your user can read them, and the directory itself is mode 0700. The files contain the wallet's mnemonic, address, network, and the status of its one and only broadcast.

Plaintext is an intentional tradeoff. These wallets hold at most a few units of dust, they sign one transaction and are done, and they carry zero authority over the collections they create. The value of being able to recover funds or resume an interrupted broadcast outweighs the value of keystore encryption for keys this disposable. **Do not reuse hot wallets for anything you care about, and do not commit the hot-wallet directory to source control.**

Companion commands for managing saved wallets live under `bitbadges-cli hot-wallet`:

```bash
bitbadges-cli hot-wallet list                       # show every saved wallet
bitbadges-cli hot-wallet show <address>             # inspect one (includes mnemonic)
bitbadges-cli hot-wallet resume <address> \
    --msg-file subscription.json --manager bb1... \
    --local                                         # re-enter a paused broadcast
bitbadges-cli hot-wallet sweep <address> \
    --to bb1your-real-address... --local            # send any remaining dust out
bitbadges-cli hot-wallet forget <address>           # delete the recovery file
```

## What Happens If Funding Is Slow

Zero-fee transactions on a quiet chain usually land in under ten seconds, but things can stall: a backed-up faucet queue, a missed block, or a slow manual transfer. If the wallet isn't funded by the time `--poll-timeout` elapses, the CLI doesn't exit — it asks what you want to do.

- **Keep waiting** (default). Polls for another window of the same length.
- **Retry the faucet**. Sends another request to the indexer faucet endpoint.
- **Pause and exit**. Writes the recovery file with a `pending` status and quits cleanly. Pick it back up later with `bitbadges-cli hot-wallet resume …`.
- **Give up**. Marks the wallet as failed. You can still sweep any dust back out afterwards.

In non-interactive environments (or with `--non-interactive`), the CLI defaults to **pause and exit** so automated callers never hang forever and funds are never lost to a timeout.

## Failure Modes

- **Wrong `--manager`** — the command refuses if `--manager` is missing or invalid. It never touches the faucet for a broken run.
- **Unsupported transaction type** — anything other than a new-collection message is rejected before the wallet is even generated.
- **Unset `BITBADGES_API_KEY`** with `--fund faucet` on `--network mainnet` or `--network testnet` — the faucet will reject the request. Use `--local` for local development or set the key.
- **Faucet already airdropped to this address** — the faucet dedupes by address. The CLI generates a fresh hot wallet per run, so you'd only hit this by reusing a wallet that already got its dust.
- **Manual funding never arrives** — the CLI pauses and lets you resume once the funding lands.

In every failure mode, the recovery file captures the state so nothing is silently lost.
