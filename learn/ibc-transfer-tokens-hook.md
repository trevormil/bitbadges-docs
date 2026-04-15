# IBC Transfer Tokens Hook

The `transfer_tokens` IBC hook allows incoming IBC token transfers to automatically trigger BitBadges token transfers (minting, distributing, etc.) in a single atomic cross-chain transaction. This is a powerful primitive for cross-chain minting, airdrops, purchases, and more.

## Overview

When an IBC transfer arrives with a `transfer_tokens` memo, the module:

1. Receives the IBC token transfer (x/bank assets)
2. Derives an **intermediate sender** address from the IBC channel + original sender
3. Parses the `transfer_tokens` hook data from the memo
4. Executes a `MsgTransferTokens` on the specified collection **as the intermediate sender** (the intermediate sender is the `creator` of the transaction)
5. Returns an error acknowledgement if the transfer fails, rolling back the entire IBC transfer

This means you can, for example, send ATOM from Osmosis and atomically mint a badge on BitBadges in a single transaction.

> **Important:** The `creator` of the resulting `MsgTransferTokens` is always the **intermediate sender** — not the original sender on the source chain. Your collection's approval rules must authorize this intermediate address. The module automatically tries to initiate the transfer as the intermediate sender, but your collection approvals must still permit the transfer (e.g. a mint approval that allows the intermediate sender as the initiator).

## Memo Format

The hook data is specified in the IBC transfer memo as JSON:

```json
{
  "transfer_tokens": {
    "collection_id": "123",
    "transfers": [
      {
        "from": "Mint",
        "to_addresses": ["bb1..."],
        "balances": [
          {
            "amount": "1",
            "badge_ids": [{ "start": "1", "end": "1" }],
            "ownership_times": [{ "start": "1", "end": "18446744073709551615" }]
          }
        ],
        "prioritized_approvals": [],
        "only_check_prioritized_collection_approvals": false,
        "only_check_prioritized_incoming_approvals": false,
        "only_check_prioritized_outgoing_approvals": false
      }
    ],
    "fail_on_error": true,
    "recover_address": ""
  }
}
```

> **Note:** The memo JSON uses **snake_case** keys (e.g. `to_addresses`, `badge_ids`, `ownership_times`). These are automatically converted to camelCase internally for protobuf processing.

## Fields

### TransferTokensAction

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `collection_id` | string | Yes | The collection ID to execute transfers on |
| `transfers` | Transfer[] | Yes | Array of transfers to execute (same format as `MsgTransferTokens`) |
| `fail_on_error` | bool | Yes | If `true`, fail the entire IBC transfer on error. If `false`, fall back to `recover_address` |
| `recover_address` | string | Conditional | Required if `fail_on_error` is `false`. Address to send IBC tokens to on failure |

### Transfer Object

The `transfers` array uses the same structure as [MsgTransferTokens](../x-tokenization/messages/msg-transfer-tokens.md) transfers, but with snake_case JSON keys:

| Field | Type | Description |
|-------|------|-------------|
| `from` | string | Sender address (use `"Mint"` for minting) |
| `to_addresses` | string[] | Recipient addresses |
| `balances` | Balance[] | Token balances to transfer |
| `prioritized_approvals` | ApprovalIdentifierDetails[] | Priority approval identifiers |
| `merkle_proofs` | MerkleProof[] | Merkle challenge solutions (if applicable) |
| `eth_signature_proofs` | ETHSignatureProof[] | ETH signature proofs (if applicable) |
| `memo` | string | Transfer memo |
| `only_check_prioritized_collection_approvals` | bool | Only check collection-level approvals |
| `only_check_prioritized_incoming_approvals` | bool | Only check incoming approvals |
| `only_check_prioritized_outgoing_approvals` | bool | Only check outgoing approvals |

## Error Handling

### fail_on_error: true (Default)

If the token transfer fails, the entire IBC transfer is rolled back. The sender receives their tokens back on the source chain via the standard IBC error acknowledgement flow.

### fail_on_error: false (With Recovery)

