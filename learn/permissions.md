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
                { start: '1', end: '18446744073709551615' },
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

## Common Permission Types

### Action Permissions

Simple time-based permissions (no criteria):

```typescript
// Lock collection deletion
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
```

### Approval Permissions

Control approval updates with transfer criteria with support for fine-grained control:

```typescript
// Lock mint approval updates
const collectionPermissions: CollectionPermissions<bigint> = {
    canUpdateCollectionApprovals: [
        {
            fromListId: 'Mint',
            toListId: 'All',
            initiatedByListId: 'All',
            transferTimes: [{ start: 1n, end: 18446744073709551615n }],
            tokenIds: [{ start: 1n, end: 18446744073709551615n }],
            ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
            approvalId: 'All',
            permanentlyPermittedTimes: [],
            permanentlyForbiddenTimes: [
                { start: 1n, end: 18446744073709551615n },
            ],
        },
    ],
    // ... other permission fields
};
```

See [Permissions](../x-badges/concepts/permissions/README.md) for detailed permission configuration and all permission types.
