# API

We recommend using [https://github.com/BitBadges/bitbadges-frontend/blob/main/src/bitbadges-api/api.ts](https://github.com/BitBadges/bitbadges-frontend/blob/main/src/bitbadges-api/api.ts) for an example implementation of all API routes.

### Getting Started

1. TODO API Key?
2. Start sending requests to the base URL of [https://api.bitbadges.io/](https://api.bitbadges.io/) or another indexer.

### Authentication

For certain requests, we require the user to be authenticated via [Blockin](http://127.0.0.1:5000/o/7VSYQvtb1QtdWFsEGoUn/s/AwjdYgEsUkK9cCca5DiU/). If the user is not signed in, the API will respond with a 401 error code. See [Authentication](tutorials/authentication.md) for how to authenticate users.

### Status Codes

We attempt to use typical HTTP error codes. 200 is the typical success code. All errors should follow the [ErrorResponse](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/ErrorResponse.html) type which defines a human-readable message via **message**.

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

We recommend reading all [concepts](concepts/) for background information on the API but especially the following:

* [Authentication](tutorials/authentication.md)
* [Number Types / Stringified Responses](concepts/number-types.md)
* [Paginations](concepts/paginations.md)

### Using the SDK

We recommend using the SDK for all requests, routes, and responses. All are exported for your convenience such as:

```typescript
import { GetStatusRouteRequestBody, GetStatusRouteSuccessResponse, GetStatusRoute } from 'bitbadgesjs-utils';

export async function getStatus(requestBody?: GetStatusRouteRequestBody): Promise<GetStatusRouteSuccessResponse<DesiredNumberType>> {
  try {
    const response = await axios.post<GetStatusRouteSuccessResponse<string>>(`${BACKEND_URL}${GetStatusRoute()}`, requestBody);
    return convertGetStatusRouteSuccessResponse(response.data, ConvertFunction);
  } catch (error) {
    await handleApiError(error);
    return Promise.reject(error);
  }
}
```

## Routes

Auth Required = \*



### **Blockin Auth**

**POST /api/v0/auth/getChallenge - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetSignInChallengeRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetSignInChallengeRouteSuccessResponse.html)**)**

Gets Blockin challenge to be signed by the user.

**POST /api/v0/auth/verify - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/VerifySignInRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/VerifySignInRouteSuccessResponse.html)**)**

Submit the signed Blockin challenge to this route to be authenticated.

**POST /api/v0/auth/logout - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/SignOutRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/SignOutSuccessResponse.html)**)**

Logout and remove all session cookies.

**POST /api/v0/auth/status - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/CheckSignInStatusRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/CheckSignInStatusRequestSuccessResponse.html)**)**

Check the health of your sign in.



See [Authentication](tutorials/authentication.md) for tutorial.



### **Status**

**POST /api/v0/status - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetStatusRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetStatusRouteSuccessResponse.html)**)**

Gets info about the status of the indexer / blockchain (gas prices, block height, etc).

###

### **Search**

**POST /api/v0/search/:searchValue - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetSearchRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetSearchRouteSuccessResponse.html)**)**

Search collections and accounts based on a search value.



### **Collections**

See [Collections Tutorial](tutorials/collections-tutorials.md) for how to deal with the paginated response, metadata fetches, etc.



**POST /api/v0/collection/batch - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetCollectionBatchRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetCollectionBatchRouteSuccessResponse.html)**)**

Batch fetch details about multiple collections.

**POST /api/v0/collection/:collectionId - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetCollectionByIdRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetCollectionRouteSuccessResponse.html)**)**&#x20;

Gets a single collection.

**POST /api/v0/collection/:collectionId/:badgeId/owners - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetOwnersForBadgeRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetOwnersForBadgeRouteSuccessResponse.html)**)**

Gets badge owners for a specific badge in a collection.

**POST /api/v0/collection/:collectionId/metadata - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetMetadataForCollectionRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetMetadataForCollectionRouteSuccessResponse.html)**)**

Gets metadata for a collection.

**POST /api/v0/collection/:collectionId/balance/:cosmosAddress - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetBadgeBalanceByAddressRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetBadgeBalanceByAddressRouteSuccessResponse.html)**)**

Gets badge balances for a specific address.

**POST /api/v0/collection/:collectionId/:badgeId/activity - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetBadgeActivityRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetBadgeActivityRouteSuccessResponse.html)**)**

Gets badge activity for a speciifc badge.

**POST /api/v0/collection/:collectionId/refreshMetadata - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/RefreshMetadataRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/RefreshMetadataRouteSuccessResponse.html)**)**

Trigger a metadata refresh of the collection. Includes both metadata and balances. Limit once per 60 seconds.

