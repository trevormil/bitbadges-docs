# Subscriptions Protocol

The subscriptions protocol is a subset of the overall badge standard. We use the folllowing techniques to make it work:

1. Set up a collection faucet with the durationFromTimestamp + allowOverrideTimestamp + coinTransfers to determine the price
2. Each user sets up incoming approvals using recurringOwnershipTimes
3. The MsgTransferBadges specifies overrideTimestamp to match the next recurring interval with the overriden timestamp.

```typescript
import {
    AddressList,
    iCollectionApprovalWithDetails,
    iUintRange,
    iUserIncomingApprovalWithDetails,
    UintRangeArray,
} from 'bitbadgesjs-sdk';
import { t } from 'i18next';
import { RequiredApprovalProps } from '../../components/transfers/ApprovalSelectHelpers/utils';

export const MONTHLY_MS = 2629746000n;
export const ANNUAL_MS = 31556952000n;

export const subscriptionFaucet = ({
    payouts,
    defaultApprovalToAdd,
    validBadgeIds,
    intervalDuration = MONTHLY_MS,
    approvalId,
}: {
    payouts: {
        payoutAddress: string;
        ubadgeAmount: bigint;
    }[];
    defaultApprovalToAdd: RequiredApprovalProps;
    validBadgeIds: iUintRange<bigint>[];
    intervalDuration?: bigint;
    approvalId: string;
}): RequiredApprovalProps => {
    const id = approvalId;
    const toSet: RequiredApprovalProps = {
        ...defaultApprovalToAdd,
        details: {
            ...defaultApprovalToAdd.details,
            name: t('Subscription_Faucet', { ns: 'common' }),
            description: t('subscription_faucet_description', { ns: 'common' }),
        },
        initiatedByListId: 'All',
        initiatedByList: AddressList.AllAddresses(),
        transferTimes: UintRangeArray.FullRanges(),
        badgeIds: validBadgeIds,
        ownershipTimes: UintRangeArray.FullRanges(),
        approvalId: id,
        approvalCriteria: {
            ...defaultApprovalToAdd.approvalCriteria,
            coinTransfers: payouts.map((payout) => ({
                to: payout.payoutAddress,
                overrideFromWithApproverAddress: false,
                overrideToWithInitiator: false,
                coins: [
                    {
                        amount: payout.ubadgeAmount,
                        denom: 'ubadge',
                    },
                ],
            })),
            predeterminedBalances: {
                manualBalances: [],
                orderCalculationMethod: {
                    useOverallNumTransfers: true,
                    usePerToAddressNumTransfers: false,
                    usePerFromAddressNumTransfers: false,
                    usePerInitiatedByAddressNumTransfers: false,
                    useMerkleChallengeLeafIndex: false,
                    challengeTrackerId: '',
                },
                incrementedBalances: {
                    startBalances: [
                        {
                            amount: 1n,
                            badgeIds: [{ start: 1n, end: 1n }],
                            ownershipTimes: UintRangeArray.FullRanges(),
                        },
                    ],
                    incrementBadgeIdsBy: 0n,
                    incrementOwnershipTimesBy: 0n,

                    //monthly in milliseconds
                    durationFromTimestamp: intervalDuration
                        ? intervalDuration
                        : MONTHLY_MS,
                    allowOverrideTimestamp: true,
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
            requireToEqualsInitiatedBy: false,
            requireFromEqualsInitiatedBy: false,
            overridesFromOutgoingApprovals: true,
            overridesToIncomingApprovals: false,
        },
    };

    return toSet;
};

export const userRecurringApproval = ({
    subscriptionApproval,
    firstIntervalStartTime,
    ubadgeTipAmount,
    transferTimes,
    approvalId,
}: {
    subscriptionApproval: iCollectionApprovalWithDetails<bigint>;
    firstIntervalStartTime: bigint;
    ubadgeTipAmount: bigint;
    transferTimes: UintRangeArray<bigint>;
    approvalId: string;
}) => {
    const id = approvalId;

    const badgeIds = [{ start: 1n, end: 1n }];
    const intervalLength = BigInt(
        subscriptionApproval.approvalCriteria?.predeterminedBalances
            ?.incrementedBalances.durationFromTimestamp ?? 0
    );
    const chargePeriodLength = BigInt(
        Math.min(Number(intervalLength), 604800000)
    );
    let subscriptionAmount = 0n;
    for (const coinTransfer of subscriptionApproval.approvalCriteria
        ?.coinTransfers ?? []) {
        subscriptionAmount += coinTransfer.coins[0].amount;
    }

    return {
        fromList: AddressList.Reserved('Mint'),
        fromListId: 'Mint',
        initiatedByList: AddressList.AllAddresses(),
        initiatedByListId: 'All',
        transferTimes: transferTimes,
        badgeIds: badgeIds,
        ownershipTimes: UintRangeArray.FullRanges(),
        approvalId: id,
        approvalCriteria: {
            coinTransfers: [
                {
                    to: '',
                    overrideFromWithApproverAddress: true,
                    overrideToWithInitiator: true,
                    coins: [
                        {
                            amount: ubadgeTipAmount + subscriptionAmount,
                            denom: 'ubadge',
                        },
                    ],
                },
            ],
            predeterminedBalances: {
                manualBalances: [],
                orderCalculationMethod: {
                    useOverallNumTransfers: true,
                    usePerToAddressNumTransfers: false,
                    usePerFromAddressNumTransfers: false,
                    usePerInitiatedByAddressNumTransfers: false,
                    useMerkleChallengeLeafIndex: false,
                    challengeTrackerId: '',
                },
                incrementedBalances: {
                    // We override the start balances to be the badge ids from the subscription approval
                    // This is to get the correct badge IDs and amounts. Times will be overridden by the recurring approval
                    startBalances: [
                        {
                            amount: 1n,
                            badgeIds: badgeIds,
                            ownershipTimes: UintRangeArray.FullRanges(),
                        },
                    ],
                    incrementBadgeIdsBy: 0n,
                    incrementOwnershipTimesBy: 0n,
                    durationFromTimestamp: 0n,
                    allowOverrideTimestamp: false,
                    allowOverrideWithAnyValidBadge: false,
                    recurringOwnershipTimes: {
                        startTime: firstIntervalStartTime,
                        intervalLength: intervalLength,
                        //1 week in milliseconds
                        chargePeriodLength: chargePeriodLength,
                    },
                },
            },
            maxNumTransfers: {
                overallMaxNumTransfers: 1n,
                perToAddressMaxNumTransfers: 0n,
                perFromAddressMaxNumTransfers: 0n,
                perInitiatedByAddressMaxNumTransfers: 0n,
                amountTrackerId: id,
                resetTimeIntervals: {
                    startTime: firstIntervalStartTime,
                    intervalLength: intervalLength,
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
            requireToEqualsInitiatedBy: false,
            requireFromEqualsInitiatedBy: false,
        },
        details: {
            ...subscriptionApproval.details,
            name: t('Recurring_Approval', { ns: 'common' }),
            description: t('recurring_approval_description', { ns: 'common' }),
        },
        version: 0n,
    } as iUserIncomingApprovalWithDetails<bigint>;
};
```
