## Manager

The **manager** is the central authority for a collection, controlling all administrative operations. The manager executes actions according to permission rules defined in `collectionPermissions`.

```typescript
const collection: TokenCollection<bigint> = {
    manager: 'bb1kj9kt5y64n5a8677fhjqnmcc24ht2vy9atmdls',
    collectionPermissions: {
        // Permissions define what the manager can do
    },
    // ... other collection fields
};
```

### Setting Initial Manager

During collection creation:

```typescript
const collection: TokenCollection<bigint> = {
    creator: 'bb1alice...',
    manager: 'bb1alice...',
    collectionPermissions: {
        canUpdateManager: [
            {
                permanentlyPermittedTimes: [
                    { start: 1n, end: 18446744073709551615n },
                ],
                permanentlyForbiddenTimes: [],
            },
        ],
        // ... other permission fields
    },
    // ... other collection fields
};
```

### No Manager

If you don't want a manager, set the manager to an empty string. Permission values don't matter when there's no manager:

```typescript
const manager: string = '';
```

## Manager Capabilities

The manager can execute administrative actions according to permissions:

-   Updating collection metadata
-   Updating token metadata
-   Updating transferability (collection approvals)
-   Updating valid token IDs
-   Archiving the collection
-   Deleting the collection
-   Updating manager
-   And more...

All permissions are defined on-chain with time-based configuration, allowing permissions to change over time.

You may also implement off-chain permissions for the manager, but this is up to you and your use case.

```typescript
// Manager with full control (soft-enabled)
const collectionPermissions: CollectionPermissions<bigint> = {
    canDeleteCollection: [],
    canArchiveCollection: [],
    canUpdateStandards: [],
    canUpdateCustomData: [],
    canUpdateManager: [],
    canUpdateCollectionMetadata: [],
    canUpdateTokenMetadata: [],
    canUpdateCollectionApprovals: [],
    canUpdateValidTokenIds: [],
    // ... other permission fields
};
```

## User Permissions

User permissions control what individual users can do with their own badges and transfer approvals. Unlike manager permissions which control collection-wide administrative actions, user permissions control user-specific actions like updating auto-approve settings and managing their own incoming/outgoing transfer approvals.

These are almost always never needed unless in advanced situations. Typically, you just leave these soft-enabled (empty arrays) for all. These are only really needed in advanced situations where you want to lock down a user's ability to update their own approvals, such as escrow accounts.

```typescript
const userPermissions: UserPermissions<bigint> = {
    canUpdateAutoApproveSelfInitiatedOutgoingTransfers: [],
    canUpdateAutoApproveSelfInitiatedIncomingTransfers: [],
    canUpdateAutoApproveAllIncomingTransfers: [],
    canUpdateOutgoingApprovals: [],
    canUpdateIncomingApprovals: [],
};
```

## Permissions

Permissions control which actions can be performed and when those actions can be executed. The manager executes administrative actions according to these permission rules.

## Permission States

Permissions have three states:

| State                     | Description                    | Behavior                                                   |
| ------------------------- | ------------------------------ | ---------------------------------------------------------- |
| **Permanently Permitted** | Action ALWAYS allowed (frozen) | Can be executed                                            |
| **Permanently Forbidden** | Action ALWAYS blocked (frozen) | Cannot be executed                                         |
| **Neutral**               | Not specified                  | **Allowed by default now, but can change state in future** |

**Important:** Once set to permanently permitted or forbidden, it can never changed. This is by design to act as a check and balance enforced on-chain.

```typescript
// Lock collection deletion forever
const collectionPermissions: CollectionPermissions<bigint> = {
    canDeleteCollection: [
        {
            permanentlyPermittedTimes: [],
            permanentlyForbiddenTimes: [
                { start: 1n, end: 18446744073709551615n },
            ],
        },
    ],
    // ... other permission fields
};

// Soft-enabled (default behavior - allowed but can be changed)
const collectionPermissions: CollectionPermissions<bigint> = {
    canDeleteCollection: [], // Empty = allowed by default
    // ... other permission fields
};
```

