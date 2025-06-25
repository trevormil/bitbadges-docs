# MsgInstantiateContractCompat

```typescript
export interface MsgInstantiateContractCompat {
  sender: string
  codeId: string
  label: string
  funds: string
}
```

MsgInstantiateContractCompat is a wrapper for CosmWASM's MsgInstantiateContract that is compatible with all signing methods for BitBadges users. See [Create a Smart Contract](broken-reference) tutorial for more information.