**POST /api/v0/collection/:collectionId/codes - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetAllCodesAndPasswordsRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetAllCodesAndPasswordsRouteSuccessResponse.html)**)\***

Get the secret codes for a collection's claim. Manager only.

**POST /api/v0/collection/:collectionId/password/:cid/:password - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetMerkleChallengeCodeViaPasswordRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetMerkleChallengeCodeViaPasswordRouteSuccessResponse.html)**)\***

Returns the user with a new unused code, if they provide a correct password.

**POST /api/v0/collection/:collectionId/addAnnouncement - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/AddAnnouncementRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/AddAnnouncementRouteSuccessResponse.html)**)\***

Adds an announcement for the collection. Manager only. Will show up in the returned announcements of all users who own a badge.

**POST /api/v0/collection/:collectionId/addReview - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/AddReviewForCollectionRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/AddReviewForCollectionRouteSuccessResponse.html)**)\***

Adds a review for the collection.

**POST /api/v0/browse - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetBrowseCollectionsRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetBrowseCollectionsRouteSuccessResponse.html)**)**

Returns features, latest, etc. collections to be displayed on a browse page.

**POST /api/v0/addressMappings - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetAddressMappingsRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetAddressMappingsRouteSuccessResponse.html)**)**

Gets address mappings by mapping ID. Note for reserved mapping IDs, you can use **getReservedAddressMappings** from the SDK.

**POST /api/v0/approvals - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetApprovalsRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetApprovalsRouteSuccessResponse.html)**)**

Gets current approvals (includes collection level approvals). This returns how many transfers, amounts, etc of the approval have been used (if applicable).

**POST /api/v0/merkleChallenges - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetMerkleChallengeTrackersRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetMerkleChallengeTrackersRouteSuccessResponse.html)**)**

Gets merkle challenge trackers (including for collections). This returns how many claims have been processed, used leaf indices, etc of the challenge have been used (if applicable).



### **Users**

Note that usernames are under development so **addressOrUsername** only corresponds to addresses currently. Addresses must be well formatted but can be in the format of any chain (we handle conversions to / from Cosmos addresses).



See [Users Tutorial](tutorials/user-tutorials.md) for how to deal with the paginated response, etc.



**POST /api/v0/user/batch - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetAccountsRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetAccountsRouteSuccessResponse.html)**)**

Get and update details about users and their profiles. Batch fetch.

**POST /api/v0/user/updateAccount - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/UpdateAccountInfoRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/UpdateAccountInfoRouteSuccessResponse.html)**)\***

Updates a user's profile document in the indexer (GitHub, Discord, lastSeenActivity timestamp etc).

**POST /api/v0/user/:addressOrUsername - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetAccountRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetAccountRouteSuccessResponse.html)**)**

Gets a single user's details.

**POST /api/v0/user/:addressOrUsername/addReview - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/AddReviewForUserRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/AddReviewForUserRouteSuccessResponse.html)**)\***

Add a review for the specified user.



### **IPFS**

Upload files to IPFS. 100 MB per user cumulative limit.

**POST /api/v0/addMetadataToIpfs - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/AddMetadataToIpfsRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/AddMetadataToIpfsRouteSuccessResponse.html)**)\***

**POST /api/v0/addMerkleChallengeToIpfs - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/AddMerkleChallengeToIpfsRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/AddMerkleChallengeToIpfsRouteSuccessResponse.html)**)\***

**POST /api/v0/addBalancesToIpfs - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/AddBalancesToIpfsRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/AddBalancesToIpfsRouteSuccessResponse.html)**)\***



### **Broadcasting**

Broadcast and simulate blockchain transactions to the official BitBadges node. Also see https://bitbadges.io/dev/broadcast if you simply want to copy and paste your transaction to a UI. All signing, API communication, etc is outsourced to the UI.

**POST /api/v0/broadcast - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/types/BroadcastTxRouteSuccessResponse.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/types/BroadcastTxRouteSuccessResponse.html)**)**

**POST /api/v0/simulate - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/types/SimulateTxRouteSuccessResponse.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/types/SimulateTxRouteSuccessResponse.html)**)**



### **Fetch Metadata From Source**

**POST /api/v0/metadata - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/FetchMetadataDirectlyRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/FetchMetadataDirectlyRouteSuccessResponse.html)**)**

Fetches metadata directly from the source URI, rather than from our indexer cache.



### **Faucet**

**POST /api/v0/faucet - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/types/GetTokensFromFaucetRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/types/GetTokensFromFaucetRouteSuccessResponse.html)**)\***

Faucet to get tokens from our betanet faucet. 1 airdrop of $1000 BADGE per address.



