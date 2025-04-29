# MsgUpdateUserApprovals

To update approvals, you can use [MsgUpdateUserApprovals](https://bitbadges.github.io/bitbadgesjs/classes/MsgUpdateUserApprovals.html). Upon genesis, every user's approval store and permissions will be the defaults specified by the collection.&#x20;

From there, they can be updated according to the permissions set (i.e. the **userPermissions** previously set). Users can only update thier own approvals.

We use an update flag + new value format. If the update flag is true, we will update it to the new value. If it is false, we do not update and ignore the value in the Msg and keep what was previously set.

```typescript
export interface MsgUpdateUserApprovals<T extends NumberType> {
    creator: string;
    collectionId: T;
    updateOutgoingApprovals?: boolean;
    outgoingApprovals?: UserOutgoingApproval<T>[];
    updateIncomingApprovals?: boolean;
    incomingApprovals?: UserIncomingApproval<T>[];
    updateAutoApproveSelfInitiatedOutgoingTransfers?: boolean;
    autoApproveSelfInitiatedOutgoingTransfers?: boolean;
    updateAutoApproveSelfInitiatedIncomingTransfers?: boolean;
    autoApproveSelfInitiatedIncomingTransfers?: boolean;
    updateAutoApproveAllIncomingTransfers?: boolean;
    autoApproveAllIncomingTransfers?: boolean;
    updateUserPermissions?: boolean;
    userPermissions?: UserPermissions<T>;
}
```
