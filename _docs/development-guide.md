# Documentation Development Guide

A concise guide for writing GitBook markdown documentation for BitBadges.

## Core Principles

### 1. Be Concise

**Bad:**

```markdown
In this section, we will be discussing the various aspects and components that make up the transfer validation process, which is a critical part of how the system ensures that all transfers are properly validated before they are executed.
```

**Good:**

```markdown
Transfers must pass validation before execution. The system checks balances, collection approvals, and user-level approvals.
```

### 2. Code Snippets Everywhere

Every section should include at least one code snippet, example, or graphic.

````markdown
## Creating a Collection

Use `MsgCreateCollection` to create a new badge collection:

```typescript
const msg = {
    typeUrl: '/bitbadges.badges.MsgCreateCollection',
    value: {
        creator: 'bb1kj9kt5y64n5a8677fhjqnmcc24ht2vy9atmdls',
        collectionId: '1',
        badges: [],
    },
};
```
````

````

### 3. Developer-First, Zero Assumptions

Use technical jargon freely, but explain concepts as if the reader has no prior knowledge.

```markdown
## UintRanges

UintRanges represent token ID ranges using string-based uint64 values. Use them for batch operations:

```typescript
const tokenIds: UintRange[] = [
  { start: '1', end: '100' },
  { start: '200', end: '300' },
];
````

**Why strings?** JavaScript's `Number.MAX_SAFE_INTEGER` is `2^53 - 1`, but uint64 supports up to `2^64 - 1`. Strings prevent precision loss.

````

## GitBook Markdown Syntax

### Frontmatter

**Do not include descriptions in frontmatter.** GitBook automatically generates descriptions from page titles and content.

```markdown
---
# No description field needed
---
````

### Content References

Link to related docs using GitBook's content reference syntax:

```markdown
{% content-ref url="../path/to/file.md" %}
[Link Text](../path/to/file.md)
{% endcontent-ref %}
```

### Images

Use GitBook's figure syntax with captions:

```markdown
<figure>
  <img src="../.gitbook/assets/image.png" alt="Description">
  <figcaption>Caption explaining the image</figcaption>
</figure>
```

### Code Blocks

Always specify language for syntax highlighting:

```typescript
// TypeScript example
const collection = { id: '1' };
```

```json
// JSON example
{
    "collectionId": "1",
    "badges": []
}
```

```bash
# Shell commands
bitbadgeschaind query badges collection 1
```

## Structure Guidelines

### Headers

**Avoid H1 headings:** GitBook automatically applies the page title as an H1. Start with H2 (`##`) or lower:

```markdown
# ❌ Don't use H1 - GitBook adds this automatically

## ✅ Start with H2
```

Use descriptive headers. Prefer action-oriented titles:

```markdown
## Creating Collections

## Building Approvals

## Querying Balances
```

Not:

```markdown
## Collection Creation

## About Approvals

## Balance Information
```

### Nested Section READMEs

For parent pages with nested children, leave the README minimal or blank. GitBook automatically generates a table of contents:

```markdown
---
description: >-
    Brief description of the section
---

<!-- GitBook automatically shows child pages here -->
```

**Don't manually list child pages** - GitBook does this automatically from SUMMARY.md.

### Sections

Keep sections focused. If a section exceeds 300 words, split it:

```markdown
## Collection Configuration

### Base Fields

[Content here]

### Metadata Timeline

[Content here]

### Permissions

[Content here]
```

### Lists

Use lists for multiple items, steps, or options:

````markdown
## Transfer Validation Steps

1. **Balance Check**: Verify sender has sufficient tokens
2. **Collection Approval**: At least one approval must pass
3. **User Approvals**: Sender/recipient approvals must pass

```typescript
const validateTransfer = (transfer: Transfer) => {
    checkBalance(transfer);
    checkCollectionApprovals(transfer);
    checkUserApprovals(transfer);
};
```
````

````

## Code Examples

### Real Examples Only

Always use working, tested code:

