# 🖼️ Metadata

Collections can define metadata for both the collection and individual badges via their **collectionMetadataTimeline** and **badgeMetadataTimeline,** respectively. Both will follow the same metadata interface, as explained here. For the **badgeMetadataTimeline,** we take first match of a specific badge ID via linear scan, so the .**badgeMetadata\[]** order matters.

{% content-ref url="broken-reference" %}
[Broken link](broken-reference)
{% endcontent-ref %}

Within the collection interface, this would look like:

<pre class="language-json"><code class="lang-json"><strong>"collectionMetadataTimeline": [
</strong>  {
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
</code></pre>

#### **Expected Interface (BitBadges API / Indexer / Site)**

By default, we expect the collection and badge metadata to follow the format shown below for the BitBadges indexer / API. However, this is only a default expected format, and we envision that many different metadata standards can develop and be supported over time.

Using {id} Placeholder - If the badge metadata URI includes "{id}" anywhere in the URI, it is expected to be dynamically replaced by the corresponding badge ID number. For example: `"...abc.com/metadata/{id}"` becomes `"...abc.com/metadata/1"` for badge ID 1 (see [Compatibility](../bitbadges-api/concepts/designing-for-compatibility.md) for more info).

Markdown - Markdown is supported for descriptions.

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
  
  //If a collection hosts balances off-chain, you can specify details about where they are hosted and how they are assigned
  offChainTransferabilityInfo?: {
    host: string
    assignMethod: string
  }
}
```

