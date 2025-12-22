# Transferability & Approvals

## Overview

Transferability in BitBadges is controlled through a hierarchical approval system with three levels:

<figure><img src="../../.gitbook/assets/image (166) (1).png" alt="Approval hierarchy diagram"><figcaption><p>Approval hierarchy: Collection → User (Incoming/Outgoing)</p></figcaption></figure>

### Approval Levels

| Level          | Description                              | Fields                                    |
| -------------- | ---------------------------------------- | ----------------------------------------- |
| **Collection** | Global rules for the entire collection   | All fields                                |
| **Incoming**   | User-specific rules for receiving tokens | `toList` = user's address, no overrides   |
| **Outgoing**   | User-specific rules for sending tokens   | `fromList` = user's address, no overrides |

**Key Rule**: A transfer must satisfy collection-level approvals AND (unless overridden) user-level incoming/outgoing approvals.

## Approval Structure

```typescript
interface CollectionApproval<T extends NumberType> {
    // Core Fields
    toListId: string; // Who can receive?
    fromListId: string; // Who can send?
    initiatedByListId: string; // Who can initiate?
    transferTimes: UintRange<T>[]; // When can transfer happen?
    tokenIds: UintRange<T>[]; // Which token IDs?
    ownershipTimes: UintRange<T>[]; // Which ownership times?
    approvalId: string; // Unique identifier

    // Version control (incremented on each update)
    version: T;

    // Optional Fields
    uri?: string; // Metadata link
    customData?: string; // Custom data
    approvalCriteria?: ApprovalCriteria<T>; // Additional restrictions
}
```

See [Approval Criteria](broken-reference/) for more details on the `approvalCriteria` field.

## Approval Value vs Permission

While the value may seem similar to the approval update permissions, the permission corresponds to the **updatability** of the approvals (i.e. `canUpdateCollectionApprovals`). The approvals themselves correspond to if a transfer is currently approved or not.

## The Six Core Fields

Every approval defines **Who? When? What?** through these fields:

| Field               | Type                             | Purpose                                 | Example                                            |
| ------------------- | -------------------------------- | --------------------------------------- | -------------------------------------------------- |
| `toListId`          | Address List ID                  | Who can receive tokens                  | `"All"`, `"Mint"`, `"bb1..."`                      |
| `fromListId`        | Address List ID                  | Who can send tokens                     | `"Mint"`, `"!Mint"`                                |
| `initiatedByListId` | Address List ID                  | Who can initiate transfer               | `"All"`, `"bb1..."`                                |
| `transferTimes`     | UintRange\[] (UNIX Milliseconds) | When transfer can occur                 | `[{start: "1691931600000", end: "1723554000000"}]` |
| `tokenIds`          | UintRange\[] (Token IDs)         | Which token IDs                         | `[{start: "1", end: "100"}]`                       |
| `ownershipTimes`    | UintRange\[] (UNIX Milliseconds) | Which ownership times to be transferred | `[{start: "1", end: "18446744073709551615"}]`      |

### Example Approval

```json
{
    "fromListId": "Mint",
    "toListId": "All",
    "initiatedByListId": "All",
    "transferTimes": [{ "start": "1691931600000", "end": "1723554000000" }],
    "tokenIds": [{ "start": "1", "end": "100" }],
    "ownershipTimes": [{ "start": "1", "end": "18446744073709551615" }],
    "approvalId": "mint-to-all"
}
```

**Translation**: Allow anyone to claim tokens 1-100 from the Mint address between Aug 13, 2023 and Aug 13, 2024.

## Approval Matching Process

### Step-by-Step Flow (High-Level)

```
Transfer Request
    ↓
Check Collection Approvals
    ↓
Match Found? ──No──→ TRANSFER DENIED
    ↓ Yes
Check Approval Criteria
    ↓
Criteria Met? ──No──→ TRANSFER DENIED
    ↓ Yes
Check User Approvals (where necessary)
    ↓
User Approvals Met? ──No──→ TRANSFER DENIED
    ↓ Yes
TRANSFER APPROVED
```

### Matching Logic

For optimal design, you should try to design transfers such that they only use specific approvals without the need for splitting. However, if needed, we split the transfer / approvals as fine-grained as we can to make it succeed. In other words, we deduct as much as possible from each approval as we iterate.

