# $BADGE Transfers

**coinTransfers** are the $BADGE credits to be sent **every** use of the approval. There can be multiple transfers here to implement complex royalty systems, for example. Or, you can leave it blank for no $BADGE transfers. The only allowed denom is "ubadge" which has 9 decimals. 1e9 ubadge = 1 $BADGE.

Note: $BADGE refers to the native gas credits token of the blockchain, not a specific badge.

This will be executed every time the approval is used.

```typescript
export interface iCoinTransfer<T extends NumberType> {
    /**
     * The recipient of the coin transfer. This should be a Bech32 BitBadges address.
     */
    to: string;
    /**
     * The coins
     */
    coins: iCosmosCoin<T>[];
    /**
     * Whether or not to override the from address with the approver address.
     */
    overrideFromWithApproverAddress: boolean;
    /**
     * Whether or not to override the to address with the initiator of the transaction.
     */
    overrideToWithInitiator: boolean;
}
```

```typescript
export interface iCosmosCoin<T extends NumberType> {
    /** The amount of the coin. */
    amount: T;
    /** The denomination of the coin. */
    denom: string;
}
```