If the token transfer fails, the IBC tokens are sent to the `recover_address` instead. The IBC transfer itself succeeds (success acknowledgement), but the token transfer does not execute. This is useful when you don't want the sender to have to recover tokens on the source chain.

```json
{
  "transfer_tokens": {
    "collection_id": "123",
    "transfers": [{ "..." }],
    "fail_on_error": false,
    "recover_address": "bb1recoveryaddress..."
  }
}
```

## Intermediate Sender (Transaction Creator)

The `creator` of the `MsgTransferTokens` is **not** the original sender on the source chain. Instead, the module derives a deterministic **intermediate sender** address from the IBC channel and original sender. This is important because:

- The intermediate sender is the `creator` of the executed `MsgTransferTokens`
- It receives the IBC tokens on BitBadges
- It is automatically granted auto-approval flags on the collection
- **Your collection approvals must authorize this address** — for example, if you want cross-chain minting, your mint approval's `initiatedByList` must include the intermediate sender (or use a wildcard list)

You can derive this address ahead of time to configure your approvals:

```typescript
import { deriveIntermediateSender } from 'bitbadges';

// Derive the intermediate sender for a given channel + source address
const creator = deriveIntermediateSender('channel-0', 'osmo1...', 'bb');
// Use this address in your collection's approval initiatedByList
```

## Using Minimal-Value Triggers

The `transfer_tokens` hook rides on standard IBC / ICS-20 transfer rails. There is **no restriction on what denomination or amount is transferred** — the hook simply needs an IBC transfer to carry the memo. This means you can use a negligible amount of any ICS-20 asset as a pure trigger:

```json
// IBC MsgTransfer fields:
// token: { denom: "ubadge", amount: "1" }   ← 0.000001 BADGE, effectively free
// memo: { "transfer_tokens": { ... } }
```

The IBC token being transferred does not need to relate to the token transfer being executed. For example:

- Send **1 ubadge** (~zero value) to trigger a mint of an NFT badge
- Send **1 uatom** to trigger a badge distribution
- Send **any ICS-20 asset** available on the source chain

This makes the hook useful as a **cross-chain trigger mechanism** where the IBC transfer itself is just a vehicle for the memo, not a meaningful value transfer. The actual business logic lives entirely in the `transfers` array and the collection's approval rules.

> **Tip:** If you're building a system where the IBC transfer is purely a trigger, consider using the cheapest available denomination on the source chain. The transferred tokens will end up with the intermediate sender (or `recover_address` on failure).

## Mutual Exclusivity

An IBC transfer memo cannot contain both `swap_and_action` and `transfer_tokens` hooks. Only one hook type is allowed per transfer. If both are present, the transfer will fail.

## Atomicity

All operations execute atomically using a cached context:

- If the IBC transfer succeeds but the token transfer fails (and `fail_on_error` is `true`), everything is rolled back
- State changes are only committed if all operations succeed
- When `fail_on_error` is `false`, the fallback to `recover_address` is also atomic

## Use Cases

- **Cross-chain minting**: Send tokens from another chain to mint badges on BitBadges
- **Cross-chain purchases**: Pay with IBC tokens and receive badges atomically
- **Cross-chain airdrops**: Distribute badges triggered by IBC transfers
- **Bridge-and-transfer**: Receive IBC tokens and redistribute BitBadges tokens in one step

## Limitations

- **Memo size**: Maximum 64KB for the IBC transfer memo (DoS prevention)
- **Native assets**: x/tokenization assets cannot be IBC transferred — only x/bank assets can trigger this hook
- **Collection must exist**: The target collection must already exist on BitBadges
- **Approval rules apply**: Standard collection approval rules apply to the transfer — the intermediate sender must be authorized

## See Also

- [MsgTransferTokens](../x-tokenization/messages/msg-transfer-tokens.md) — Transfer message reference
- [Swap and Action Hook](../x-custom-ibc-hooks/overview.md) — The other IBC hook type
- [IBC Backed Minting](ibc-backed-minting.md) — Minting tokens backed by IBC assets
