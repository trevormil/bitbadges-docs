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

2. "AI Agent Vault" standard - This is a **display-only** standard that has no impact on on-chain logic, approvals, or permissions. When a Smart Token collection includes this standard, the BitBadges frontend will show an additional "AI Prompt" tab on the token view page. This tab generates a ready-to-use text prompt that can be copied and provided to an AI agent, containing all the vault details (collection ID, backing address, denomination, spend limits, and deposit/withdraw instructions). Use this standard when the token is intended to be managed by an AI agent. It is typically combined with the "Smart Token" standard.

   ```
   standards: ["Smart Token", "AI Agent Vault"]
   ```

