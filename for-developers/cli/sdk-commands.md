# SDK Commands

The `sdk` subcommand provides utilities for reviewing, interpreting, and working with BitBadges data. All commands are available via both `bitbadges-cli sdk` and `bitbadgeschaind sdk`.

## review

Run validation, audit, and standards checks on a transaction or collection. Accepts a JSON file, inline JSON, collection ID (numeric), or `-` for stdin.

```bash
bitbadges-cli sdk review tx.json
bitbadges-cli sdk review collection.json --human
bitbadges-cli sdk review 42 --testnet
echo '{"messages":[...]}' | bitbadges-cli sdk review -
```

If `BITBADGES_API_KEY` is set, transactions are sent to the enriched simulate endpoint. Falls back to local analysis (`validateTransaction` + `auditCollection` + `verifyStandardsCompliance`) if the API is unavailable.

For collection JSON (no `messages` field), runs `auditCollection` and `verifyStandardsCompliance` locally.

## interpret-tx

Generate a human-readable explanation of a transaction.

```bash
bitbadges-cli sdk interpret-tx tx.json
bitbadges-cli sdk interpret-tx '{"messages":[...]}' --output-file summary.txt
```

## interpret-collection

Generate a human-readable explanation of a collection. Accepts a JSON file, inline JSON, numeric collection ID (fetched via API), or `-` for stdin.

```bash
bitbadges-cli sdk interpret-collection collection.json
bitbadges-cli sdk interpret-collection 42 --testnet
```

## address convert

Convert addresses between `bb1` (Cosmos) and `0x` (Ethereum) formats.

```bash
bitbadges-cli sdk address convert 0x1234...abcd --to bb1
bitbadges-cli sdk address convert bb1qpzm... --to 0x
```

## address validate

Check if an address is valid and detect its chain type.

```bash
bitbadges-cli sdk address validate bb1qpzm...
# { "valid": true, "chain": "BitBadges", "address": "bb1qpzm..." }
```

## lookup-token

Look up token info by symbol. Returns IBC denom, decimals, supported networks, and backing address (for IBC tokens). Omit the symbol to list all known tokens.

```bash
bitbadges-cli sdk lookup-token          # list all tokens
bitbadges-cli sdk lookup-token BADGE    # specific token info
```

## alias for-ibc-backing

Generate the backing address for an IBC-backed smart token.

```bash
bitbadges-cli sdk alias for-ibc-backing ibc/27394FB092D2ECCD...
```

## alias for-wrapper

Generate the wrapper path address for a cosmos coin wrapper.

```bash
bitbadges-cli sdk alias for-wrapper ubadge
```

## alias for-mint-escrow

Generate the mint escrow address for a collection (where quest reward funds are sent, etc.).

```bash
bitbadges-cli sdk alias for-mint-escrow 42
```

## gen-list-id

Generate a reserved address list ID from a set of addresses.

```bash
bitbadges-cli sdk gen-list-id bb1abc... bb1def...
bitbadges-cli sdk gen-list-id bb1abc... --blacklist
```

## skills

List available Builder skills or fetch a specific skill's instructions from the bundled docs.

```bash
bitbadges-cli sdk skills               # list all skills
bitbadges-cli sdk skills smart-token   # show specific skill
```

## docs

Browse bundled BitBadges documentation.

```bash
bitbadges-cli sdk docs                      # show section tree
bitbadges-cli sdk docs all                  # dump full documentation
bitbadges-cli sdk docs learn                # specific section
bitbadges-cli sdk docs learn/approvals      # navigate deeper with slash paths
```

## status

Show CLI status: active API key (masked), network, base URL, config path, SDK version, and API health check.

```bash
bitbadges-cli sdk status
bitbadges-cli sdk status --testnet
```
