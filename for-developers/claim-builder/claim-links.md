# Claim Links

There are two main methods for users to claim. This is only applicable if users are claiming in-site. If you are auto-claiming on behalf of users, this is not necessary.

### 1. Direct Navigation

Users can navigate directly to the claim page and enter everything manually into the forms.

### 2. Redirect with Claim Link

Users can be redirected using a claim link that auto-fills certain details for them.

#### Auto-filled Details

When using the claim link method, the following details can be auto-filled:

* `code`: The unique code for the Codes plugin (assumes only one code plugin)
* `password`: A password for the Passwords plugin (assumes only one password plugin)
* `claimId`: The unique identifier for the claim. This lets us know which one to highlight.
* `customBody`: The preconfigured JSON encoded claim body for the user. See [here for more information](../bitbadges-api/tutorials/completing-claims.md).  Note that you will have to URL encode it.

https://bitbadges.io/collections/1?code=ABC123\&password=securepass\&claimId=98765\&customBody={"exampleInstanceId":%20"abc123"}
