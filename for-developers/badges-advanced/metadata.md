# Metadata

BitBadges allows defining metadata for both collections and individual badges using `collectionMetadataTimeline` and `badgeMetadataTimeline` respectively. Both timelines follow the same metadata interface, but with slight differences in implementation.

Other interfaces like lists, maps, etc will also have a corresponding metadata field that follows the same interface.

### Metadata Timelines

#### Collection Metadata Timeline

The `collectionMetadataTimeline` defines metadata for the entire collection over time.

Example:

```json
"collectionMetadataTimeline": [
  {
    "timelineTimes": [
      {
        "start": "1",
        "end": "18446744073709551615"
      }
    ],
    "collectionMetadata": {
      "uri": "ipfs://Qmf8xxN2fwXGgouue3qsJtN8ZRSsnoHxM9mGcynTPhh6Ub",
      "customData": ""
    }
  }
]
```

#### Badge Metadata Timeline

The `badgeMetadataTimeline` defines metadata for individual badges over time. The order of `badgeMetadata` entries matters, as it uses a first-match approach via linear scan for specific badge IDs.

Example:

```json
"badgeMetadataTimeline": [
  {
    "timelineTimes": [
      {
        "start": "1",
        "end": "18446744073709551615"
      }
    ],
    "badgeMetadata": [
      {
        "uri": "ipfs://Qmf8xxN2fwXGgouue3qsJtN8ZRSsnoHxM9mGcynTPhh6Ub/{id}",
        "badgeIds": [
          {
            "start": "1",
            "end": "10000000000000"
          }
        ],
        "customData": ""
      }
    ]
  }
]
```

### Metadata Interface

The BitBadges API, Indexer, and Site expect metadata to follow this format by default:

```typescript
export interface Metadata<T extends NumberType> {
  name: string;
  description: string;
  image: string;
  video?: string;
  category?: string;
  externalUrl?: string;
  tags?: string[];
  socials?: {
    [key: string]: string;
  }
  offChainTransferabilityInfo?: {
    host: string
    assignMethod: string
  }
}
```

#### Key Points:

1. **Dynamic Badge ID**: If the badge metadata URI includes `"{id}"`, it's replaced with the actual badge ID. For example, `"...abc.com/metadata/{id}"` becomes `"...abc.com/metadata/1"` for badge ID 1.
2. **Off-Chain Balances**: For collections hosting balances off-chain, you can specify details about where they are hosted and how they are assigned using the `offChainTransferabilityInfo` field.

### Best Practices

1. **Ordering**: For `badgeMetadataTimeline`, arrange entries from most specific to least specific, as the first match is used.
2. **URI Design**: Use the `{id}` placeholder in badge metadata URIs for dynamic content generation.
3. **Comprehensive Metadata**: Provide as much relevant information as possible in the metadata to enhance the user experience and discoverability of your badges.
4. **Time Ranges**: Ensure that your time ranges cover all possible times to avoid gaps where metadata might be undefined.
5. **Off-Chain Info**: If using off-chain balance management, clearly specify the host and assignment method in the `offChainTransferabilityInfo` field.

By following these guidelines and utilizing the flexible metadata structure provided by BitBadges, you can create rich, dynamic, and informative badge collections.
