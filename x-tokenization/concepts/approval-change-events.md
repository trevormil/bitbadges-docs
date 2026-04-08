# Approval Change Events and Review Items

## Approval Change Events

The chain emits granular `approvalChange` events whenever an approval is created, edited, or deleted. These events are emitted by any message that modifies approvals, including `MsgSetCollectionApprovals`, `MsgUniversalUpdateCollection`, `MsgCreateCollection`, `MsgUpdateUserApprovals`, and the individual set/delete approval messages.

### Event Type

The event type is `approvalChange`. Each event includes the following attributes:

| Attribute | Description |
|---|---|
| `collectionId` | The ID of the collection the approval belongs to |
| `approvalId` | The unique identifier of the approval |
| `approvalLevel` | One of `"collection"`, `"incoming"`, or `"outgoing"` |
| `approverAddress` | The address of the approver (empty for collection-level approvals) |
| `action` | One of `"created"`, `"edited"`, or `"deleted"` |
| `version` | The version number of the approval after the change |
| `approval` | The full approval object serialized as JSON |

### When Events Are Emitted

The chain automatically detects the type of change by comparing the new approval state against the previous state:

-   **Created**: A new approval ID appears that did not exist before
-   **Edited**: An existing approval ID is present but its content has changed (the version is incremented)
-   **Deleted**: A previously existing approval ID is no longer present in the updated list

### Who Consumes These Events

Approval change events are primarily intended for:

-   **Indexers** that need to track approval state changes without re-fetching the full collection
-   **AI agents** and automation tools that monitor on-chain activity
-   **Notification systems** that alert users when approvals affecting them are modified

These events are informational. They do not affect transaction execution or consensus.

### ApprovalChange Proto

The `ApprovalChange` type is also returned in message responses for programmatic access:

```protobuf
message ApprovalChange {
  string collectionId = 1;
  string approvalId = 2;
  string approvalLevel = 3; // "collection", "incoming", or "outgoing"
  string approverAddress = 4;
  string action = 5; // "created", "edited", or "deleted"
  string version = 6;
}
```

## Review Items

Every message response in the tokenization module includes a `reviewItems` field. Review items are advisory strings that highlight important aspects of the transaction for the caller to be aware of.

### Key Properties

-   Review items are **advisory only** -- they never cause a transaction to fail
-   They are returned in the message response, not emitted as events
-   They are intended to surface warnings, confirmations, or noteworthy details about what the transaction did

### Example Review Items

```
"This transfer used approval 'public-mint' (version 3) which has coin transfer side effects"
"Approval 'presale' was deleted because it had autoDeletion enabled and reached its max uses"
"Collection approval permissions are permanently locked -- no further changes are possible"
"The manager field was set to empty, meaning no address can manage this collection"
```

### Usage

Review items are useful for:

-   **Wallets and frontends** that want to display human-readable summaries of what a transaction did
-   **AI agents** that need to understand the implications of a transaction they submitted
-   **Debugging** to understand why a transaction behaved in a particular way