## First Match Policy

Permissions are evaluated as a linear array where each element has criteria and time controls. Only the **first matching element** is applied - all subsequent matches are ignored.

**Key Rules:**

-   **First Match Only**: Only the first element that matches all criteria is used
-   **Deterministic State**: Each criteria combination has exactly one permission state
-   **No Overlap**: Times cannot be in both `permanentlyPermittedTimes` and `permanentlyForbiddenTimes`
-   **Order Matters**: Array order affects which permissions are applied

**Example: Action Permissions**

```typescript
const collectionPermissions: CollectionPermissions<bigint> = {
    canUpdateCollectionMetadata: [
        {
            permanentlyPermittedTimes: [],
            permanentlyForbiddenTimes: [
                { start: 1n, end: 18446744073709551615n },
            ],
        },
    ],
};
```

**Result:**

-   Collection metadata updates: **Forbidden** (locked forever)

## Satisfying Criteria

All criteria in a permission element must match for it to be applied. Partial matches are ignored.

**Example: Token Metadata Permissions**

```typescript
const collectionPermissions: CollectionPermissions<bigint> = {
    canUpdateTokenMetadata: [
        {
            tokenIds: [{ start: 1n, end: 10n }],
            permanentlyPermittedTimes: [
                { start: 1n, end: 18446744073709551615n },
            ],
            permanentlyForbiddenTimes: [],
        },
    ],
};
```

**This permission only covers:**

-   Token IDs 1-10

**It does NOT cover:**

-   Token ID 11

These combinations are **unhandled** and **allowed by default** since they do not match the permission criteria.

## Brute Force Pattern

To lock specific criteria, you must specify the target and set all other criteria to maximum ranges (brute forcing all other options).

**Example: Lock Token IDs 1-10**

```typescript
const collectionPermissions: CollectionPermissions<bigint> = {
    canUpdateCollectionApprovals: [
        {
            fromListId: 'All',
            toListId: 'All',
            initiatedByListId: 'All',
            tokenIds: [{ start: 1n, end: 10n }],
            transferTimes: [{ start: 1n, end: 18446744073709551615n }],
            ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
            approvalId: 'All',
            permanentlyPermittedTimes: [],
            permanentlyForbiddenTimes: [
                { start: 1n, end: 18446744073709551615n },
            ],
        },
    ],
};
```

## Permission Types

There are **four types** of permissions, each with different criteria:

| Type                            | Criteria                                       | Examples                                                                                                                                      |
| ------------------------------- | ---------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| **Action Permissions**          | Time control only                              | `canDeleteCollection`, `canUpdateCollectionMetadata`, `canUpdateStandards`, `canUpdateCustomData`, `canUpdateManager`, `canArchiveCollection` |
| **Token ID Action Permissions** | Token IDs + time control                       | `canUpdateValidTokenIds`, `canUpdateTokenMetadata`                                                                                            |
| **Approval Permissions**        | Transfer criteria + approval ID + time control | `canUpdateCollectionApprovals`, `canUpdateIncomingApprovals`                                                                                  |

### Action Permissions

Simple time-based permissions with no additional criteria. Control when actions can be executed based solely on time.

**Collection Actions:**

-   `canDeleteCollection` - Delete entire collection
-   `canUpdateCollectionMetadata` - Update collection metadata
-   `canUpdateStandards` - Update standards
-   `canUpdateCustomData` - Update custom data
-   `canUpdateManager` - Update manager
-   `canArchiveCollection` - Archive/unarchive collection

**User Actions:**

-   `canUpdateAutoApproveSelfInitiatedOutgoingTransfers` - Auto-approve outgoing transfers
-   `canUpdateAutoApproveSelfInitiatedIncomingTransfers` - Auto-approve incoming transfers
-   `canUpdateAutoApproveAllIncomingTransfers` - Auto-approve all incoming transfers

