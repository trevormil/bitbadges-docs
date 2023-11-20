# 🖼 Metadata

Collections can define metadata for both the collection and individual badges via **collectionMetadataTimeline** and **badgeMetadataTimeline,** respectively**.**&#x20;

By default, we expect the collection and badge metadata as shown below for the BitBadges indexer / API. Also, if the badge metadata URI includes "{id}" anywhere in the URI, it will be dynamically replaced by the corresponding badge ID number. For example: `"...x.com/metadata/{id}"` (see [Compatibility](../indexer-api/compatibility.md) for more info).

However, this is only a default expected format, and we envision that many different metadata standards can form over time (identified using **standardsTimeline**).

```typescript
export interface Metadata<T extends NumberType> {
  name: string;                 // Name of the badge or collection
  description: string;          // Description of the badge or collection
  image: string;                // URL to the badge or collection image
  validFrom?: UintRange<T>;     // Valid from range (optional)
  category?: string;            // Category (optional)
  externalUrl?: string;         // External URL (optional)
  tags?: string[];              // Tags (optional)
}
```

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
],
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
        "uri": "ipfs://Qmf8xxN2fwXGgouue3qsJtN8ZRSsnoHxM9mGcynTPhh6Ub",
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
],
```
