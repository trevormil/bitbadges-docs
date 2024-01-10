# Max Number of Transfers

Pre-Readings (IMPORTANT): [Approval Trackers](approval-trackers.md)

```typescript
export interface ApprovalCriteria<T extends NumberType> {
  ...
  maxNumTransfers?: MaxNumTransfers<T>;
  ...
}
```

Similar to approval amounts, you can also specify the maximum number of transfers that can occur on an overall or per sender/recipient/initiatedBy basis. If tracked, the corresponding approval tracker will be incremented by 1 each transfer.&#x20;

"0" means unlimited allowed and not tracked. "N" means max N transfers allowed.

**Example**

Let's say we have the ID `1-collection- -uniqueID-initiatedBy-alice` and the defined values below:

<pre class="language-json"><code class="lang-json">"maxNumTransfers": {
<strong>    "overallMaxNumTransfers": "0",
</strong>    "perFromAddressMaxNumTransfers": "0",
    "perToAddressMaxNumTransfers": "0",
    "perInitiatedByAddressMaxNumTransfers": "1"
}
</code></pre>

The first transfer initiated by Alice would increment the approval tracker ID`1-collection- -uniqueID-initiatedBy-alice` to 1/1 transfers used. Alice can no longer initiate another transfer.

However, Bob can still transfer because his ID is `1-collection- -uniqueID-initiatedBy-bob` which is a different tracker.
