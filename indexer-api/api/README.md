# API

See [https://github.com/BitBadges/bitbadges-frontend/blob/main/src/bitbadges-api/api.ts](https://github.com/BitBadges/bitbadges-frontend/blob/main/src/bitbadges-api/api.ts) for an example implementation of all API routes.

### Getting Started

1. TODO API Key?
2. Start sending requests to the base URL of [https://api.bitbadges.io/](https://api.bitbadges.io/) or another indexer.

### Authentication

For certain requests, we require the user to be authenticated via [Blockin](http://127.0.0.1:5000/o/7VSYQvtb1QtdWFsEGoUn/s/AwjdYgEsUkK9cCca5DiU/). If the user is not signed in, the API will respond with a 401 error code. See [Authentication](../concepts/authentication.md) for how to authenticate users.

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

## Routes

Auth Required = \*



**Blockin Auth:** Authenticate users using Blockin

* **POST /api/v0/auth/getChallenge - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetSignInChallengeRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetSignInChallengeRouteSuccessResponse.html)**)**
* **POST /api/v0/auth/verify - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/VerifySignInRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/VerifySignInRouteSuccessResponse.html)**)**
* **POST /api/v0/auth/logout - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/SignOutRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/SignOutSuccessResponse.html)**)**
* **POST /api/v0/auth/status - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/CheckSignInStatusRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/CheckSignInStatusRequestSuccessResponse.html)**)**

**Status:** Gets info about the status of the indexer / blockchain (gas prices, block height, etc).

* **POST /api/v0/status - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetStatusRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetStatusRouteSuccessResponse.html)**)**

**Search:** Search collections and accounts based on a search value.

* **POST /api/v0/search/:searchValue - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetSearchRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetSearchRouteSuccessResponse.html)**)**

**Collections:** Get details about badge collections.

* **POST /api/v0/collection/batch - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetCollectionBatchRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetCollectionBatchRouteSuccessResponse.html)**)**
* **POST /api/v0/collection/:collectionId - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetCollectionByIdRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetCollectionRouteSuccessResponse.html)**)**&#x20;
* **POST /api/v0/collection/:collectionId/:badgeId/owners - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetOwnersForBadgeRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetOwnersForBadgeRouteSuccessResponse.html)**)**
* **POST /api/v0/collection/:collectionId/metadata - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetMetadataForCollectionRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetMetadataForCollectionRouteSuccessResponse.html)**)**
* **POST /api/v0/collection/:collectionId/balance/:cosmosAddress - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetBadgeBalanceByAddressRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetBadgeBalanceByAddressRouteSuccessResponse.html)**)**
* **POST /api/v0/collection/:collectionId/:badgeId/activity - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetBadgeActivityRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetBadgeActivityRouteSuccessResponse.html)**)**
* **POST /api/v0/collection/:collectionId/refreshMetadata - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/RefreshMetadataRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/RefreshMetadataRouteSuccessResponse.html)**)**
* **POST /api/v0/collection/:collectionId/codes - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetAllCodesAndPasswordsRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetAllCodesAndPasswordsRouteSuccessResponse.html)**)\***
* **POST /api/v0/collection/:collectionId/password/:cid/:password - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetMerkleChallengeCodeViaPasswordRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetMerkleChallengeCodeViaPasswordRouteSuccessResponse.html)**)\***
* **POST /api/v0/collection/:collectionId/addAnnouncement - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/AddAnnouncementRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/AddAnnouncementRouteSuccessResponse.html)**)\***
* **POST /api/v0/collection/:collectionId/addReview - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/AddReviewForCollectionRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/AddReviewForCollectionRouteSuccessResponse.html)**)\***
* **POST /api/v0/browse - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetBrowseCollectionsRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetBrowseCollectionsRouteSuccessResponse.html)**)**
* **POST /api/v0/addressMappings - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetAddressMappingsRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetAddressMappingsRouteSuccessResponse.html)**)**
* **POST /api/v0/approvals - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetApprovalsRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetApprovalsRouteSuccessResponse.html)**)**
* **POST /api/v0/merkleChallenges - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetMerkleChallengeTrackersRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetMerkleChallengeTrackersRouteSuccessResponse.html)**)**

**Users:** Get and update details about users and their profile.

* **POST /api/v0/user/batch - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetAccountsRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetAccountsRouteSuccessResponse.html)**)**
* **POST /api/v0/user/updateAccount - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/UpdateAccountInfoRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/UpdateAccountInfoRouteSuccessResponse.html)**)\***
* **POST /api/v0/user/:addressOrUsername - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetAccountRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetAccountRouteSuccessResponse.html)**)**
* **POST /api/v0/user/:addressOrUsername/addReview - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/AddReviewForUserRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/AddReviewForUserRouteSuccessResponse.html)**)\***

**IPFS:** Upload files to IPFS.

* **POST /api/v0/addMetadataToIpfs - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/AddMetadataToIpfsRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/AddMetadataToIpfsRouteSuccessResponse.html)**)\***
* **POST /api/v0/addMerkleChallengeToIpfs - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/AddMerkleChallengeToIpfsRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/AddMerkleChallengeToIpfsRouteSuccessResponse.html)**)\***
* **POST /api/v0/addBalancesToIpfs - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/AddBalancesToIpfsRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/AddBalancesToIpfsRouteSuccessResponse.html)**)\***

**Broadcasting:** Broadcast and simulate blockchain transactions.

* **POST /api/v0/broadcast - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/types/BroadcastTxRouteSuccessResponse.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/types/BroadcastTxRouteSuccessResponse.html)**)**
* **POST /api/v0/simulate - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/types/SimulateTxRouteSuccessResponse.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/types/SimulateTxRouteSuccessResponse.html)**)**

**Fetch Metadata From Source:**

* **POST /api/v0/metadata - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/FetchMetadataDirectlyRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/FetchMetadataDirectlyRouteSuccessResponse.html)**)**

**Faucet:** Faucet for betanet airdrop.

* **POST /api/v0/faucet - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/types/GetTokensFromFaucetRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/types/GetTokensFromFaucetRouteSuccessResponse.html)**)\***