**Logic:**

```
For each action request:
    Check if current time is in permanentlyPermittedTimes
        → If yes: ALLOW
        → If no: Check if current time is in permanentlyForbiddenTimes
            → If yes: DENY
            → If no: ALLOW (neutral state)
```

**Examples:**

```typescript
// Lock collection deletion forever
const collectionPermissions: CollectionPermissions<bigint> = {
    canDeleteCollection: [
        {
            permanentlyPermittedTimes: [],
            permanentlyForbiddenTimes: [
                { start: 1n, end: 18446744073709551615n },
            ],
        },
    ],
};

// Allow collection deletion only during specific period
const collectionPermissions: CollectionPermissions<bigint> = {
    canDeleteCollection: [
        {
            permanentlyPermittedTimes: [
                { start: 1704067200000n, end: 1735689600000n },
            ],
            permanentlyForbiddenTimes: [],
        },
    ],
};

// Default behavior (no restrictions)
const collectionPermissions: CollectionPermissions<bigint> = {
    canDeleteCollection: [],
};
```

### Token ID Action Permissions

Control when token ID-based fields can be updated. These permissions match on `tokenIds` to determine which token IDs can be updated.

**Token ID Action Permissions:**

-   `canUpdateValidTokenIds` - Update valid token IDs
-   `canUpdateTokenMetadata` - Update token metadata

**Logic:**

```
For each token ID update request:
    Check if token ID matches any tokenIds criteria
        → If no match: ALLOW (neutral state)
        → If match: Check if current time is in permanentlyPermittedTimes
            → If yes: ALLOW
            → If no: Check if current time is in permanentlyForbiddenTimes
                → If yes: DENY
                → If no: ALLOW (neutral state)
```

**Examples:**

```typescript
// Lock token metadata for specific tokens forever
const collectionPermissions: CollectionPermissions<bigint> = {
    canUpdateTokenMetadata: [
        {
            tokenIds: [{ start: 1n, end: 100n }],
            permanentlyPermittedTimes: [],
            permanentlyForbiddenTimes: [
                { start: 1n, end: 18446744073709551615n },
            ],
        },
    ],
};

// Allow token metadata updates only during specific period
const collectionPermissions: CollectionPermissions<bigint> = {
    canUpdateTokenMetadata: [
        {
            tokenIds: [{ start: 1n, end: 100n }],
            permanentlyPermittedTimes: [
                { start: 1704067200000n, end: 1735689600000n },
            ],
            permanentlyForbiddenTimes: [],
        },
    ],
};
```

### Token ID Action Permissions

Control which token-specific actions can be performed based on token IDs.

**Available Actions:**

-   `canUpdateValidTokenIds` - Update valid token ID ranges

**Logic:**

```
For each token action request:
    Check if token ID matches any tokenIds criteria
        → If no match: ALLOW (neutral state)
        → If match: Check if current time is in permanentlyPermittedTimes
            → If yes: ALLOW
            → If no: Check if current time is in permanentlyForbiddenTimes
                → If yes: DENY
                → If no: ALLOW (neutral state)
```

**Examples:**

```typescript
// Lock all token ID updates
const collectionPermissions: CollectionPermissions<bigint> = {
    canUpdateValidTokenIds: [
        {
            tokenIds: [{ start: 1n, end: 18446744073709551615n }],
            permanentlyPermittedTimes: [],
            permanentlyForbiddenTimes: [
                { start: 1n, end: 18446744073709551615n },
            ],
        },
    ],
};

// Lock specific ID range
const collectionPermissions: CollectionPermissions<bigint> = {
    canUpdateValidTokenIds: [
        {
            tokenIds: [{ start: 1n, end: 100n }],
            permanentlyPermittedTimes: [],
            permanentlyForbiddenTimes: [
                { start: 1n, end: 18446744073709551615n },
            ],
        },
    ],
};

// Allow future token IDs only
const collectionPermissions: CollectionPermissions<bigint> = {
    canUpdateValidTokenIds: [
        {
            tokenIds: [{ start: 101n, end: 18446744073709551615n }],
            permanentlyPermittedTimes: [
                { start: 1n, end: 18446744073709551615n },
            ],
            permanentlyForbiddenTimes: [],
        },
    ],
};
```

