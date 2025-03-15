# $BADGE Transfers

**coinTransfers** are the $BADGE credits to be sent **every** use of the approval. There can be multiple transfers here to implement complex royalty systems, for example. Or, you can leave it blank for no $BADGE transfers.

Note: $BADGE refers to the native gas credits token of the blockchain, not a specific badge.

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
