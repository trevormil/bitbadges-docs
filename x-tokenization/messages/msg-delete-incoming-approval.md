# MsgDeleteIncomingApproval

A helper message to delete a single incoming approval for token transfers. This is a developer-friendly wrapper around `MsgUpdateUserApprovals` that simplifies deleting individual incoming approvals. For more information, we refer to the [MsgUpdateUserApprovals](./msg-update-user-approvals.md) documentation.

## Overview

This message allows you to delete a single incoming approval by its ID without having to construct the full `MsgUpdateUserApprovals` message with an empty approval list.

## Proto Definition

```protobuf
message MsgDeleteIncomingApproval {
  string creator = 1; // User deleting the approval
  string collectionId = 2; // Target collection for approval
  string approvalId = 3; // The ID of the approval to delete
}

message MsgDeleteIncomingApprovalResponse {
  bool found = 1; // Whether the approval was found and deleted
  string version = 2; // The version of the deleted approval
  repeated string reviewItems = 3; // Advisory review items about the transaction
}
```

## Response

The response includes:

-   **`found`**: Whether the approval was found and successfully deleted
-   **`version`**: The version of the approval that was deleted
-   **`reviewItems`**: Advisory strings about the transaction (see [Review Items](../concepts/approval-change-events.md#review-items))

## Usage Example

```bash
# CLI command
bitbadgeschaind tx tokenization delete-incoming-approval [collection-id] [approval-id] --from user-key
```

### Example

```bash
bitbadgeschaind tx tokenization delete-incoming-approval 1 "my-approval-1" --from user-key
```

## Behavior

-   **Approval Lookup**: The system searches for an incoming approval with the specified `approvalId`
-   **Deletion**: If found, the approval is removed from the user's incoming approvals list
-   **Error Handling**: If the approval ID is not found, an error is returned
-   **Validation**: The deletion is validated according to the collection's permissions and user's approval update permissions

## Authorization & Permissions

Users can only delete their own incoming approvals. The operation must be performed according to the permissions set (i.e. the `userPermissions` previously set for that user).

## Related Messages

-   [MsgUpdateUserApprovals](./msg-update-user-approvals.md) - Full approval management
-   [MsgSetIncomingApproval](./msg-set-incoming-approval.md) - Set an incoming approval
-   [MsgDeleteOutgoingApproval](./msg-delete-outgoing-approval.md) - Delete an outgoing approval
