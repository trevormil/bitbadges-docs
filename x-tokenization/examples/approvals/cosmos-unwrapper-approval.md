# Cosmos Unwrapper Approval

This example demonstrates how to create an approval that allows the Cosmos coin wrapper address to send tokens back to users, enabling conversion from Cosmos coins back to tokens (unwrapping).

You pretty much: 1) figure out your address and 2) figure out a path that users can send from this address without needing the address to control its approvals.

Full example: [Cosmos Coin Wrapper Example](broken-reference/)

## Code Example

```typescript
export const unwrapperApproval = ({
    specialAddress,
    tokenIds,
    ownershipTimes,
    approvalId,
}: {
    specialAddress: string;
    tokenIds: iUintRange<bigint>[];
    ownershipTimes: iUintRange<bigint>[];
    approvalId: string;
}): RequiredApprovalProps => {
    const id = approvalId;
    const toSet: RequiredApprovalProps = {
        version: 0n,
        fromListId: specialAddress,
        fromList: AddressList.getReservedAddressList(specialAddress),
        toListId: 'All',
        toList: AddressList.AllAddresses(),
        initiatedByListId: 'All',
        initiatedByList: AddressList.AllAddresses(),
        transferTimes: UintRangeArray.FullRanges(),
        tokenIds: tokenIds,
        ownershipTimes: ownershipTimes,
        approvalId: id,
        approvalCriteria: {
            ...EmptyApprovalCriteria,
            allowSpecialWrapping: true, // Required for wrapper path operations
            overridesFromOutgoingApprovals: true,
        },
    };

    return toSet;
};
```
