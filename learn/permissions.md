---
description: >-
    Fine-grained permissions system for token collections: manager controls and administrative actions
---

## Manager

The **manager** is the central authority for a collection, controlling all administrative operations. The manager executes actions according to permission rules defined in `collectionPermissions`.

```typescript
const collection = {
    managerTimeline: [
        {
            manager: 'bb1kj9kt5y64n5a8677fhjqnmcc24ht2vy9atmdls',
            timelineTimes: [{ start: '1', end: '18446744073709551615' }],
        },
    ],
    collectionPermissions: {
        // Permissions define what the manager can do
    },
};
```

### Manager Timeline

Managers can change over time using timeline-based configuration:

```typescript
// Manager changes automatically on a specific date
const managerTimeline = [
    {
        timelineTimes: [{ start: '1', end: '1672531199000' }],
        manager: 'bb1alice...',
    },
    {
        timelineTimes: [
            { start: '1672531200000', end: '18446744073709551615' },
        ],
        manager: 'bb1bob...',
    },
];
```

This transfers management from Alice to Bob on January 1, 2023 automatically.

### No Manager

If you don't want a manager, set the manager to an empty string. Permission values don't matter when there's no manager:

```typescript
const managerTimeline = [];
```

## Permissions

Permissions control who can perform actions on collections and when those actions can be executed. The manager executes administrative actions according to these permission rules.

## Permission States

Permissions have three states:

| State                     | Description                    | Behavior                                                   |
| ------------------------- | ------------------------------ | ---------------------------------------------------------- |
| **Permanently Permitted** | Action ALWAYS allowed (frozen) | Can be executed                                            |
| **Permanently Forbidden** | Action ALWAYS blocked (frozen) | Cannot be executed                                         |
| **Neutral**               | Not specified                  | **Allowed by default now, but can change state in future** |

```typescript
// Lock collection deletion forever
const collectionPermissions = {
    canDeleteCollection: [
        {
            permanentlyPermittedTimes: [],
            permanentlyForbiddenTimes: [
                { start: '1', end: '18446744073709551615' },
            ],
        },
    ],
};

// Soft-enabled (default behavior - allowed but can be changed)
const collectionPermissions = {
    canDeleteCollection: [], // Empty = allowed by default
};
```

**Important:** Once set to permanently permitted or forbidden, it cannot be changed.

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

```typescript
// Manager with full control (soft-enabled)
const collectionPermissions = {
    canDeleteCollection: [],
    canArchiveCollection: [],
    canUpdateStandards: [],
    canUpdateCustomData: [],
    canUpdateManager: [],
    canUpdateCollectionMetadata: [],
    canUpdateTokenMetadata: [],
    canUpdateCollectionApprovals: [],
    canUpdateValidTokenIds: [],
};
```

All permissions are defined on-chain with timeline-based configuration, allowing permissions to change over time.

## Common Permission Types

### Action Permissions

Simple time-based permissions (no criteria):

```typescript
// Lock collection deletion
const collectionPermissions = {
    canDeleteCollection: [
        {
            permanentlyPermittedTimes: [],
            permanentlyForbiddenTimes: FullTimeRanges,
        },
    ],
};
```

### Approval Permissions

Control approval updates with transfer criteria:

```typescript
// Lock mint approval updates
const collectionPermissions = {
    canUpdateCollectionApprovals: [
        {
            fromListId: 'Mint',
            approvalId: 'All',
            ...,
            permanentlyForbiddenTimes: FullTimeRanges,
        },
    ],
};
```

See [Permissions](../x-badges/concepts/permissions/README.md) for detailed permission configuration and all permission types.
