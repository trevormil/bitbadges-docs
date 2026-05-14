# Chain Commands

The chain binary (`bitbadgeschaind`, aliased as `bb`) provides key management, transaction signing, broadcasting, and on-chain queries. It is a standard Cosmos SDK binary with BitBadges-specific modules.

Every command on this page works with either binary name. We use `bb` throughout the rest of the docs; `bitbadgeschaind` is the canonical underlying name and is still accepted everywhere.

## Key Management

BitBadges supports both Ethereum-compatible keys (`eth_secp256k1`) and standard Cosmos keys (`secp256k1`). The default keyring backend is `test` (unencrypted, for development). For production, use `--keyring-backend os` or `--keyring-backend file`.

```bash
# Create a new key
bb keys add mykey

# Recover from mnemonic
bb keys add mykey --recover

# List keys
bb keys list

# Show key details (add --bech val for validator address)
bb keys show mykey

# Export / Import
bb keys export mykey
bb keys import mykey keyfile.armor

# Delete a key
bb keys delete mykey
```

## Submitting Transactions

All transaction commands follow this pattern:

```bash
bb tx <module> <command> [args...] \
  --from mykey \
  --chain-id bitbadges-1 \
  --node https://lcd.bitbadges.io:443 \
  --gas auto \
  --gas-adjustment 1.5 \
  --fees 10000ubadge
```

### Common Flags

| Flag | Description | Default |
|------|-------------|---------|
| `--from` | Key name or address to sign with | (required) |
| `--chain-id` | Cosmos chain ID | `bitbadgeschain` |
| `--node` | Node RPC endpoint | `tcp://localhost:26657` |
| `--gas` | Gas limit (`auto` to simulate) | `200000` |
| `--gas-adjustment` | Multiplier when using `auto` gas | `1.0` |
| `--fees` | Transaction fee (e.g. `10000ubadge`) | |
| `--keyring-backend` | Keyring backend (`os`, `file`, `test`) | `test` |
| `--broadcast-mode` | `sync`, `async`, or `block` | `sync` |
| `--dry-run` | Simulate without broadcasting | `false` |
| `--generate-only` | Generate unsigned tx JSON | `false` |

### Network Endpoints

| Network | Cosmos Chain ID | Node LCD | EVM Chain ID |
|---------|----------------|----------|--------------|
| **Mainnet** | `bitbadges-1` | `https://lcd.bitbadges.io` | 50024 |
| **Testnet** | `bitbadges-2` | `https://lcd-testnet.bitbadges.io` | 50025 |

## Tokenization Module

The `tokenization` module handles collections, tokens, transfers, approvals, and dynamic stores. Most commands accept a JSON argument (inline or file path).

### Collections

```bash
# Create a collection
bb tx tokenization create-collection ./create-collection.json \
  --from mykey --chain-id bitbadges-1 --node https://lcd.bitbadges.io:443

# Universal update (can modify any field)
bb tx tokenization universal-update-collection ./update.json \
  --from mykey --chain-id bitbadges-1 --node https://lcd.bitbadges.io:443

# Update collection
bb tx tokenization update-collection ./update.json \
  --from mykey --chain-id bitbadges-1 --node https://lcd.bitbadges.io:443

# Delete collection
bb tx tokenization delete-collection 1 \
  --from mykey --chain-id bitbadges-1 --node https://lcd.bitbadges.io:443
```

### Transfers

```bash
bb tx tokenization transfer-tokens ./transfer.json \
  --from mykey --chain-id bitbadges-1 --node https://lcd.bitbadges.io:443
```

Example `transfer.json`:

```json
{
  "collectionId": "1",
  "transfers": [
    {
      "from": "bb1...",
      "toAddresses": ["bb1..."],
      "balances": [
        {
          "amount": "1",
          "tokenIds": [{ "start": "1", "end": "1" }],
          "ownershipTimes": [{ "start": "1", "end": "18446744073709551615" }]
        }
      ]
    }
  ]
}
```

### Approvals

```bash
# Set incoming / outgoing approval
bb tx tokenization set-incoming-approval 1 ./approval.json --from mykey ...
bb tx tokenization set-outgoing-approval 1 ./approval.json --from mykey ...

# Delete approvals
bb tx tokenization delete-incoming-approval 1 my-approval-id --from mykey ...
bb tx tokenization delete-outgoing-approval 1 my-approval-id --from mykey ...

# Update all user approvals at once
bb tx tokenization update-user-approved-transfers ./approvals.json --from mykey ...

# Purge expired approvals
bb tx tokenization purge-approvals 1 true "" false '[]' --from mykey ...
```

### Metadata and Configuration

```bash
bb tx tokenization set-setcollectionmetadata ./metadata.json --from mykey ...
bb tx tokenization set-settokenmetadata ./metadata.json --from mykey ...
bb tx tokenization set-setcustomdata ./data.json --from mykey ...
bb tx tokenization set-setstandards ./standards.json --from mykey ...
bb tx tokenization set-setcollectionapprovals ./approvals.json --from mykey ...
bb tx tokenization set-valid-token-ids ./tokenids.json --from mykey ...
bb tx tokenization set-manager ./manager.json --from mykey ...
bb tx tokenization set-setisarchived ./archived.json --from mykey ...
```

### Dynamic Stores

```bash
bb tx tokenization create-dynamic-store true --from mykey ...
bb tx tokenization update-dynamic-store 1 false true --from mykey ...
bb tx tokenization set-dynamic-store-value 1 bb1... true --from mykey ...
bb tx tokenization delete-dynamic-store 1 --from mykey ...
```

### Address Lists

```bash
bb tx tokenization create-address-lists ./lists.json --from mykey ...
```

## Query Commands

```bash
# Collection details
bb query tokenization collection 1

# Balance for an address
bb query tokenization balance 1 bb1...

# Collection statistics
bb query tokenization get-collection-stats 1

# Module parameters
bb query tokenization params

# Dynamic store queries
bb query tokenization get-dynamic-store
bb query tokenization get-dynamic-store-value
```

All query commands support `--node` to specify which node to query and `--output json` for JSON output.

## JSON Input

Most transaction commands accept JSON either inline or from a file path:

```bash
# From a file
bb tx tokenization create-collection ./my-collection.json --from mykey ...

# Inline JSON
bb tx tokenization create-collection '{"collectionMetadataTimeline": [...]}' --from mykey ...
```

Using files is recommended for complex transactions. The JSON schema for each message matches the protobuf definitions documented in the [Messages](../../x-tokenization/messages/README.md) section.
