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
 * Type for Cosmos SDK Coin information with support for bigint amounts (e.g. { amount: 1000000, denom: 'ubadge' }).
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
  "fromAddress": "bb1pa6p7nsqg57yqv23khnu5dumfhycr459jjnzsg",
  "toAddress": "bb1uy4my3dwzwv9drgq06pt433z742l9vrlsm053p",
  "amount": [
    {
      "denom": "ubadge",
      "amount": "1"
    }
  ]
}
```
