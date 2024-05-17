# Codes

### Plugin ID: `codes`

This plugin manages a code challenge, where users must provide a valid one-time-use code to claim. Note that codes are assigned a zero-based index and we keep track of which indices have been used. This allows for the code to be rotated and consistency with on-chain approvals. Thus, note that the&#x20;

### Public Parameters

* **numCodes**: The total number of codes that can be generated or used. Unless in edge cases, this should match the total possible number of claims.

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
