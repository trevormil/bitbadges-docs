# API

### Getting Started

1. Request an API Key by contacting us via Discord.
2. Start sending requests to the base URL of [https://api.bitbadges.io/](https://api.bitbadges.io/) with the HTTP header x-api-key.

<figure><img src="../../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

### Status Codes

We use standard HTTP error codes. 200 is the success code. All errors should follow the [ErrorResponse](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/ErrorResponse.html) type which defines a human-readable message via **message**.

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
  message: string;
  //Authentication error. Present if the user is not authenticated.
  unauthorized?: boolean;
}
```

***

### Pre-Readings

We recommend reading all [concepts](concepts/) for background information. Also visit the [Tutorials](../tutorials/) for common execution flows.

### Number Types / Stringified Responses

All requests / responses are stringified before being sent over HTTP. This is to avoid losing precision with big numbers > JavaScript's MAX\_SAFE\_INTEGER.  For how to convert the responses to your desired NumberType (bigint, JS number, etc), please see [Number Type Conversions](../bitbadges-sdk/common-snippets/numbertype-conversions.md) from the SDK.

We recommend using the JavaScript bigint type.

Note: this is all handled for you if you use the BitBadges API SDK.

### Using the API SDK (Recommended)

If you are using JavaScript / TypeScript, consider using the typed API SDK for convenience. This will give you typed routes, provide quality checks, and also auto-convert all responses to your desired number type (bigint, Number, etc).

```typescript
import { BigIntify, BitBadgesAPI } from 'bitbadgesjs-utils';

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

await BitBadgesApi.getStatus()
await BitBadgesApi.getOwnersForBadge(collectionId, badgeId, requestBody)
await BitBadgesApi.getCollections(...)
await BitBadgesApi.getAccounts(...)
//And so on for all routes....
```

Check out [https://github.com/BitBadges/bitbadges-frontend/blob/main/src/bitbadges-api/api.ts](https://github.com/BitBadges/bitbadges-frontend/blob/main/src/bitbadges-api/api.ts) for a working reference of the API routes using the SDK.

### Using BitBadges JS/SDK

Check out the BitBadges JS/SDK for implementing further functionality beyond just API requests / responses, such as manipulating balances, handling approvals, checking permissions, etc.

{% content-ref url="../bitbadges-sdk/" %}
[bitbadges-sdk](../bitbadges-sdk/)
{% endcontent-ref %}

### Authentication

For accessing certain private information or performing certain actions, we require for some requests that the user to be authenticated via [Blockin](https://app.gitbook.com/o/7VSYQvtb1QtdWFsEGoUn/s/AwjdYgEsUkK9cCca5DiU/). Blockin is a free-to-use, decentralized, universal sign-in standard for all of Web 3.0 that can support signing in with all blockchains! It was created and is maintained by the BitBadges core development team.

If the user is not signed in, the API will respond with a 401 error code. See [Authentication](tutorials/authentication.md) for how to authenticate users.

### Confined Responses

**IMPORTANT**: Remember that each retrieval is confined to what is stipulated in the query options. It is your responsibility to append the data to your previous responses as you need. The [Tutorials ](tutorials/)and [Concepts ](concepts/)will be extremely beneficial to help you deal with this.

**Bookmarking**

Throughout the API, we use a bookmark technique. For the first request, you will not need to specify a bookmark (e.g. ""), and it will fetch the first page. Within the response, it will return a **bookmark** and **hasMore**. **hasMore** defines whether there are more pages to be fetched. To fetch the next page, you will specify the returned bookmark from the previous request to the next request. This process can be repeated until all are loaded.

## [Routes](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/classes/BitBadgesAPI.html)

See all documentation for routes [here](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/classes/BitBadgesAPI.html). The TypeScript body / response type definitions can be found [here](https://github.com/BitBadges/bitbadgesjs/blob/main/packages/utils/src/types/api.ts).

## Tutorials

{% content-ref url="tutorials/" %}
[tutorials](tutorials/)
{% endcontent-ref %}
