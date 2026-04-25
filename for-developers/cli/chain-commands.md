# Chain Commands (bitbadgeschaind)

{% hint style="warning" %}
**Testnet is temporarily offline** (as of 2026-04-25) to reduce hosting costs while it sees minimal usage. Mainnet ([https://bitbadges.io](https://bitbadges.io)) is the only currently live network. The `--testnet` flag examples on this page are retained for reference and will become accurate again when testnet is reactivated.
{% endhint %}

The `bitbadgeschaind` binary provides key management, transaction signing, broadcasting, and on-chain queries. It is a standard Cosmos SDK binary with BitBadges-specific modules.

## Key Management

BitBadges supports both Ethereum-compatible keys (`eth_secp256k1`) and standard Cosmos keys (`secp256k1`). The default keyring backend is `test` (unencrypted, for development). For production, use `--keyring-backend os` or `--keyring-backend file`.

```bash
# Create a new key
bitbadgeschaind keys add mykey

# Recover from mnemonic
bitbadgeschaind keys add mykey --recover

# List keys
bitbadgeschaind keys list

# Show key details (add --bech val for validator address)
bitbadgeschaind keys show mykey

# Export / Import
bitbadgeschaind keys export mykey
bitbadgeschaind keys import mykey keyfile.armor

# Delete a key
bitbadgeschaind keys delete mykey
```

## Submitting Transactions

All transaction commands follow this pattern:

```bash
bitbadgeschaind tx <module> <command> [args...] \
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
bitbadgeschaind tx tokenization create-collection ./create-collection.json \
  --from mykey --chain-id bitbadges-1 --node https://lcd.bitbadges.io:443

# Universal update (can modify any field)
bitbadgeschaind tx tokenization universal-update-collection ./update.json \
  --from mykey --chain-id bitbadges-1 --node https://lcd.bitbadges.io:443

# Update collection
bitbadgeschaind tx tokenization update-collection ./update.json \
  --from mykey --chain-id bitbadges-1 --node https://lcd.bitbadges.io:443

# Delete collection
bitbadgeschaind tx tokenization delete-collection 1 \
  --from mykey --chain-id bitbadges-1 --node https://lcd.bitbadges.io:443
```

### Transfers

```bash
bitbadgeschaind tx tokenization transfer-tokens ./transfer.json \
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
bitbadgeschaind tx tokenization set-incoming-approval 1 ./approval.json --from mykey ...
bitbadgeschaind tx tokenization set-outgoing-approval 1 ./approval.json --from mykey ...

# Delete approvals
bitbadgeschaind tx tokenization delete-incoming-approval 1 my-approval-id --from mykey ...
bitbadgeschaind tx tokenization delete-outgoing-approval 1 my-approval-id --from mykey ...

# Update all user approvals at once
bitbadgeschaind tx tokenization update-user-approved-transfers ./approvals.json --from mykey ...

# Purge expired approvals
bitbadgeschaind tx tokenization purge-approvals 1 true "" false '[]' --from mykey ...
```

### Metadata and Configuration

```bash
bitbadgeschaind tx tokenization set-setcollectionmetadata ./metadata.json --from mykey ...
bitbadgeschaind tx tokenization set-settokenmetadata ./metadata.json --from mykey ...
bitbadgeschaind tx tokenization set-setcustomdata ./data.json --from mykey ...
bitbadgeschaind tx tokenization set-setstandards ./standards.json --from mykey ...
bitbadgeschaind tx tokenization set-setcollectionapprovals ./approvals.json --from mykey ...
bitbadgeschaind tx tokenization set-valid-token-ids ./tokenids.json --from mykey ...
bitbadgeschaind tx tokenization set-manager ./manager.json --from mykey ...
bitbadgeschaind tx tokenization set-setisarchived ./archived.json --from mykey ...
```

### Dynamic Stores

```bash
bitbadgeschaind tx tokenization create-dynamic-store true --from mykey ...
bitbadgeschaind tx tokenization update-dynamic-store 1 false true --from mykey ...
bitbadgeschaind tx tokenization set-dynamic-store-value 1 bb1... true --from mykey ...
bitbadgeschaind tx tokenization delete-dynamic-store 1 --from mykey ...
```

### Address Lists

```bash
bitbadgeschaind tx tokenization create-address-lists ./lists.json --from mykey ...
```

## Query Commands

```bash
# Collection details
bitbadgeschaind query tokenization collection 1

# Balance for an address
bitbadgeschaind query tokenization balance 1 bb1...

# Collection statistics
bitbadgeschaind query tokenization get-collection-stats 1

# Module parameters
bitbadgeschaind query tokenization params

# Dynamic store queries
bitbadgeschaind query tokenization get-dynamic-store
bitbadgeschaind query tokenization get-dynamic-store-value
```

All query commands support `--node` to specify which node to query and `--output json` for JSON output.

## JSON Input

Most transaction commands accept JSON either inline or from a file path:

```bash
# From a file
bitbadgeschaind tx tokenization create-collection ./my-collection.json --from mykey ...

# Inline JSON
bitbadgeschaind tx tokenization create-collection '{"collectionMetadataTimeline": [...]}' --from mykey ...
```

Using files is recommended for complex transactions. The JSON schema for each message matches the protobuf definitions documented in the [Messages](../../x-tokenization/messages/README.md) section.
