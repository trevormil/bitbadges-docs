# Standards

Collections can optionally implement a specific type of collection or standard. Standards define the expected format of the collection and help others to know how to interpret the details of the collection.

Standards can define the expected values and format of everything about a collection, such as its expected metadata format or the expected genesis conditions. If you implement a standard, it is your responsibility to follow the rules defined by the standard.

Choose the most appropriate standard(s) for your desired use case. You may choose to mix and match more than one as long as they are compatible.

**Example**

```
standards: ["non-fungible", "attendance-event", "No User Ownership"]
```

## List of Standards

1. "No User Ownership" standard - If the standards contains the standard "No User Ownership", then, user ownership is deemed unimportant and all user balances are to not be displayed. This means nothing about transferability, approvals, activity, and so on is displayed. This standard is used by collections where only the token metadata / permissions matter, such as an attestation to something. All tokens are expected to not have any recipient.

