# Default User Approvals

Use the following functions to add the default user approvals to an existing set of approvals. For incoming, this will be: if unhandled, approve only if to == initiatedBy. For outgoing, vice versa.

```typescript
export function appendDefaultForOutgoing(currApprovedTransfers: UserApprovedOutgoingTransferWithDetails<bigint>[], userAddress: string): UserApprovedOutgoingTransferWithDetails<bigint>[]
```

```typescript
export function appendDefaultForIncoming(currApprovedTransfers: UserApprovedIncomingTransferWithDetails<bigint>[], userAddress: string): UserApprovedIncomingTransferWithDetails<bigint>[]
```
