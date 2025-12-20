# Address Lists

Address lists define collections of addresses for use in approval configurations (`fromListId`, `toListId`, `initiatedByListId`). They support reserved IDs, inline colon-separated lists, and user-created stored lists.

## Proto Definition

```protobuf
message AddressList {
  string listId = 1;
  repeated string addresses = 2;
  bool whitelist = 3;  // true = whitelist, false = blacklist
  string uri = 4;
  string customData = 5;
  string createdBy = 6;
}
```

## Matching Logic

```javascript
function checkAddress(address, addressList) {
    const found = addressList.addresses.includes(address);
    return addressList.whitelist ? found : !found;
}
```

-   **Whitelist** (`whitelist: true`): Only listed addresses are included
-   **Blacklist** (`whitelist: false`): All addresses except listed ones are included

## Reserved IDs

Dynamically generated, zero storage overhead.

| ID                    | Logic                                               | Description                            |
| --------------------- | --------------------------------------------------- | -------------------------------------- |
| `"Mint"`              | `{addresses: ["Mint"], whitelist: true}`            | Only Mint address                      |
| `"All"`               | `{addresses: [], whitelist: false}`                 | All addresses (including Mint)         |
| `"None"`              | `{addresses: [], whitelist: true}`                  | No addresses                           |
| `"AllWithout<addrs>"` | `{addresses: [addrs...], whitelist: false}`         | All except specified (colon-separated) |
| `"addr1:addr2:..."`   | `{addresses: [addr1, addr2, ...], whitelist: true}` | Inline whitelist                       |

## Mint Address Handling

The set of valid addresses includes any valid `bb1` address **and** `"Mint"` (a reserved address representing the collection's mint address).

**Important**: `"All"` includes the Mint address. When creating approvals, especially `fromListId`, be careful with Mint handling since the Mint address has unlimited balances.

```json
// Exclude Mint from senders (common pattern)
{
    "fromListId": "AllWithoutMint",  // All addresses except Mint
    "toListId": "All"
}

// Allow only Mint to send (minting only)
{
    "fromListId": "Mint",
    "toListId": "All"
}
```

## Inversion

Prefix with `!` to invert whitelist/blacklist behavior. **Only works with reserved IDs and inline lists, not user-created lists.** Supports parentheses too for syntax `!(...)`.

```javascript
'!All'; // Inverts All → None behavior
'!bb1alice...:bb1bob...'; // Inverts inline list → blacklist
'!(AllWithoutMint)'; // Inverts AllWithoutMint → whitelist with only Mint
```

Formats: `"!listId"` (if listId doesn't end with `)`) or `"!(listId)"`.

## User-Created Lists

Created via `MsgCreateAddressLists`. **Immutable** once created. Referenced by the custom listId where needed. This approach is useful for large lists that are referenced multiple times to save on gas.

### ID Validation

-   Alphanumeric only (`a-z`, `A-Z`, `0-9`)
-   Cannot be empty
-   Cannot start with `!`
-   Cannot conflict with reserved IDs (`"All"`, `"Mint"`, `"None"`, `"AllWithout*"`)
-   Must be unique

### Example

```json
{
    "listId": "vipMembers",
    "addresses": ["bb1alice...", "bb1bob...", "bb1charlie..."],
    "whitelist": true,
    "uri": "",
    "customData": "",
    "createdBy": "bb1manager..."
}
```

## Usage Examples

```json
// Universal access
{
    "fromListId": "AllWithoutMint",
    "toListId": "All",
    "initiatedByListId": "All"
}

// Restricted with inline list
{
    "fromListId": "bb1alice...:bb1bob...:bb1charlie...",
    "toListId": "AllWithoutMint:bb1blocked...",
    "initiatedByListId": "All"
}

// User-created list
{
    "fromListId": "vipMembers",
    "toListId": "!bb1banned...",  // Inversion works with inline lists
    "initiatedByListId": "All"
}
```

## Performance

-   **Reserved IDs**: Zero storage, dynamic generation, minimal gas
-   **Inline lists**: Zero storage, dynamic parsing, efficient for < 10 addresses
-   **User-created**: On-chain storage, reusable short ID, efficient lookups, best for large/repeated lists
