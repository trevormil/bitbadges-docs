# Codes

### Plugin ID: `codes`

This plugin manages a code challenge, where users must provide a valid one-time-use code to claim. Note that codes are assigned a zero-based index and we keep track of which indices have been used. This allows for the code to be rotated and consistency with on-chain approvals. Thus, note that the code value changing does not mean its used status changes.

We also support specific claim numbers dependent on the zero-based codeIdx using the assignMethod == instanceId.

{% content-ref url="../universal-approach-claim-codes.md" %}
[universal-approach-claim-codes.md](../universal-approach-claim-codes.md)
{% endcontent-ref %}

### Public Parameters

* **numCodes**: The total number of codes that can be generated or used. Unless in edge cases, this should match the total possible number of claims.
* **hideCurrentState**: If true, we will NOT reveal the state to users by default.&#x20;
  * If you are claim creator / authorized viewer, use the fetch private parameters flag and it will return the state.
  * The **publicState** will just be an empty {} by default.
  * Note that this hides it within the context of the claim, but if the claim action is public (e.g. public badge assignment, public lists), the state may still be leaked there.

### Private Parameters

* **codes**: An array of valid codes that users can provide.
* **seedCode**: A seed used to generate a list of valid codes.

### State

State is maintained by tracking which codes have been used.

```json
{
  "usedCodeIndices": {
    "0": 1,
    "1": 1
  }
}
```

#### Default State

The default state of the plugin is defined as follows:

```json
{
  "usedCodeIndices": {}
}
```

#### Public State

State is made public as-is, showing the indices of used codes.

Note: For the **publicState**, we pre-parse the indices into an array (e.g. \['0', '1']) instead fof returning the { "0": 1 } object.
