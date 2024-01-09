# 🖼 Metadata

Collections can define metadata for both the collection and individual badges via their **collectionMetadataTimeline** and **badgeMetadataTimeline,** respectively**.**&#x20;

Both will follow the same metadata interface, as explained here.&#x20;

{% content-ref url="../../overview/how-it-works/metadata.md" %}
[metadata.md](../../overview/how-it-works/metadata.md)
{% endcontent-ref %}

**Using {id} Placeholder**

If the badge metadata URI includes "{id}" anywhere in the URI, it is expected to be dynamically replaced by the corresponding badge ID number. For example: `"...abc.com/metadata/{id}"` becomes `"...abc.com/metadata/1"` for badge ID 1 (see [Compatibility](../bitbadges-api/designing-for-compatibility.md) for more info).

**Markdown**

Markdown is supported for descriptions.

#### **Expected Interface (BitBadges API / Indexer)**

By default, we expect the collection and badge metadata to follow the format shown below for the BitBadges indexer / API. However, this is only a default expected format, and we envision that many different metadata standards can develop and be supported over time.

```typescript
export interface Metadata<T extends NumberType> {
  name: string;
  description: string;
  image: string;
  video?: string;
  validFrom?: UintRange<T>[];
  category?: string;
  externalUrl?: string;
  video?: string
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
