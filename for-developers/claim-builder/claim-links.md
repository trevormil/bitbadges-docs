# Claim Links (URLs)

If users claim in-site, simply direct them to the claim page, such as the following:

[https://bitbadges.io/claims/ad831a95b20ab0aeaa1695ae8ada7bb6](https://bitbadges.io/claims/ad831a95b20ab0aeaa1695ae8ada7bb6)

Or if it is tied with an application, address list, or badge, direct them to the corresponding page there.

#### Auto-filled Details (Advanced)

The following details can be auto-filled for the user if specified in the query parameters.

* `code`: The unique code for the Codes plugin (assumes only one code plugin)
* `password`: A password for the Passwords plugin (assumes only one password plugin)
* `claimId`: The unique identifier for the claim. This lets us know which one to highlight.
* `customBody`: The preconfigured JSON encoded claim body for the user. See [here for more information](../bitbadges-api/tutorials/completing-claims.md).  Note that you will have to URL encode it.
* `approvalLevel`: "collection" | "incoming" | "outgoing". We scan for the claimId (collection level first) but for on-chain collections, you may have claims for user level approvals as well. This is used in the case of duplicate claimIds on different levels.

https://bitbadges.io/collections/1?code=ABC123\&password=securepass\&claimId=98765\&customBody={"exampleInstanceId":%20"abc123"}
