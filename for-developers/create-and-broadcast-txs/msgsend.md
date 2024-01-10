# MsgSend

MsgSend is the Msg to send $BADGE. It is a core Cosmos SDK msg (i.e. not written by BitBadges), o we refer you to their documentation for more information.

```typescript
/**
 * MsgSend represents a message to send coins from one account to another.
 *
 * @typedef {Object} MsgSend
 * @property {string} fromAddress - The sender of the transaction.
 * @property {string} toAddress - The recipient of the transaction.
 * @property {CosmosCoin[]} amount - The amount of coins to send.
 */
export interface MsgSend<T extends NumberType> {
  fromAddress: string
  toAddress: string
  amount: CosmosCoin<T>[]
}


/**
 * Type for Cosmos SDK Coin information with support for bigint amounts (e.g. { amount: 1000000, denom: 'badge' }).
 *
 * @typedef {Object} CosmosCoin
 * @property {NumberType} amount - The amount of the coin.
 * @property {string} denom - The denomination of the coin.
 */
export interface CosmosCoin<T extends NumberType> {
  amount: T,
  denom: string,
}

```

```json
{
  "fromAddress": "cosmos1pa6p7nsqg57yqv23khnu5dumfhycr45930m3ve",
  "toAddress": "cosmos1uy4my3dwzwv9drgq06pt433z742l9vrlnx88ds",
  "amount": [
    {
      "denom": "badge",
      "amount": "1"
    }
  ]
}
```
