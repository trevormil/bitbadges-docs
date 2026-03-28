# 🔍 Transaction Interpreter

The `interpretTransaction` function generates a thorough, human-readable markdown explanation of a raw `MsgUniversalUpdateCollection` transaction body. It is the transaction-level counterpart to `interpretCollection` — while `interpretCollection` works with fully hydrated `BitBadgesCollection` objects fetched from the API, `interpretTransaction` works directly with the raw JSON message value before or after broadcast.

This is useful for:

- **Pre-sign review** — show users exactly what a transaction will do before they sign it
- **AI builder post-build review** — let agents verify that a generated transaction matches intent
- **Audit trails** — produce a permanent, readable record of what each transaction configured

## Usage

```typescript
import { interpretTransaction } from 'bitbadgesjs-sdk';

// For a new collection creation transaction
const explanation = interpretTransaction(txBody);
console.log(explanation);
```

### Update Transactions

When interpreting an update to an existing collection, pass `isUpdate: true` and the list of update flags. The output will only describe the sections relevant to the fields being changed, rather than the entire collection.

```typescript
import { interpretTransaction } from 'bitbadgesjs-sdk';

const updateExplanation = interpretTransaction(
  txBody,
  true, // isUpdate
  ['updateCollectionApprovals', 'updatePermissions']
);
```

### Multi-Message Transactions

If your transaction bundles multiple messages (for example, a collection update followed by token transfers), pass the full messages array as the fourth argument. The interpreter will include a Token Transfers section describing any `MsgTransferTokens` messages.

```typescript
const explanation = interpretTransaction(txBody, false, [], messages);
```

## Parameters

| Parameter            | Type                    | Default | Description                                                                                          |
| -------------------- | ----------------------- | ------- | ---------------------------------------------------------------------------------------------------- |
| `txBody`             | `Record<string, any>`   | —       | The raw `value` object from `MsgUniversalUpdateCollection`.                                          |
| `isUpdate`           | `boolean`               | `false` | Set to `true` when interpreting an update to an existing collection.                                 |
| `activeUpdateFlags`  | `string[]`              | `[]`    | Which fields are being updated. Controls which sections appear in the output.                        |
| `messages`           | `any[]`                 | —       | Optional full messages array. If provided, bundled `MsgTransferTokens` messages are also explained.  |

## Update Flags

The following flags control which sections are included in the output when `isUpdate` is `true`:

| Flag                            | Sections Included                      |
| ------------------------------- | -------------------------------------- |
| `updateCollectionApprovals`     | Minting and transfer rules             |
| `updateCollectionMetadata`      | Collection overview                    |
| `updateTokenMetadata`           | Collection overview                    |
| `updateCollectionPermissions`   | Permissions                            |
| `updateValidTokenIds`           | Collection overview                    |
| `updateManager`                 | Key reference, custom data             |
| `updateStandards`               | Standards                              |
| `updateCustomData`              | Key reference, custom data             |
| `updateDefaultBalances`         | Default user balances                  |
| `updateInvariants`              | Backing and paths, invariants          |
| `updateIsArchived`              | Collection overview                    |
| `updateAliasPaths`              | Backing and paths                      |
| `updateCosmosCoinWrapperPaths`  | Backing and paths                      |

## What's Included in the Output

The generated markdown report contains these sections (all included for creation; only relevant sections for updates):

- **Transaction Summary** — whether this is a create or update, and what is being changed
- **Collection Overview** — name, description, token type, token ID range, supply cap, standards, per-token metadata, custom data, archived status
- **Token Backing and Cross-Chain** — IBC backing configuration, alias paths, Cosmos coin wrapper paths
- **Invariants** — on-chain guarantees like no forceful transfers and max supply enforcement
- **Standards** — declared standards and what they mean for wallets and tooling
- **Key Reference** — manager address, collection ID, creator
- **How Tokens Are Created** — every mint approval explained in detail, with practical guidance on how to claim or mint
- **Transfer and Approval Rules** — every transfer approval explained, soulbound detection, withdrawal rules for asset-backed tokens
- **Permissions** — what the manager can and cannot change
- **Default User Balances** — starting balances and auto-approve settings
- **Token Transfers** — any bundled `MsgTransferTokens` messages (multi-message transactions only)

## Differences from interpretCollection

| Aspect                | `interpretCollection`                        | `interpretTransaction`                          |
| --------------------- | -------------------------------------------- | ----------------------------------------------- |
| Input                 | Hydrated `BitBadgesCollection` from the API  | Raw `MsgUniversalUpdateCollection` JSON value   |
| Data types            | Uses `BigInt` values                         | Uses strings and numbers                        |
| Claims                | Includes claim plugin details                | No claim data (not part of the transaction)      |
| Update mode           | N/A                                          | Can scope output to only changed fields          |
| Multi-message         | N/A                                          | Can explain bundled transfer messages            |
| When to use           | Explaining an existing on-chain collection   | Reviewing a transaction before or after signing  |

## Also Available Via

- **MCP**: The `explain_collection` tool uses `interpretCollection` under the hood; `interpretTransaction` can be called directly in SDK-based workflows
- **AI Builders**: Used internally for post-build transaction review
