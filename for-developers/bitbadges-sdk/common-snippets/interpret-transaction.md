# 🔍 Transaction Interpreter

`interpretTransaction` generates a human-readable markdown explanation of a raw `MsgUniversalUpdateCollection` transaction body — the transaction-level counterpart to `interpretCollection`.

## Usage

```typescript
import { interpretTransaction } from 'bitbadgesjs-sdk';

// New collection creation
const explanation = interpretTransaction(txBody);

// Update — only describes changed fields
const explanation = interpretTransaction(txBody, true, ['updateCollectionApprovals', 'updatePermissions']);

// Multi-message — also explains bundled MsgTransferTokens
const explanation = interpretTransaction(txBody, false, [], messages);
```

## Parameters

| Parameter           | Type                  | Default | Description                                                      |
| ------------------- | --------------------- | ------- | ---------------------------------------------------------------- |
| `txBody`            | `Record<string, any>` | —       | Raw `value` from `MsgUniversalUpdateCollection`                  |
| `isUpdate`          | `boolean`             | `false` | Scope output to only changed fields                              |
| `activeUpdateFlags` | `string[]`            | `[]`    | Which fields are being updated (e.g. `updateCollectionApprovals`) |
| `messages`          | `any[]`               | —       | Full messages array to explain bundled transfers                 |

## vs interpretCollection

| Aspect | `interpretCollection`                     | `interpretTransaction`                        |
| ------ | ----------------------------------------- | --------------------------------------------- |
| Input  | Hydrated `BitBadgesCollection` from API   | Raw `MsgUniversalUpdateCollection` JSON value |
| When   | Explaining an existing on-chain collection | Reviewing a transaction before/after signing |
| Claims | Includes claim plugin details             | No claim data (not in the transaction)        |
