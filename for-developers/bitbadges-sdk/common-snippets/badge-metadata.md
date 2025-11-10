# Token Metadata

#### Tutorial: Managing Token Metadata in TypeScript

**1. Introduction to `TokenMetadataDetails`**

The `TokenMetadataDetails` type captures comprehensive details about the metadata of a token. It contains fields such as token IDs (ranges), associated metadata, a URI, and custom data. This is what is used via the **cachedTokenMetadata** field from collection responses.

**2. Removing Metadata for Specific Token IDs**

To delete metadata associated with specific token IDs:

```typescript
const currentMetadata: TokenMetadataDetails<bigint>[] = [...]; // your current metadata array

const tokenIdsToRemove = UintRangeArray.From([
  { start: 5n, end: 10n }
];

const updatedMetadata = removeTokenMetadata(currentMetadata, tokenIdsToRemove);
console.log(updatedMetadata); // This will show metadata without the removed token IDs.
```

**3. Updating Metadata for Tokens**

If you wish to update specific token metadata in the token metadata details:

```typescript
const currentMetadata: TokenMetadataDetails<bigint>[] = [...]; // your current metadata array

const metadataToUpdate: TokenMetadataDetails<bigint> = new TokenMetadataDetails<bigint>({
  tokenIds: [{ start: 7n, end: 7n }],
  metadata: { /* your metadata details here */ },
  uri: "http://new-metadata-url.com", //Or 'Placeholder' or something else
  customData: "Some custom information",
});

const newMetadataArray = updateTokenMetadata(currentMetadata, metadataToUpdate);
console.log(newMetadataArray); // This will show the array with the updated metadata.
```

**4. Fetch Metadata Details for a Specific Token ID**

To retrieve metadata details for a particular token ID:

```typescript
const tokenIdToFetch = 12n;

const tokenMetadataDetails = getMetadataDetailsForTokenId(tokenIdToFetch, currentMetadata);
console.log(tokenMetadataDetails); // This will display the metadata details for the specified token ID.
```

**5. Fetch Only the Metadata for a Specific Token ID**

To only retrieve the metadata (without the surrounding details) for a particular token ID:

```typescript
const tokenIdToFetch = 15n;

const tokenMetadata = getMetadataForTokenId(tokenIdToFetch, currentMetadata);
console.log(tokenMetadata); // This will show only the metadata for the given token ID.
```

**Conclusion**

These functions provide a robust toolkit for managing token metadata. Whether you're updating, fetching, or deleting metadata associated with tokens, you have a systematic and structured approach available.