```typescript
// ✅ Good - Real, working example
const msg = {
  typeUrl: '/bitbadges.badges.MsgCreateCollection',
  value: {
    creator: 'bb1kj9kt5y64n5a8677fhjqnmcc24ht2vy9atmdls',
    collectionId: '1',
    badges: [],
  },
};

// ❌ Bad - Placeholder
const msg = {
  typeUrl: '...',
  value: {
    creator: 'YOUR_ADDRESS',
    // ...
  },
};
````

### Context Matters

Provide enough context for the example to be useful:

```typescript
import { MsgCreateCollection } from 'bitbadgesjs-sdk';

// Create a collection with 100 token IDs
const collection = {
    // ...
    validTokenIds: [{ start: '1', end: '100' }],
    manager: 'bb1kj9kt5y64n5a8677fhjqnmcc24ht2vy9atmdls',
};

const msg = MsgCreateCollection(collection);
```

### Common Patterns

Extract common patterns into reusable examples:

```typescript
// Use throughout documentation
const manager = 'bb1kj9kt5y64n5a8677fhjqnmcc24ht2vy9atmdls';
```

## Tables

Use tables for structured data:

```markdown
| Field           | Type        | Description                  |
| --------------- | ----------- | ---------------------------- |
| `collectionId`  | string      | Unique collection identifier |
| `validTokenIds` | UintRange[] | Valid token ID ranges        |
| `manager`       | string      | Manager address              |
```

## Cross-References

Link to related concepts liberally within the content, but avoid "Next Steps" sections at the end:

```markdown
## Collection Approvals

Collection approvals define transferability rules. See [User Approvals](./user-approvals.md) for sender/recipient-level controls.

For approval criteria details, see [Approval Criteria](../concepts/approval-criteria/).
```

**Don't add "Next Steps" sections:** Avoid adding a "Next Steps" section with links at the end of pages. Instead, embed relevant links naturally within the content where they're contextually relevant.

## Writing Style

### Active Voice

```markdown
✅ The system validates transfers automatically.
❌ Transfers are validated automatically by the system.
```

### Direct Statements

```markdown
✅ Use `MsgTransfer` to transfer tokens.
❌ You can use `MsgTransfer` if you want to transfer tokens.
```

### Technical Terms

Define terms on first use, then use freely:

```markdown
## Mint Address

The **Mint address** is a reserved address representing the collection's minting source. It has unlimited balances.

Transfers from the Mint address create new tokens. The Mint address cannot receive tokens.
```

### Avoid Redundant Explanations

Don't repeat information that's already explained in the text. If you've stated something, don't create a separate "Key properties" or "Summary" list that repeats it:

```markdown
# ❌ Bad - Redundant

The **Mint address** is a reserved address representing the collection's minting source. It has unlimited balances, and any transfer from the Mint address creates tokens.

**Key properties:**

-   Unlimited balances
-   Transfers from Mint = token creation
-   Cannot receive tokens

# ✅ Good - No redundancy

The **Mint address** is a reserved address representing the collection's minting source. It has unlimited balances, and any transfer from the Mint address creates tokens. The Mint address cannot receive tokens.
```

**Exception:** Summary lists are acceptable if they consolidate information from multiple sections or provide a quick reference after a long explanation.

## Common Patterns

### Concept Explanation

````markdown
## Collection Fields

Collection fields are set directly without timeline structures.

```typescript
const collectionMetadata = {
    uri: 'ipfs://...',
    customData: '',
};
```
````

This sets the collection metadata directly.

````

### API Documentation

```markdown
## Query Collection

Query a collection by ID:

```bash
bitbadgeschaind query badges collection 1
````

**Response:**

```json
{
    "collection": {
        "collectionId": "1",
        "validTokenIds": [{ "start": "1", "end": "100" }]
    }
}
```

````

### Tutorial Format

```markdown
## Creating Your First Collection