### Approval Permissions

Control when transfer approvals can be updated, allowing you to freeze specific transfer rules. These permissions match on transfer criteria (from, to, initiatedBy, transferTimes, tokenIds, ownershipTimes) and approval ID.

**Available Actions:**

-   `canUpdateCollectionApprovals` - Control collection-level approvals
-   `canUpdateIncomingApprovals` - Control incoming transfer approvals (user)
-   `canUpdateOutgoingApprovals` - Control outgoing transfer approvals (user)

**Note**: For user approvals, `fromListId` and `toListId` are automatically set:

-   **Incoming**: `toListId` is hardcoded to the user's address
-   **Outgoing**: `fromListId` is hardcoded to the user's address

**Logic:**

```
For each approval update request:
    Check if approval criteria match (from, to, initiatedBy, transferTimes, tokenIds, ownershipTimes, approvalId)
        → If no match: ALLOW (neutral state)
        → If match: Check if current time is in permanentlyPermittedTimes
            → If yes: ALLOW
            → If no: Check if current time is in permanentlyForbiddenTimes
                → If yes: DENY
                → If no: ALLOW (neutral state)
```

**Approval Tuple:**
An approval tuple consists of: `(from, to, initiatedBy, tokenIds, transferTimes, ownershipTimes, approvalId)`

**Examples:**

```typescript
// Lock specific token ID range
const collectionPermissions: CollectionPermissions<bigint> = {
    canUpdateCollectionApprovals: [
        {
            fromListId: 'All',
            toListId: 'All',
            initiatedByListId: 'All',
            tokenIds: [{ start: 1n, end: 100n }],
            transferTimes: [{ start: 1n, end: 18446744073709551615n }],
            ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
            approvalId: 'All',
            permanentlyPermittedTimes: [],
            permanentlyForbiddenTimes: [
                { start: 1n, end: 18446744073709551615n },
            ],
        },
    ],
};

// Lock specific approval ID
const collectionPermissions: CollectionPermissions<bigint> = {
    canUpdateCollectionApprovals: [
        {
            fromListId: 'All',
            toListId: 'All',
            initiatedByListId: 'All',
            tokenIds: [{ start: 1n, end: 18446744073709551615n }],
            transferTimes: [{ start: 1n, end: 18446744073709551615n }],
            ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
            approvalId: 'specific-approval-id',
            permanentlyPermittedTimes: [],
            permanentlyForbiddenTimes: [
                { start: 1n, end: 18446744073709551615n },
            ],
        },
    ],
};

// Complete freeze - lock all approvals
const collectionPermissions: CollectionPermissions<bigint> = {
    canUpdateCollectionApprovals: [
        {
            fromListId: 'All',
            toListId: 'All',
            initiatedByListId: 'All',
            tokenIds: [{ start: 1n, end: 18446744073709551615n }],
            transferTimes: [{ start: 1n, end: 18446744073709551615n }],
            ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
            approvalId: 'All',
            permanentlyPermittedTimes: [],
            permanentlyForbiddenTimes: [
                { start: 1n, end: 18446744073709551615n },
            ],
        },
    ],
};
```

**Protection Strategies:**

1. **Specific Approval Lock**: Lock a specific approval by its unique ID
2. **Range Lock with Overlap Protection**: Lock a token range AND all overlapping approvals
3. **Complete Freeze**: Lock all approvals for a collection
