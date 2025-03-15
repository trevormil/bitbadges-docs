# 🖊️ Standards

Standards are a generic concept that allows anyone to define how to interpret the details of a badge collection. All collections will implement the same interface on the blockchain, but the standard defines how these fields are interpreted and are expected to be defined. Standards can define anything from the expected genesis conditions to the expected metadata format to any expected features of the collection.

```json
"standardsTimeline": [
  {
    "timelineTimes": [...],
    "standards": ["transferable", "text-only-metadata", "non-fungible", "attendance-format"]1
  }
]
```

You can define and implement multiple standards, as long as they are compatible. There is no check in the blockchain logic that a specific standard is actually followed. The value stored on the blockchain is purely informational and for guidelines. It is the querier's responsibility to double check standards are being followed correctly and take action accordingly.

## **Compatibility with BitBadges API / Indexer**

Note the BitBadges API / Indexer expects certain formatting and interfaces to be followed to be compatible. See [BitBadges API Compatibility](../bitbadges-api/concepts/designing-for-compatibility.md). If you want compatibility, please make sure all standards are compatible.

## List of Standards

See [here](../../overview/learn-the-basics/badge-concepts/standards.md).
