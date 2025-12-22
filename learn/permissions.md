## Manager

The **manager** is the central authority for a collection, controlling all administrative operations. The manager executes actions according to permission rules defined in `collectionPermissions`.

```typescript
const collection: TokenCollection<bigint> = {
    managerTimeline: [
        {
            manager: 'bb1kj9kt5y64n5a8677fhjqnmcc24ht2vy9atmdls',
            timelineTimes: [{ start: 1n, end: 18446744073709551615n }],
        },
    ],
    collectionPermissions: {
        // Permissions define what the manager can do
    },
    // ... other collection fields
};
```

### Manager Timeline

Managers can change over time using timeline-based configuration:

```typescript
// Manager changes automatically on a specific date
const managerTimeline: ManagerTimeline<bigint>[] = [
    {
        timelineTimes: [{ start: 1n, end: 1672531199000n }],
        manager: 'bb1alice...',
    },
    {
        timelineTimes: [
            {
                start: 1672531200000n,
                end: 18446744073709551615n,
            },
        ],
        manager: 'bb1bob...',
    },
];
```

This transfers management from Alice to Bob on January 1, 2023 automatically.

### Setting Initial Manager

During collection creation:

```typescript
const collection: TokenCollection<bigint> = {
    creator: 'bb1alice...',
    managerTimeline: [
        {
            timelineTimes: [{ start: 1n, end: 18446744073709551615n }],
            manager: 'bb1alice...',
        },
    ],
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

### Decentralized Management Transition

Transition to a burn address manager and lock management permanently, creating a decentralized collection:

```typescript
const collection: TokenCollection<bigint> = {
    managerTimeline: [
        {
            timelineTimes: [{ start: 1n, end: 1672531199000n }],
            manager: 'bb1alice...',
        },
        {
            timelineTimes: [
                { start: 1672531200000n, end: 18446744073709551615n },
            ],
            manager: 'bb1qqqq....', // Burn address
        },
    ],
    collectionPermissions: {
        canUpdateManager: [
            {
                permanentlyPermittedTimes: [],
                permanentlyForbiddenTimes: [
                    { start: 1672531200000n, end: 18446744073709551615n },
                ],
            },
        ],
        // ... other permission fields
    },
    // ... other collection fields
};
```

This transitions to a burn address manager and locks management permanently, creating a decentralized collection.

### No Manager

If you don't want a manager, set the manager to an empty string or empty array. Permission values don't matter when there's no manager:

```typescript
const managerTimeline: ManagerTimeline<bigint>[] = [];
```

## Manager Capabilities

The manager can execute administrative actions according to permissions:

-   Updating collection metadata
-   Updating token metadata
-   Updating transferability (collection approvals)
-   Updating valid token IDs
-   Archiving the collection
-   Deleting the collection
-   Updating manager timeline
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

**Example: Timeline Permissions**

```typescript
const collectionPermissions: CollectionPermissions<bigint> = {
    canUpdateCollectionMetadata: [
        {
            timelineTimes: [{ start: 1n, end: 10n }],
            permanentlyPermittedTimes: [],
            permanentlyForbiddenTimes: [{ start: 1n, end: 10n }],
        },
        {
            timelineTimes: [{ start: 1n, end: 100n }],
            permanentlyPermittedTimes: [
                { start: 1n, end: 18446744073709551615n },
            ],
            permanentlyForbiddenTimes: [],
        },
    ],
};
```

**Result:**

-   Timeline times 1-10: **Forbidden** (first element matches, second element does not)
-   Timeline times 11-100: **Permitted** (second element matches)

## Satisfying Criteria

All criteria in a permission element must match for it to be applied. Partial matches are ignored.

**Example: Token Metadata Permissions**

```typescript
const collectionPermissions: CollectionPermissions<bigint> = {
    canUpdateTokenMetadata: [
        {
            timelineTimes: [{ start: 1n, end: 10n }],
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

-   Timeline times 1-10 AND token IDs 1-10

**It does NOT cover:**

-   Timeline time 1 with token ID 11
-   Timeline time 11 with token ID 1
-   Timeline time 11 with token ID 11

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

## Important Notes

-   **First Match Policy**: Only the first matching permission is applied
-   **Default Allow**: Unspecified permissions are allowed by default
-   **Manager Required**: Collection permissions require a manager
-   **User Control**: User permissions typically remain empty for full control
-   **Brute Force**: Use maximum ranges to ensure complete coverage
-   **Order Matters**: Array order affects permission evaluation
-   **No Forbidden + Not Frozen**: There is no forbidden + not frozen state because theoretically, it could be updated to permitted at any time and executed (thus making it permitted)

## Permission Types

There are **five types** of permissions, each with different criteria:

| Type                            | Criteria                                       | Examples                                                     |
| ------------------------------- | ---------------------------------------------- | ------------------------------------------------------------ |
| **Action Permissions**          | Time control only                              | `canDeleteCollection`                                        |
| **Timeline Permissions**        | Timeline times + time control                  | `canUpdateCollectionMetadata`, `canUpdateStandards`          |
| **Timeline with Token IDs**     | Timeline times + token IDs + time control      | `canUpdateTokenMetadata`                                     |
| **Token ID Action Permissions** | Token IDs + time control                       | `canUpdateValidTokenIds`                                     |
| **Approval Permissions**        | Transfer criteria + approval ID + time control | `canUpdateCollectionApprovals`, `canUpdateIncomingApprovals` |

### Action Permissions

Simple time-based permissions with no additional criteria. Control when actions can be executed based solely on time.

**Collection Actions:**

-   `canDeleteCollection` - Delete entire collection

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

### Timeline Permissions

Control when timeline-based fields can be updated. These permissions match on `timelineTimes` to determine which timeline values can be updated.

**Basic Timeline Permissions** (timeline times only):

-   `canArchiveCollection`
-   `canUpdateStandards`
-   `canUpdateCustomData`
-   `canUpdateManager`
-   `canUpdateCollectionMetadata`

**Token-Specific Timeline Permissions** (timeline times + token IDs):

-   `canUpdateTokenMetadata`

**Logic:**

```
For each timeline update request:
    Check if timeline time matches any timelineTimes criteria
        → If no match: ALLOW (neutral state)
        → If match: Check if current time is in permanentlyPermittedTimes
            → If yes: ALLOW
            → If no: Check if current time is in permanentlyForbiddenTimes
                → If yes: DENY
                → If no: ALLOW (neutral state)
```

**Timeline vs Execution Times:**

-   **Timeline Times**: Which timeline values can be updated?
-   **Execution Times**: When can the permission be executed?

These may not align. For example, you might forbid updating timeline values for Jan 2024 during 2023.

**Examples:**

```typescript
// Lock collection metadata forever
const collectionPermissions: CollectionPermissions<bigint> = {
    canUpdateCollectionMetadata: [
        {
            timelineTimes: [{ start: 1n, end: 18446744073709551615n }],
            permanentlyPermittedTimes: [],
            permanentlyForbiddenTimes: [
                { start: 1n, end: 18446744073709551615n },
            ],
        },
    ],
};

// Lock specific timeline period
const collectionPermissions: CollectionPermissions<bigint> = {
    canUpdateCollectionMetadata: [
        {
            timelineTimes: [{ start: 1000n, end: 2000n }],
            permanentlyPermittedTimes: [],
            permanentlyForbiddenTimes: [
                { start: 1n, end: 18446744073709551615n },
            ],
        },
    ],
};

// Lock token metadata for existing tokens
const collectionPermissions: CollectionPermissions<bigint> = {
    canUpdateTokenMetadata: [
        {
            timelineTimes: [{ start: 1n, end: 18446744073709551615n }],
            tokenIds: [{ start: 1n, end: 100n }],
            permanentlyPermittedTimes: [],
            permanentlyForbiddenTimes: [
                { start: 1n, end: 18446744073709551615n },
            ],
        },
    ],
};

// Allow updates only during specific period
const collectionPermissions: CollectionPermissions<bigint> = {
    canUpdateCollectionMetadata: [
        {
            timelineTimes: [{ start: 1n, end: 18446744073709551615n }],
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
