# Base Collection Details

BitBadges collections are very expressive but also can lead to verbose configurations. We will provide additional examples in this section but also refer you to the corresponding concepts section for more details on any specific field.

### Reference Links

For detailed information about each field, see the corresponding concepts documentation:

| Field                         | Concepts Link                                                                    |
| ----------------------------- | -------------------------------------------------------------------------------- |
| `validTokenIds`               | [Collection Setup Fields](../../token-standard/learn/collection-setup-fields.md) |
| `managerTimeline`             | [Manager](../concepts/manager.md)                                                |
| `collectionMetadataTimeline`  | [Collection Setup Fields](../../token-standard/learn/collection-setup-fields.md) |
| `tokenMetadataTimeline`       | [Collection Setup Fields](../../token-standard/learn/collection-setup-fields.md) |
| `customDataTimeline`          | [Collection Setup Fields](../../token-standard/learn/collection-setup-fields.md) |
| `standardsTimeline`           | [Collection Setup Fields](../../token-standard/learn/collection-setup-fields.md) |
| `isArchivedTimeline`          | [Collection Setup Fields](../../token-standard/learn/collection-setup-fields.md) |
| `defaultBalances`             | [Collection Setup Fields](../../token-standard/learn/collection-setup-fields.md) |
| `mintEscrowCoinsToTransfer`   | [Mint Escrow Address](../concepts/mint-escrow-address.md)                        |
| `cosmosCoinWrapperPathsToAdd` | [Cosmos Wrapper Paths](broken-reference/)                                        |
| Timeline System (all fields)  | [Timeline System](../concepts/timeline-system.md)                                |

## Base Collection Details

For most collections, your base configuration for these fields will be very similar to this. Note that this excludes collection permissions and approvals. See the [Building Collection Approvals](building-collection-approvals.md) example and [Building Collection Permissions](building-collection-permissions.md) example for these.

```typescript
// Our standard time range represeting "forever"
const FullTimeRanges = [
    {
        start: '1',
        end: '18446744073709551615',
    },
];

const BaseCollectionDetails = {
    validTokenIds: [
        {
            start: '1',
            end: '100', // Set to your max ID
        },
    ],
    managerTimeline: [
        {
            manager: 'bb1kj9kt5y64n5a8677fhjqnmcc24ht2vy9atmdls', // Set to your address
            timelineTimes: FullTimeRanges,
        },
    ],
    collectionMetadataTimeline: [
        {
            timelineTimes: FullTimeRanges,
            collectionMetadata: {
                uri: 'ipfs://QmSTZZPgYF58gS9bM7q3nWVegUJH51WBdT91fz7q94qDwS', // Points to a valid .json metadata file
                customData: '',
            },
        },
    ],
    tokenMetadataTimeline: [
        {
            timelineTimes: FullTimeRanges,
            tokenMetadata: [
                {
                    uri: 'ipfs://QmeSjSinHpPnmXmspMjwiXyN6zS4E9zccariGR3jxcaWtq/{id}', // Points to a valid .json metadata file (replacing {id} with the token ID)
                    tokenIds: [
                        {
                            start: '1',
                            end: '100',
                        },
                    ],
                    customData: '',
                },
                // You can have multiple entries. This is useful for placeholder metadata.
                {
                    uri: 'ipfs://QmSTZZPgYF58gS9bM7q3nWVegUJH51WBdT91fz7q94qDwS', // Placeholder metadata
                    tokenIds: [
                        {
                            start: '101',
                            end: '100000000',
                        },
                    ],
                    customData: '',
                },
            ],
        },
    ],
    customDataTimeline: [
        {
            timelineTimes: FullTimeRanges,
            customData: '',
        },
    ],
    standardsTimeline: [
        {
            timelineTimes: FullTimeRanges,
            standards: ['Subscriptions'],
        },
    ],
    isArchivedTimeline: [
        {
            timelineTimes: FullTimeRanges,
            isArchived: false,
        },
    ],

    // Coins to send to the mint escrow address. You can also fund after the fact. This is just useful for genesis since the address is dependent on the collectionId which you don't know until after the collection is created.
    mintEscrowCoinsToTransfer: [
        {
            denom: 'ubadge',
            amount: '1',
        },
    ],

    // If you want to add paths to wrap tokens as Cosmos coins, you can do so here.
    cosmosCoinWrapperPathsToAdd: [],

    defaultBalances: {
        // Everyone starts with empty balances and no approvals
        balances: [],
        incomingApprovals: [],
        outgoingApprovals: [],
        // Empty = Soft Enabled (i.e. enabled but can be disabled at any time by each user)
        userPermissions: {
            canUpdateOutgoingApprovals: [],
            canUpdateIncomingApprovals: [],
            canUpdateAutoApproveSelfInitiatedOutgoingTransfers: [],
            canUpdateAutoApproveSelfInitiatedIncomingTransfers: [],
            canUpdateAutoApproveAllIncomingTransfers: [],
        },

        // Typically, these flags are all you need to set.
        autoApproveSelfInitiatedIncomingTransfers: true,
        autoApproveSelfInitiatedOutgoingTransfers: true,
        autoApproveAllIncomingTransfers: true,
    },
};
```

For information on building collection approvals, see [Building Collection Approvals](building-collection-approvals.md).
