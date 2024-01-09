# Self-Initiated Approvals

Use the following functions to add the default user approvals to an existing set of approvals. For incoming, this will be: if unhandled, approve only if to == initiatedBy. For outgoing, vice versa.

```typescript
export function appendSelfInitiatedOutgoingApproval(currApprovals: UserOutgoingApprovalWithDetails<bigint>[], userAddress: string): UserOutgoingApprovalWithDetails<bigint>[]
```

```typescript
export function appendSelfInitiatedIncomingApproval(currApprovals: UserIncomingApprovalWithDetails<bigint>[], userAddress: string): UserIncomingApprovalWithDetails<bigint>[] 
```

