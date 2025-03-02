# MsgExecuteContractCompat



```typescript
/**
 * MsgExecuteContractCompat defines a ExecuteContractCompat message.
 *
 * @typedef {Object} MsgExecuteContractCompat
 * @property {string} sender - The sender of the transaction.
 * @property {string} contract - The contract address to execute.
 * @property {string} msg - The message to pass to the contract. Must be a valid JSON string.
 * @property {string} funds - The funds to send to the contract. Must be a valid JSON string.
 */
export interface MsgExecuteContractCompat {
  sender: string
  contract: string
  msg: string
  funds: string
}
```

MsgExecuteContractCompat is a wrapper for CosmWASM's MsgExecuteContract that is compatible with all signing methods for BitBadges users. See [Create a Smart Contract](../../create-a-wasm-contract.md) tutorial for more information.
