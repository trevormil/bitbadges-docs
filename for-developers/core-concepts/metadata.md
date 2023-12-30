# 🖼 Metadata

Collections can define metadata for both the collection and individual badges via **collectionMetadataTimeline** and **badgeMetadataTimeline,** respectively**.**&#x20;

#### **Expected Format (BitBadges API / Indexer)**

By default, we expect the collection and badge metadata to follow the format shown below for the BitBadges indexer / API. Also, if the badge metadata URI includes "{id}" anywhere in the URI, it will be dynamically replaced by the corresponding badge ID number. For example: `"...x.com/metadata/{id}"` becomes `"...x.com/metadata/1"` for badge ID 1 (see [Compatibility](../bitbadges-api/compatibility.md) for more info).

For images, we display them in circular format with a maximum size of 200 x 200 on the BitBadges website.

However, this is only a default expected format, and we envision that many different metadata standards can develop and be supported over time.

```typescript
export interface Metadata<T extends NumberType> {
  name: string;
  description: string;
  image: string;
  validFrom?: UintRange<T>[];
  category?: string;
  externalUrl?: string;
  tags?: string[];

  socials?: {
    [key: string]: string;
  }
  
  //If a collection hosts balances off-chain, you can specify details about where they are hosted and how they are assigned
  offChainTransferabilityInfo?: {
    host: string
    assignMethod: string
  }
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
