# Address Lists

Reusable collections of addresses for approval configurations with efficient whitelist/blacklist logic and flexible storage options.

## Proto Definition

```protobuf
message AddressList {
  string listId = 1 [(gogoproto.customtype) = "Uint", (gogoproto.nullable) = false];
  repeated string addresses = 2;
  bool whitelist = 3;
  string uri = 4;
  string customData = 5;
  string createdBy = 6;
  string aliasAddress = 7;
}
```

## Reserved Address List Shorthand IDs

BitBadges provides powerful built-in address lists that you can use immediately without creating custom lists:

### Universal Lists

-   **`"All"`** - Includes every possible address (listId = 2^256 - 1)

    -   No storage overhead, automatically includes all addresses
    -   Perfect for public collections or unrestricted transfers
    -   Example: Open minting, public airdrops

-   **`"!All"`** - Excludes every address (empty set)
    -   Effectively blocks all transfers when used in approvals
    -   Useful for temporarily disabling transfers

### Special Address References

-   **`"Mint"`** - References the special mint address
    -   Used for badge creation and initial distribution
    -   Cannot be used as a regular address for transfers

### Dynamic List Inversion

-   **`"!{listId}"`** - Inverts any existing list (e.g., `"!5"` inverts list 5)
    -   Whitelist becomes blacklist, blacklist becomes whitelist
    -   Allows reusing lists with opposite logic
    -   Example: `"!1"` means "everyone except those in list 1"

### Practical Reserved ID Examples

```json
// Everyone can transfer freely
{
  "listId": "All",
  "approvalAmounts": "unlimited",
  "maxNumTransfers": "unlimited"
}

// Only VIP members (list 1), everyone else blocked
{
  "listId": "!1",        // Invert VIP list to block non-VIPs
  "approvalAmounts": "0",
  "maxNumTransfers": "0"
}

// Block specific addresses (list 2), allow everyone else
{
  "listId": "!2",        // Invert blacklist to allow non-blocked
  "approvalAmounts": "unlimited",
  "maxNumTransfers": "unlimited"
}
```

### Performance Benefits

-   **Zero storage cost** for `"All"` and `"!All"`
-   **Instant availability** - no need to create lists first
-   **Dynamic inversion** without storing duplicate data
-   **Gas efficiency** for common access patterns

## When to Use Lists vs Badges

Address lists are primarily used for **approvals and permissions** rather than representing ownership or membership. Use address lists when you need to:

-   Define who can transfer badges (approval configurations)
-   Set up whitelist/blacklist logic for transfers
-   Create reusable address groups for multiple collections
-   Implement complex approval criteria

For representing **membership or ownership**, use badges themselves rather than address lists.

## Whitelist vs Blacklist Logic

Address lists use an **inversion-based** system for efficient whitelist/blacklist operations:

### Whitelist Logic (`whitelist: true`)

-   **Listed addresses**: Explicitly allowed
-   **Unlisted addresses**: Implicitly forbidden
-   **Use case**: "Only these specific addresses can participate"

### Blacklist Logic (`whitelist: false`)

-   **Listed addresses**: Explicitly forbidden
-   **Unlisted addresses**: Implicitly allowed
-   **Use case**: "Everyone can participate except these addresses"

### Inversion

The `whitelist` boolean can be **inverted** when used in approval criteria to flip the logic:

-   Whitelist with inversion → Blacklist behavior
-   Blacklist with inversion → Whitelist behavior

## Storage Options

### On-Chain Storage

-   Addresses stored directly in the `addresses` field
-   Immediate availability and verification
-   Higher storage costs for large lists
-   Suitable for smaller, frequently-used lists

### Off-Chain Storage

-   Addresses stored externally, referenced by `uri`
-   Lower on-chain storage costs
-   Requires external storage infrastructure
-   Suitable for large lists or dynamic membership

**Example URI formats:**

-   IPFS: `ipfs://QmHash...`
-   HTTPS: `https://api.example.com/lists/123`
-   Custom protocols supported

## Advanced Reserved Features

### Complex Inversion Patterns

You can chain and combine reserved IDs for sophisticated access control:

```json
// Multi-tier access control
{
    "collectionApprovals": [
        {
            "listId": "1", // VIP tier: full access
            "approvalAmounts": "unlimited"
        },
        {
            "listId": "!3", // Everyone except banned users
            "approvalAmounts": "1", // Limited access for regular users
            "maxNumTransfers": "3"
        }
    ]
}
```

### Reserved ID Best Practices

1. **Start with reserved IDs** - Use `"All"` and `"!listId"` before creating custom lists
2. **Combine strategically** - Layer multiple approval tiers using inversion
3. **Document intentions** - Clear comments when using complex inversion logic
4. **Test thoroughly** - Verify inverted logic behaves as expected

### Common Patterns

-   **Public with exceptions**: `"!{banned_list_id}"` for public access with blocklist
-   **Private with inclusions**: `"{whitelist_id}"` for restricted access
-   **Temporary restrictions**: `"!All"` to pause all activity
-   **Minting controls**: `"Mint"` for creation-only operations

## Custom IDs for Efficiency

For optimal efficiency and predictable list IDs, consider:

1. **Pre-create address lists** during collection setup
2. **Use sequential IDs** for related lists (1, 2, 3...)
3. **Document list purposes** in metadata for clarity
4. **Reuse lists** across multiple collections when appropriate

## Practical Examples

### Example 1: VIP Access List

```json
{
    "listId": "1",
    "addresses": ["bb1alice...", "bb1bob...", "bb1charlie..."],
    "whitelist": true,
    "uri": "",
    "customData": "VIP members with exclusive access",
    "createdBy": "bb1manager...",
    "aliasAddress": ""
}
```

### Example 2: Blocked Users List

```json
{
    "listId": "2",
    "addresses": ["bb1spammer...", "bb1banned..."],
    "whitelist": false,
    "uri": "",
    "customData": "Blocked users - cannot participate",
    "createdBy": "bb1manager...",
    "aliasAddress": ""
}
```

### Example 3: Large Community (Off-Chain)

```json
{
    "listId": "3",
    "addresses": [],
    "whitelist": true,
    "uri": "ipfs://QmCommunityMembersList...",
    "customData": "10k+ community members stored off-chain",
    "createdBy": "bb1dao...",
    "aliasAddress": ""
}
```

### Example 4: Using Reserved IDs

```json
// Reference in approval configuration
{
  "listId": "All",        // Everyone allowed
  "approvalAmounts": "unlimited",
  "maxNumTransfers": "unlimited"
}

{
  "listId": "!5",         // Everyone except list 5
  "approvalAmounts": "1",
  "maxNumTransfers": "1"
}
```

## Usage in Approvals

Address lists are referenced in approval configurations through `ApprovalIdentifierDetails`:

```protobuf
message ApprovalIdentifierDetails {
  string listId = 1;                    // Reference to address list ID
  repeated string addresses = 2;        // Direct address specification
}
```

You can either:

-   **Reference a list**: Use `listId` to reference a pre-created address list
-   **Specify directly**: Use `addresses` field for one-time address specification

The approval system will resolve the addresses and apply the whitelist/blacklist logic accordingly.
