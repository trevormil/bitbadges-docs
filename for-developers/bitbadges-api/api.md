# API

## Getting Started - API Keys

By default, certain routes are available publicly in a rate limited manner with no API key. However, API keys allow you access to all routes with higher limits.

1. Sign in on and create an API key by going to [https://bitbadges.io/developer](https://bitbadges.io/developer) -> API Keys tab.
2. Start sending requests to the base URL of [https://api.bitbadges.io/](https://api.bitbadges.io/) with the HTTP header x-api-key. All routes require an API key.
3. For higher tiers / paid plans, visit [https://bitbadges.io/pricing](https://bitbadges.io/pricing). To actually upgrade, see the Upgrading an API Key Tier demo on the next page.

<figure><img src="../../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

## Quickstarter

See the [quickstart repo](https://github.com/BitBadges/bitbadges-quickstart) for a fully configured example repository with fetching collections, accounts, and more with the API!

## Number Types / Stringified Responses

All requests / responses are stringified before being sent over HTTP to avoid losing precision with big numbers > JavaScript's MAX_SAFE_INTEGER. For how to convert the responses to your desired NumberType (bigint, JS number, etc), please see [Number Type Conversions](../bitbadges-sdk/common-snippets/numbertype-conversions.md) from the SDK. We recommend using the JavaScript bigint type.

Note: this is all handled for you if you use the BitBadges API SDK.

## Routes Documentation

-   [Main](https://bitbadges.stoplight.io/docs/bitbadges)
-   [Postman](https://www.postman.com/bitbadges/workspace/bitbadges-api/collection/11647629-5bc57e3c-1818-4446-988e-23a9442cc0df?action=share&creator=11647629)
-   [OpenAPI](https://github.com/BitBadges/bitbadgesjs/blob/main/packages/bitbadgesjs-sdk/openapitypes/combined.yaml)

In this documentation, we often use the SDK format for explanation purposes

```typescript
await BitBadgesApi.routeFn(...)
```

Please convert the corresponding function name to vanilla HTTP if you are not using the SDK from the documentation above.

```
POST https://api.bitbadges.io/api/v0/routeFn
```

## Testnet Mode

A testnet version of the API is available with the base URL [https://api.bitbadges.io/testnet](https://api.bitbadges.io/testnet). Everything else is the same, just add the /testnet before all paths.

Note that this testnet API is an entirely separate service from normal API. Nothing carries over. It commmunicates with the frontend with testnet mode turned on and uses the testnet BitBadges blockchain.

## Using the API SDK (Recommended)

If you are using JavaScript / TypeScript, consider using the typed API SDK for convenience. This will give you typed routes, provide quality checks, and also auto-convert all responses to your desired number type (bigint, Number, etc).

```typescript
import { BigIntify, BitBadgesAPI } from 'bitbadgesjs-sdk';

export type DesiredNumberType = bigint;
export const ConvertFunction = BigIntify;

//BACKEND_URL for main API is https://api.bitbadges.io
//Make sure process.env.BITBADGES_API_KEY is set with a valid API key.

const BitBadgesApi = new BitBadgesAPI({
    apiKey: '...',
    //converts responses to your desired number type (bigint, Number, etc)
    convertFunction: ConvertFunction //Can also do Numberify, Stringify, etc
    apiUrl: '...' //defaults to official one if empty
});

//See https://bitbadges.github.io/bitbadgesjs/classes/BitBadgesAPI.html for documentation
//Some might require authentication. Some might be CORS only from the official site.
await BitBadgesApi.getAccounts(...);
await BitBadgesApi.getAddressLists(...);
await BitBadgesApi.getCollections(...);
await BitBadgesApi.simulateTx(...);
```

## Using BitBadges JS/SDK

Check out the BitBadges JS/SDK for implementing further functionality beyond just API requests / responses, such as manipulating balances, handling approvals, checking permissions, etc.

{% content-ref url="../bitbadges-sdk/" %}
[bitbadges-sdk](../bitbadges-sdk/)
{% endcontent-ref %}

## BitBadges API Authorization

For most applications, you should be fine without needing to access private user authenticated information. This is typically only needed by the official BitBadges frontend. However if you do:

### OAuth Authorization

Check out Sign In with BitBadges. This follows a standard OAuth 2.0 flow. This could be useful for performing actions on behalf of others.

{% content-ref url="../authenticating-with-bitbadges/" %}
[authenticating-with-bitbadges](../authenticating-with-bitbadges/)
{% endcontent-ref %}

### Password Self-Approve Method

If you want to perform authenticated operations on behalf of your **own account**, consider the flow below. We recognize that wallet signatures may be a bit difficult to sign in with, so we have designed this alternative.

The password approved sign in approach may be useful, for example, for programmatically creating attestations, completing claims, etc, without needing to directly interact with the site.

1. Set up an approved password sign in in your account settings with the desired scopes.
2. Sign in with:

```typescript
const { message } = await BitBadgesApi.getSignInChallenge(...);
const verificationRes = await BitBadgesApi.verifySignIn({
    message,
    signature: '', //Empty string
    password: '...'
})

//If successful, you can now perform authenticated requests for the approved scopes
//await BitBadgesApi.completeClaim(...)
```

Note this approach leverages HTTP session cookies as opposed to access tokens. Make sure your requests support them (e.g. for axios, { withCredentials: true }).

## Confined Responses

**IMPORTANT**: Remember that each retrieval is confined to what is stipulated in the query options. It is your responsibility to append the data to your previous responses as you need. The [Tutorials](tutorials/) and [Concepts](concepts/) will be extremely beneficial to help you deal with this.

### Bookmarking

Throughout the API, we use a bookmark technique. For the first request, you will not need to specify a bookmark (e.g. ""), and it will fetch the first page. Within the response, it will return a **bookmark** and **hasMore**. **hasMore** defines whether there are more pages to be fetched. To fetch the next page, you will specify the returned bookmark from the previous request to the next request. This process can be repeated until all are loaded.

## Status Codes

We use standard HTTP error codes. 200 is the success code. All errors should follow the [ErrorResponse](https://bitbadges.github.io/bitbadgesjs/interfaces/ErrorResponse.html) type.

```typescript
/**
 * If an error occurs, the response will be an ErrorResponse.
 *
 * 400 - Bad Request (e.g. invalid request body)
 * 401 - Unauthorized (e.g. invalid session cookie; must sign in with Blockin)
 * 500 - Internal Server Error
 *
 * @typedef {Object} ErrorResponse
 */
export interface ErrorResponse {
    //Serialized error object for debugging purposes. Advanced users can use this to debug issues.
    error?: any;
    //UX-friendly error message that can be displayed to the user. Always present if error.
    errorMessage: string;
    //Authentication error. Present if the user is not authenticated.
    unauthorized?: boolean;
}
```

## Tutorials

{% content-ref url="tutorials/" %}
[tutorials](tutorials/)
{% endcontent-ref %}
