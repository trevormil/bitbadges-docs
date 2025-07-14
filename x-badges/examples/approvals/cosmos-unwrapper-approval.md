# Cosmos Unwrapper Approval

This example demonstrates how to create an approval that allows the Cosmos coin wrapper address to send badges back to users, enabling conversion from Cosmos coins back to badges (unwrapping).

Full example: [Cosmos Coin Wrapper Example](../cosmos-coin-wrapper-example.md)

## Code Example

```typescript
export const unwrapperApproval = ({
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
        fromListId: specialAddress,
        fromList: AddressList.getReservedAddressList(specialAddress),
        toListId: 'All',
        toList: AddressList.AllAddresses(),
        initiatedByListId: 'All',
        initiatedByList: AddressList.AllAddresses(),
        transferTimes: UintRangeArray.FullRanges(),
        badgeIds: badgeIds,
        ownershipTimes: ownershipTimes,
        approvalId: id,
        approvalCriteria: {
            autoDeletionOptions: {
                afterOneUse: false,
                afterOverallMaxNumTransfers: false,
            },
            requireToDoesNotEqualInitiatedBy: false,
            requireFromDoesNotEqualInitiatedBy: false,
            coinTransfers: [],
            predeterminedBalances: {
                manualBalances: [],
                orderCalculationMethod: {
                    useOverallNumTransfers: false,
                    usePerToAddressNumTransfers: false,
                    usePerFromAddressNumTransfers: false,
                    usePerInitiatedByAddressNumTransfers: false,
                    useMerkleChallengeLeafIndex: false,
                    challengeTrackerId: '',
                },
                incrementedBalances: {
                    startBalances: [],
                    incrementBadgeIdsBy: 0n,
                    incrementOwnershipTimesBy: 0n,
                    //monthly in milliseconds
                    durationFromTimestamp: 0n,
                    allowOverrideTimestamp: false,
                    allowOverrideWithAnyValidBadge: false,
                    recurringOwnershipTimes: {
                        startTime: 0n,
                        intervalLength: 0n,
                        chargePeriodLength: 0n,
                    },
                },
            },
            maxNumTransfers: {
                overallMaxNumTransfers: 0n,
                perToAddressMaxNumTransfers: 0n,
                perFromAddressMaxNumTransfers: 0n,
                perInitiatedByAddressMaxNumTransfers: 0n,
                amountTrackerId: id,
                resetTimeIntervals: {
                    startTime: 0n,
                    intervalLength: 0n,
                },
            },
            approvalAmounts: {
                overallApprovalAmount: 0n,
                perFromAddressApprovalAmount: 0n,
                perToAddressApprovalAmount: 0n,
                perInitiatedByAddressApprovalAmount: 0n,
                amountTrackerId: id,
                resetTimeIntervals: {
                    startTime: 0n,
                    intervalLength: 0n,
                },
            },
            merkleChallenges: [],
            mustOwnBadges: [],
            dynamicStoreChallenges: [],
            requireToEqualsInitiatedBy: false,
            requireFromEqualsInitiatedBy: false,
            overridesFromOutgoingApprovals: true,
            overridesToIncomingApprovals: false,
            userRoyalties: {
                percentage: 0n,
                payoutAddress: '',
            },
        },
    };

    return toSet;
};
```

## Related Concepts

-   [Cosmos Wrapper Approval](./cosmos-wrapper-approval.md)
-   [Cosmos Wrapper Paths](../../concepts/cosmos-wrapper-paths.md)
-   [Transferability / Approvals](../../concepts/transferability-approvals.md)
-   [Address Lists](../../concepts/address-lists.md)
