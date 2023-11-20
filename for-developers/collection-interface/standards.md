# 🖊 Standards

Standards are a generic concept that allows anyone to define how to interpret the details of a badge collection. All collections will implement the same interface on the blockchain (see [Collections](broken-reference)), but the standard defines how these fields are interpreted and are expected to be defined. Standards can define anything from the expected genesis conditions to the expected metadata format to any expected features of the collection.

```json
"standardsTimeline": [
  {
    "timelineTimes": [...],
    "standards": ["transferable", "text-only-metadata", "non-fungible", "attendance-format"]1
  }
]
```

You can define and implement multiple standards, as long as they are compatible. There is no check in the blockchain logic that a specific standard is actually followed. The value stored on the blockchain is purely informational and for guidelines. It is the querier's responsibility to double check standards are being followed correctly and take action accordingly.

### Adding New Standards

We want to decentralize the process of creating new standards and open it up to everyone. The comprehensive list of standards can be found on this page below. If you would like to author a new standard and have it added, contact us.

### Format

Each standard must follow the following format:

**Author:** John Doe

**ID: "..."**

**Version:** 0.0.1

**Standard Details:** The standard details outline the expected functionality of badges who implement this standard.

## **Compatibility with BitBadges API / Indexer**

Note the BitBadges API / Indexer expects certain formatting and interfaces to be followed to be compatible. See [BitBadges API Compatibility](../indexer-api/compatibility.md). If you want compatibility, please use standards that are compatible.

## Standards Ideas

Below are some ideas for standards that we have had. Feel free to formalize them into official ones.

* Custom Metadata Interfaces
  * Text-Based Metadata Standard (as opposed to images)
  * Attendance Event Metadata (e.g. has startTime, endTime, etc)
* URI standard for pointing to smart contracts (even on other chains)&#x20;
  * Ex: Contract address points to ETH:0x123....xyz contract on Ethereum Chain
  * Can also add an extension where it points to a specific function to query
* Different JSON standards for off-chain balance maps
  * Query dynamically instead of all in one file - i.e. {id}/{cosmosAddress}
  * Balance -> Addresses\[] JSON instead of Address -> Balance JSON
  * Use Address Mapping IDs instead of individual addresses

## List of Standards

...