1. **Set valid token IDs:**
```typescript
const validTokenIds = [{ start: '1', end: '100' }];
````

2. **Configure manager:**

```typescript
const manager = 'YOUR_ADDRESS';
```

3. **Create the collection:**

```typescript
const msg = MsgCreateCollection({ validTokenIds, managerTimeline });
```

````

## Checklist

Before publishing, verify:

- [ ] Every section has at least one code snippet/example/graphic
- [ ] All code examples are real and tested
- [ ] Frontmatter includes description
- [ ] Related docs are cross-referenced
- [ ] Technical terms are defined on first use
- [ ] No fluff or unnecessary words
- [ ] Headers are action-oriented
- [ ] Code blocks specify language
- [ ] Images have alt text and captions

## BitBadges-Specific Conventions

### CLI Command

Always use `bitbadgeschaind` (not `bitbadgesd`):

```bash
# ✅ Correct
bitbadgeschaind query badges collection 1
bitbadgeschaind tx badges create-collection '[tx-json]' --from key

# ❌ Incorrect
bitbadgesd query badges collection 1
```

### Address Format

BitBadges addresses use the `bb1` prefix:

```typescript
// ✅ Correct
const address = 'bb1kj9kt5y64n5a8677fhjqnmcc24ht2vy9atmdls';
const manager = 'bb1uqxan5ch2ulhkjrgmre90rr923932w38gwez5d';

// ❌ Incorrect
const address = 'cosmos1...'; // Wrong prefix
const address = '0x...'; // Ethereum format
```

**Reserved addresses:**
- `"Mint"` - Special reserved address for minting (not a `bb1` address)

### SDK Package

Use `bitbadgesjs-sdk` (not `@bitbadges/bitbadgesjs`):

```typescript
// ✅ Correct
import { BitBadgesAPI, BigIntify, Balance } from 'bitbadgesjs-sdk';

// ❌ Incorrect
import { BitBadgesAPI } from '@bitbadges/bitbadgesjs';
```

### Number Types

Use string-based uint64 values to prevent precision loss:

```typescript
// ✅ Correct - Strings for large numbers
const tokenIds = [{ start: '1', end: '18446744073709551615' }];
const amount = '1000000000000';

// ❌ Incorrect - Numbers lose precision
const tokenIds = [{ start: 1, end: 18446744073709551615 }]; // Precision loss!
```

Convert with SDK functions:
- `BigIntify` - Convert to `bigint`
- `Numberify` - Convert to `number`
- `Stringify` - Keep as `string`

### Denomination

Base denomination is `ubadge` (display: `BADGE`):

```typescript
// ✅ Correct
const coin = { denom: 'ubadge', amount: '1000000000' };

// ❌ Incorrect
const coin = { denom: 'badge', amount: '1000000000' };
```

### Module Name

The Cosmos SDK module is `x/badges`:

```typescript
// ✅ Correct
typeUrl: '/bitbadges.badges.MsgCreateCollection'

// ❌ Incorrect
typeUrl: '/bitbadges.tokenization.MsgCreateCollection'
```

### Reserved Address List IDs

Use reserved IDs for common patterns:

```typescript
// ✅ Correct
toListId: 'All' // All addresses including Mint
toListId: 'Mint' // Only Mint address
toListId: 'None' // No addresses
toListId: 'AllWithoutMint:bb1abc...' // All except specified
toListId: 'bb1abc...:bb1def...' // Inline whitelist

// ❌ Incorrect
toListId: 'all' // Case-sensitive
toListId: 'ALL' // Case-sensitive
```

### API Endpoints

```typescript
// API base URL
const apiUrl = 'https://api.bitbadges.io/';

// LCD (Light Client Daemon) endpoint
const lcdUrl = 'https://lcd.bitbadges.io/';
```

## Quick Reference

### GitBook Syntax

```markdown
{% content-ref url="path" %}
[Text](path)
{% endcontent-ref %}

<figure>
  <img src="path" alt="alt">
  <figcaption>Caption</figcaption>
</figure>
````

### Common Code Patterns

```typescript
// Time range
const FullTimeRanges = [{ start: '1', end: '18446744073709551615' }];

// Token ID range
const tokenIds = [{ start: '1', end: '100' }];

// Address
const address = 'bb1kj9kt5y64n5a8677fhjqnmcc24ht2vy9atmdls';
```
