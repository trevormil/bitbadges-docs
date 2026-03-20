# Chain CLI (bitbadgeschaind)

The BitBadges chain ships a standard Cosmos SDK binary called `bitbadgeschaind`. You can use it for key management, transaction signing, querying, and broadcasting without writing any code.

## Installation

Build from source (requires Go 1.22+):

```bash
git clone https://github.com/BitBadges/bitbadgeschain.git
cd bitbadgeschain
make install
```

After installation, verify:

```bash
bitbadgeschaind version
```

## Key Management

BitBadges uses Ethereum-compatible keys (`eth_secp256k1`). The default keyring backend is `test` (unencrypted, for development). For production, use `--keyring-backend os` or `--keyring-backend file`.

### Create a New Key

```bash
bitbadgeschaind keys add mykey
```

This generates a new key and outputs the address and mnemonic. **Save the mnemonic securely.**

### Recover from Mnemonic

```bash
bitbadgeschaind keys add mykey --recover
```

You will be prompted to enter the mnemonic.

### List Keys

```bash
bitbadgeschaind keys list
```

### Show Key Details

```bash
bitbadgeschaind keys show mykey
```

Add `--bech val` to show the validator address or `--bech cons` for the consensus address.

### Export / Import

```bash
# Export (outputs armor-encrypted private key)
bitbadgeschaind keys export mykey

# Import from exported file
bitbadgeschaind keys import mykey keyfile.armor
```

### Delete a Key

```bash
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

## Tokenization Module Commands

The `tokenization` module handles collections, tokens, transfers, approvals, and dynamic stores. Most commands accept a JSON argument that can be provided inline or as a file path.

### Create a Collection

```bash
bitbadgeschaind tx tokenization create-collection ./create-collection.json \
  --from mykey --chain-id bitbadges-1 --node https://lcd.bitbadges.io:443
```

The JSON file should contain the fields for `MsgCreateCollection`. See [MsgCreateCollection](../../../x-tokenization/messages/msg-create-collection.md) for the full schema.

### Universal Update Collection

The most comprehensive update command -- can modify any collection field in a single transaction:

```bash
bitbadgeschaind tx tokenization universal-update-collection ./update.json \
  --from mykey --chain-id bitbadges-1 --node https://lcd.bitbadges.io:443
```

See [MsgUniversalUpdateCollection](../../../x-tokenization/messages/msg-universal-update-collection.md) for the full schema.

### Update Collection

```bash
bitbadgeschaind tx tokenization update-collection ./update.json \
  --from mykey --chain-id bitbadges-1 --node https://lcd.bitbadges.io:443
```

### Delete Collection

```bash
bitbadgeschaind tx tokenization delete-collection 1 \
  --from mykey --chain-id bitbadges-1 --node https://lcd.bitbadges.io:443
```

### Transfer Tokens

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
          "badgeIds": [{ "start": "1", "end": "1" }],
          "ownershipTimes": [{ "start": "1", "end": "18446744073709551615" }]
        }
      ]
    }
  ]
}
```

See [MsgTransferTokens](../../../x-tokenization/messages/msg-transfer-tokens.md) for the full schema.

### Approval Management

```bash
# Set incoming approval for a collection
bitbadgeschaind tx tokenization set-incoming-approval 1 ./approval.json \
  --from mykey --chain-id bitbadges-1 --node https://lcd.bitbadges.io:443

# Set outgoing approval
bitbadgeschaind tx tokenization set-outgoing-approval 1 ./approval.json \
  --from mykey --chain-id bitbadges-1 --node https://lcd.bitbadges.io:443

# Delete approvals
bitbadgeschaind tx tokenization delete-incoming-approval 1 my-approval-id \
  --from mykey --chain-id bitbadges-1 --node https://lcd.bitbadges.io:443

bitbadgeschaind tx tokenization delete-outgoing-approval 1 my-approval-id \
  --from mykey --chain-id bitbadges-1 --node https://lcd.bitbadges.io:443

# Update all user approvals at once
bitbadgeschaind tx tokenization update-user-approved-transfers ./approvals.json \
  --from mykey --chain-id bitbadges-1 --node https://lcd.bitbadges.io:443

# Purge expired approvals
bitbadgeschaind tx tokenization purge-approvals 1 true "" false '[]' \
  --from mykey --chain-id bitbadges-1 --node https://lcd.bitbadges.io:443
```

