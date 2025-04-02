# Getting Started

## Getting Started - API Keys

By default, certain routes are available publicly in a rate limited manner with no API key. However, API keys allow you access to all routes with higher limits.

1. Sign in on and create an API key by going to [https://bitbadges.io/developer](https://bitbadges.io/developer) -> API Keys tab.
2. Start sending requests to the base URL of [https://api.bitbadges.io/](https://api.bitbadges.io/) with the HTTP header x-api-key. All routes require an API key.
3. For higher tiers / paid plans, visit [https://bitbadges.io/pricing](https://bitbadges.io/pricing). To actually upgrade, see the Upgrading an API Key Tier demo on the next page.

<figure><img src="../../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

## Quickstarter

See the [quickstart repo](https://github.com/BitBadges/bitbadges-quickstart) for a fully configured example repository with fetching collections, accounts, and more with the API!

## Number Types

Note: Numbers are stringified in responses to avoid precision loss. You will have to convert them. The SDK does this for you if you use it.

## References

* [Main](https://bitbadges.stoplight.io/docs/bitbadges)
* [Postman](https://www.postman.com/bitbadges/workspace/bitbadges-api/collection/11647629-5bc57e3c-1818-4446-988e-23a9442cc0df?action=share\&creator=11647629)
* [OpenAPI](https://github.com/BitBadges/bitbadgesjs/blob/main/packages/bitbadgesjs-sdk/openapitypes/combined.yaml)

In this documentation, we often use the SDK format for explanation purposes. Please convert the corresponding function name to vanilla HTTP if you are not using the SDK from the documentation above.

```typescript
await BitBadgesApi.routeFn(...)
```

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

Note we also have a python auto-generated SDK wrapper for the API without any of the helper functions like the JS SDK has at [https://pypi.org/project/bitbadgespy-sdk/](https://pypi.org/project/bitbadgespy-sdk/)

## Using BitBadges JS/SDK

Check out the BitBadges JS/SDK for implementing further functionality beyond just API requests / responses, such as manipulating balances, handling approvals, checking permissions, etc.

{% content-ref url="../bitbadges-sdk/" %}
[bitbadges-sdk](../bitbadges-sdk/)
{% endcontent-ref %}

## API Authorization

For most applications, you should be fine without needing to access private user authenticated information. However if you do, check out Sign In with BitBadges. This follows a standard OAuth 2.0 flow. Use the scopes to gain access to authenticated routes. Refer to the API reference to see what scopes are needed where.

{% content-ref url="../authenticating-with-bitbadges/" %}
[authenticating-with-bitbadges](../authenticating-with-bitbadges/)
{% endcontent-ref %}

## Bookmarking

Throughout the API, we use a bookmark technique. For the first request, you will not need to specify a bookmark (e.g. ""), and it will fetch the first page. Within the response, it will return a **bookmark** and **hasMore**. **hasMore** defines whether there are more pages to be fetched. To fetch the next page, you will specify the returned bookmark from the previous request to the next request. This process can be repeated until all are loaded.
