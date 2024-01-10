# MsgStoreCodeCompat

```typescript
export interface MsgStoreCodeCompat {
  sender: string
  hexWasmByteCode: string
}
```

MsgStoreCompat is a wrapper for CosmWASM's MsgStoreCode that is compatible with all signing methods for BitBadges users. See [Create a Smart Contract](../../tutorials/create-a-wasm-contract.md) tutorial for more information.