### Collection Metadata and Configuration

Each of these accepts a JSON argument (inline or file path):

```bash
# Set collection-level metadata
bitbadgeschaind tx tokenization set-setcollectionmetadata ./metadata.json --from mykey ...

# Set token-level metadata
bitbadgeschaind tx tokenization set-settokenmetadata ./metadata.json --from mykey ...

# Set custom data
bitbadgeschaind tx tokenization set-setcustomdata ./data.json --from mykey ...

# Set standards
bitbadgeschaind tx tokenization set-setstandards ./standards.json --from mykey ...

# Set collection approvals
bitbadgeschaind tx tokenization set-setcollectionapprovals ./approvals.json --from mykey ...

# Set valid token IDs
bitbadgeschaind tx tokenization set-valid-token-ids ./tokenids.json --from mykey ...

# Set manager
bitbadgeschaind tx tokenization set-manager ./manager.json --from mykey ...

# Archive / unarchive
bitbadgeschaind tx tokenization set-setisarchived ./archived.json --from mykey ...
```

### Dynamic Stores

Dynamic stores are on-chain boolean mappings (allowlists/blocklists) that can be referenced in approval criteria.

```bash
# Create a dynamic store (default value: true or false)
bitbadgeschaind tx tokenization create-dynamic-store true --from mykey ...

# Update a store
bitbadgeschaind tx tokenization update-dynamic-store 1 false true --from mykey ...

# Set a specific address value
bitbadgeschaind tx tokenization set-dynamic-store-value 1 bb1... true --from mykey ...

# Delete a store
bitbadgeschaind tx tokenization delete-dynamic-store 1 --from mykey ...
```

### Address Lists

```bash
bitbadgeschaind tx tokenization create-address-lists ./lists.json --from mykey ...
```

### Voting

```bash
# Cast a vote within an approval-based governance proposal
bitbadgeschaind tx tokenization cast-vote 1 collection bb1... my-approval-id proposal-1 100 \
  --from mykey ...
```

## Query Commands

```bash
# Get collection details
bitbadgeschaind query tokenization collection 1

# Get balance for an address
bitbadgeschaind query tokenization balance 1 bb1...

# Get collection statistics
bitbadgeschaind query tokenization get-collection-stats 1

# Get module parameters
bitbadgeschaind query tokenization params

# Dynamic store queries
bitbadgeschaind query tokenization get-dynamic-store
bitbadgeschaind query tokenization get-dynamic-store-value
```

All query commands support `--node` to specify which node to query and `--output json` for JSON output.

## JSON Input

Most transaction commands accept JSON either inline or from a file path. The CLI automatically detects which format you are using:

```bash
# From a file
bitbadgeschaind tx tokenization create-collection ./my-collection.json --from mykey ...

# Inline JSON
bitbadgeschaind tx tokenization create-collection '{"collectionMetadataTimeline": [...]}' --from mykey ...
```

Using files is recommended for complex transactions. The JSON schema for each message matches the protobuf definitions documented in the [Messages](../../../x-tokenization/messages/README.md) section.

## For Developers (SDK Alternative)

If you prefer programmatic signing over the CLI, use the [BitBadgesSigningClient](./signing-client.md) from `bitbadgesjs-sdk`. It provides TypeScript functions for all transaction types with automatic gas estimation, sequence management, and broadcasting.

```typescript
import { BitBadgesSigningClient, GenericCosmosAdapter } from 'bitbadgesjs-sdk';

const adapter = await GenericCosmosAdapter.fromMnemonic('your mnemonic here', 'bitbadges-1');
const client = new BitBadgesSigningClient({ adapter });
const result = await client.signAndBroadcast([msg]);
```
