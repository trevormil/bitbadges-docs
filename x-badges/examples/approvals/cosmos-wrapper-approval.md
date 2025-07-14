# Cosmos Wrapper Approval

This example demonstrates how to create an approval that allows badges to be sent to a Cosmos coin wrapper address, enabling conversion to native Cosmos SDK coins.

Full example: [Cosmos Coin Wrapper Example](../cosmos-coin-wrapper-example.md)

## Code Example

```typescript
export const wrapperApproval = ({
    specialAddress,
    badgeIds,
    ownershipTimes,
    approvalId,
}: {
    specialAddress: string;
    badgeIds: iUintRange<bigint>[];
    ownershipTimes: iUintRange<bigint>[];
    approvalId: string;
}): RequiredApprovalProps => {
    const id = approvalId;
    const toSet: RequiredApprovalProps = {
        version: 0n,
        toListId: specialAddress,
        toList: AddressList.getReservedAddressList(specialAddress),
        fromListId: 'AllWithoutMint',
        fromList: AddressList.getReservedAddressList('AllWithoutMint'),
        initiatedByListId: 'All',
        initiatedByList: AddressList.AllAddresses(),
        transferTimes: UintRangeArray.FullRanges(),
        badgeIds: badgeIds,
        ownershipTimes: ownershipTimes,
        approvalId: id,
        approvalCriteria: {
            ...defaultNoRestrictionsApprovalCriteria,
            overridesToIncomingApprovals: true,
        },
    };

    return toSet;
};
```

## Related Concepts

-   [Cosmos Wrapper Paths](../../concepts/cosmos-wrapper-paths.md)
-   [Transferability / Approvals](../../concepts/transferability-approvals.md)
-   [Address Lists](../../concepts/address-lists.md)
