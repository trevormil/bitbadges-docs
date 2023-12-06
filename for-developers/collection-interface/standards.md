# 🖊 Standards

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

### Adding New Standards

We want to decentralize the process of creating new standards and open it up to everyone. The comprehensive list of standards can be found on this page below. If you would like to author a new standard and have it added, contact us.

### Format

Each standard must follow the following format:

**Author:** John Doe

**ID: "..."**

**Version:** 0.0.1

**Standard Details:** The standard details outline the expected functionality of badges who implement this standard.

## **Compatibility with BitBadges API / Indexer**

Note the BitBadges API / Indexer expects certain formatting and interfaces to be followed to be compatible. See [BitBadges API Compatibility](../bitbadges-api/compatibility.md). If you want compatibility, please make sure all standards are compatible.

## List of Standards

1.  "No Balances" standard - If the standards array contains the standard "No Balances", then, the balances for all badges in the collection are deemed unimportant and are to not be displayed. This means nothing about transferability, approvals, activity, and so on is displayed. This standard is used by collections where only the badge metadata / permissions matter, such as an attestation to something. All badges are expected to not have any recipient.

