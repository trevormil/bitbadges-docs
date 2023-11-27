# API

We recommend using [https://github.com/BitBadges/bitbadges-frontend/blob/main/src/bitbadges-api/api.ts](https://github.com/BitBadges/bitbadges-frontend/blob/main/src/bitbadges-api/api.ts) for an example implementation of all API routes.

### Getting Started

1. Request an API Key by contacting us via Discord.
2. Start sending requests to the base URL of [https://api.bitbadges.io/](https://api.bitbadges.io/) with the HTTP header x-api-key.

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

### Status Codes

We use typical HTTP error codes. 200 is the success code. All errors should follow the [ErrorResponse](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/ErrorResponse.html) type which defines a human-readable message via **message**.

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
* [Limits / Restrictions](limits-restrictions.md)

### Using the SDK

If you are using JavaScript / TypeScript, consider using the typed API SDK for convenience.

```typescript
import { BigIntify, BitBadgesAPI } from 'bitbadgesjs-utils';

export type DesiredNumberType = bigint;
export const ConvertFunction = BigIntify;

//BACKEND_URL for main API is https://api.bitbadges.io
//Make sure process.env.BITBADGES_API_KEY is set with a valid API key.

const BitBadgesApi = new BitBadgesAPI({
    apiKey: '...',
    convertFunction: ConvertFunction //Can also do Numberify, Stringify, etc
    apiUrl: '...' //defaults to official one if empty
}); 

await BitBadgesApi.getStatus()
await BitBadgesApi.getOwnersForBadge(collectionId, badgeId, requestBody)
//And so on...
```

See all documentation for routes [here](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/classes/BitBadgesAPI.html).

### Authentication

Blockin Authentication Required = \*

For certain requests, we require the user to be authenticated via [Blockin](http://127.0.0.1:5000/o/7VSYQvtb1QtdWFsEGoUn/s/AwjdYgEsUkK9cCca5DiU/). Blockin is a free-to-use, decentralized, universal sign-in standard for all of Web 3.0 that can support signing in with all blockchains! It was created and is maintained by the BitBadges core development team.

If the user is not signed in, the API will respond with a 401 error code. See [Authentication](tutorials/authentication.md) for how to authenticate users.

## Routes

See all documentation for routes [here](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/classes/BitBadgesAPI.html).

### **Authentication**

See [Authentication](tutorials/authentication.md) for tutorial.

#### **POST /api/v0/auth/getChallenge - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetSignInChallengeRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetSignInChallengeRouteSuccessResponse.html)**)**

Gets Blockin challenge to be signed by the user. The returned **blockinMessage** is the message to be signed by the user.

```typescript
export interface GetSignInChallengeRouteRequestBody {
  chain: SupportedChain,
  address: string,
  hours?: NumberType,
}
export interface GetSignInChallengeRouteSuccessResponse<T extends NumberType> {
  nonce: string,
  params: ChallengeParams<T>,
  blockinMessage: string,
}
```

#### **POST /api/v0/auth/verify - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/VerifySignInRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/VerifySignInRouteSuccessResponse.html)**)**

Submit the signed Blockin challenge to this route to be authenticated. See [Authentication Tutorial](tutorials/authentication.md).

```typescript
export interface VerifySignInRouteRequestBody {
  chain: SupportedChain,
  originalBytes: any
  signatureBytes: any
}
export interface VerifySignInRouteSuccessResponse<T extends NumberType> {
  success: boolean,
  successMessage: string,
}
```

#### **POST /api/v0/auth/logout - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/SignOutRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/SignOutSuccessResponse.html)**)**

Logout and remove all session cookies.

```typescript
export interface SignOutSuccessResponse<T extends NumberType> {
  success: boolean,
}
```

#### **POST /api/v0/auth/status - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/CheckSignInStatusRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/CheckSignInStatusRequestSuccessResponse.html)**)**

Check the health of your sign in.

```typescript
export interface CheckSignInStatusRequestSuccessResponse<T extends NumberType> {
  signedIn: boolean
}
```

### **Status**

#### **POST /api/v0/status - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetStatusRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetStatusRouteSuccessResponse.html)**)**

Gets info about the status of the indexer / blockchain (gas, block height, etc).

```typescript
export interface GetStatusRouteSuccessResponse<T extends NumberType> {
  status: StatusInfo<T>;
}
```

```typescript
export interface StatusInfoBase<T extends NumberType> {
  block: LatestBlockStatus<T>
  nextCollectionId: T;
  gasPrice: number;
  lastXGasLimits: T[];
  lastXGasAmounts: T[];
}
```

### **Search**

#### **POST /api/v0/search/:searchValue - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetSearchRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetSearchRouteSuccessResponse.html)**)**

Search collections, accounts, address lists based on a search value.

```typescript
export interface GetSearchRouteSuccessResponse<T extends NumberType> {
  collections: BitBadgesCollection<T>[],
  accounts: BitBadgesUserInfo<T>[],
  addressMappings: AddressMappingWithMetadata<T>[],
  badges: {
    badgeIds: UintRange<T>[],
    collection: BitBadgesCollection<T>,
  }[],
}
```

### **Collections**

See [Collections Tutorial](tutorials/fetching-collections.md) for how to deal with the paginated response, metadata fetches, etc.



#### **POST /api/v0/collection/batch - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetCollectionBatchRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetCollectionBatchRouteSuccessResponse.html)**)**

Batch fetch details about multiple collections.  See [Collections tutorial](tutorials/fetching-collections.md).

```typescript
export interface GetCollectionBatchRouteRequestBody {
  collectionsToFetch: ({ collectionId: NumberType } & GetMetadataForCollectionRequestBody & GetAdditionalCollectionDetailsRequestBody)[],
}
```

```typescript
export interface GetCollectionBatchRouteSuccessResponse<T extends NumberType> {
  collections: BitBadgesCollection<T>[]
}
```

#### **POST /api/v0/collection/:collectionId - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetCollectionByIdRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetCollectionRouteSuccessResponse.html)**)**&#x20;

Gets a single collection.

```typescript
export interface GetCollectionRouteSuccessResponse<T extends NumberType> {
  collection: BitBadgesCollection<T>,
}
```

#### **POST /api/v0/collection/:collectionId/:badgeId/owners - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetOwnersForBadgeRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetOwnersForBadgeRouteSuccessResponse.html)**)**

Gets badge owners for a specific badge in a collection.

```typescript
export interface GetOwnersForBadgeRouteRequestBody {
  bookmark?: string,
}
```

```typescript
export interface GetOwnersForBadgeRouteSuccessResponse<T extends NumberType> {
  owners: BalanceInfoWithDetails<T>[],
  pagination: PaginationInfo,
}
```

#### **POST /api/v0/collection/:collectionId/balance/:cosmosAddress - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetBadgeBalanceByAddressRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetBadgeBalanceByAddressRouteSuccessResponse.html)**)**

Gets badge balances for a specific address.

```typescript
export interface GetBadgeBalanceByAddressRouteSuccessResponse<T extends NumberType> {
  balance: BalanceInfoWithDetails<T>,
}
```

#### **POST /api/v0/collection/:collectionId/:badgeId/activity - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetBadgeActivityRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetBadgeActivityRouteSuccessResponse.html)**)**

Gets badge transfer activity for a speciifc badge.

```typescript
export interface GetBadgeActivityRouteRequestBody {
  bookmark?: string,
}
```

```typescript
export interface GetBadgeActivityRouteSuccessResponse<T extends NumberType> {
  activity: TransferActivityInfo<T>[],
  pagination: PaginationInfo,
}
```

#### **POST /api/v0/collection/:collectionId/refresh - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/RefreshMetadataRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/RefreshMetadataRouteSuccessResponse.html)**)**

Trigger a metadata refresh of the collection. Includes both metadata and balances. Note it will reject if recently refreshed.

```typescript
export interface RefreshMetadataRouteSuccessResponse<T extends NumberType> {
  successMessage: string,
}
```

#### **POST /api/v0/collection/:collectionId/refreshStatus - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/RefreshStatusRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/RefreshStatusRouteSuccessResponse.html)**)**

Get the status of a collection's refresh. Returns if the collection has anything in the refresh queue plus any error docs.

```typescript
export interface RefreshStatusRouteRequestBody<T extends NumberType> {
  collectionId: T,
}
```

```typescript
export interface RefreshStatusRouteSuccessResponse<T extends NumberType> {
  inQueue: boolean,
  errorDocs: QueueInfo<T>[],
}
```

#### **POST /api/v0/collection/:collectionId/codes - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetAllCodesAndPasswordsRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetAllCodesAndPasswordsRouteSuccessResponse.html)**)\***

Get the secret codes for a collection's claim. Manager only.

```typescript
export interface GetAllCodesAndPasswordsRouteSuccessResponse<T extends NumberType> {
  codesAndPasswords: CodesAndPasswords[],
}
```

#### **POST /api/v0/collection/:collectionId/password/:cid/:password - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetCodeForPasswordRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetCodeForPasswordRouteSuccessResponse.html)**)\***

Returns the user with a new unused code, if they provide the correct password.

```typescript
export interface GetCodeForPasswordRouteSuccessResponse<T extends NumberType> {
  code: string,
}
```

#### **POST /api/v0/collection/:collectionId/addAnnouncement - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/AddAnnouncementRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/AddAnnouncementRouteSuccessResponse.html)**)\***

Adds an announcement for the collection. Manager only. Will show up in the returned announcements of all users who own a badge.

```typescript
export interface AddAnnouncementRouteRequestBody {
  announcement: string, //1 to 2048 characters
}
```

```typescript
export interface AddAnnouncementRouteSuccessResponse<T extends NumberType> {
  success: boolean
}
```

#### **POST /api/v0/collection/deleteAnnouncement/:announcementId**

```typescript
export interface DeleteAnnouncementRouteSuccessResponse<T extends NumberType> {
  success: boolean
}
```

#### **POST /api/v0/collection/:collectionId/addReview - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/AddReviewForCollectionRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/AddReviewForCollectionRouteSuccessResponse.html)**)\***

Adds a review for the collection.

```typescript
export interface AddReviewForCollectionRouteRequestBody {
  review: string, //1 to 2048 characters
  stars: NumberType, //1 to 5
}
```

```typescript
export interface AddReviewForCollectionRouteSuccessResponse<T extends NumberType> {
  success: boolean
}
```

**POST /api/v0/deleteReview/:reviewId**&#x20;

```typescript
export interface DeleteReviewRouteRequestBody {
  reviewId: string
}
```

```typescript
export interface DeleteReviewRouteSuccessResponse<T extends NumberType> {
  success: boolean
}
```

#### **POST /api/v0/browse - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetBrowseCollectionsRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetBrowseCollectionsRouteSuccessResponse.html)**)**

Returns features, latest, etc. collections to be displayed on a browse page.

```typescript
export interface GetBrowseCollectionsRouteSuccessResponse<T extends NumberType> {
  collections: {
    [category: string]: BitBadgesCollection<T>[],
  },
  addressMappings: {
    [category: string]: AddressMappingWithMetadata<T>[],
  },
  profiles: {
    [category: string]: BitBadgesUserInfo<T>[],
  },
  activity: TransferActivityInfo<T>[],
}
```

####

#### **POST /api/v0/addressMappings - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetAddressMappingsRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetAddressMappingsRouteSuccessResponse.html)**)**

Gets address mappings by mapping ID. Note for reserved mapping IDs, you can use **getReservedAddressMappings** from the SDK.

```typescript
export interface GetAddressMappingsRouteRequestBody {
  mappingIds: string[],
  managerAddress?: string,
}
```

```typescript
export interface GetAddressMappingsRouteSuccessResponse<T extends NumberType> {
  addressMappings: AddressMappingWithMetadata<T>[],
}
```

#### **POST /api/v0/addressMappings/create - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/CreateAddressMappingsRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/CreateAddressMappingsRouteSuccessResponse.html)**)**

Creates off-chain address mappings stored on our centralized servers. For on-chain, they must be created through MsgCreateAddressMappings.

```typescript
export interface UpdateAddressMappingsRouteRequestBody {
  addressMappings: AddressMapping[],
}
```

#### **POST /api/v0/addressMappings/delete**

Only applicable to off-chain mappings. You must also be the creator.

```typescript
export interface DeleteAddressMappingsRouteRequestBody {
  mappingIds: string[],
}
```

#### **POST /api/v0/approvals - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetApprovalsRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetApprovalsRouteSuccessResponse.html)**)**

Gets current approvals (includes collection level approvals). This returns how many transfers, amounts, etc of the approval have been used (if applicable).

```typescript
export interface GetApprovalsRouteRequestBody {
  amountTrackerIds: AmountTrackerIdDetails<NumberType>[],
}
```

```typescript
export interface GetApprovalsRouteSuccessResponse<T extends NumberType> {
  approvalTrackers: ApprovalsTrackerInfo<T>[],
}
```

#### **POST /api/v0/challenges - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetMerkleChallengeTrackersRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetMerkleChallengeTrackersRouteSuccessResponse.html)**)**

Gets merkle challenge trackers (including for collections). This returns how many claims have been processed, used leaf indices, etc of the challenge have been used (if applicable).

```typescript
export interface GetMerkleChallengeTrackersRouteRequestBody {
  challengeTrackerIds: MerkleChallengeIdDetails<NumberType>[],
}
```

```typescript
export interface GetMerkleChallengeTrackersRouteSuccessResponse<T extends NumberType> {
  challengeTrackers: MerkleChallengeInfo<T>[],
}
```

### **Users**

Addresses must be well formatted but can be in the format of any chain.

See [Users Tutorial](tutorials/fetching-users.md) for how to deal with the paginated response, etc.



#### **POST /api/v0/user/batch - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetAccountsRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetAccountsRouteSuccessResponse.html)**)**

Get and update details about users and their profiles. Batch fetch.

```typescript
export interface GetAccountsRouteRequestBody {
  accountsToFetch: AccountFetchDetails[],
}
```

```typescript
export interface GetAccountsRouteSuccessResponse<T extends NumberType> {
  accounts: BitBadgesUserInfo<T>[],
}
```

#### **POST /api/v0/user/updateAccount - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/UpdateAccountInfoRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/UpdateAccountInfoRouteSuccessResponse.html)**)\***

Updates a user's profile document in the indexer (GitHub, Discord, lastSeenActivity timestamp etc).

```typescript
export interface UpdateAccountInfoRouteRequestBody<T extends NumberType> {
  discord?: string,
  twitter?: string,
  github?: string,
  telegram?: string,
  seenActivity?: NumberType,
  readme?: string,

  hiddenBadges?: {
    collectionId: T,
    badgeIds: UintRange<T>[],
  }[],

  customPages?: {
    title: string,
    description: string,
    badges: {
      collectionId: T,
      badgeIds: UintRange<T>[],
    }[]
  }[]

  profilePicUrl?: string
  username?: string

  profilePicImageFile?: any
}
```

```typescript
export interface UpdateAccountInfoRouteSuccessResponse<T extends NumberType> {
  success: boolean
}
```

#### **POST /api/v0/user/:addressOrUsername/addReview - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/AddReviewForUserRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/AddReviewForUserRouteSuccessResponse.html)**)\***

Add a review for the specified user.

```typescript
export interface AddReviewForUserRouteRequestBody {
  review: string, //1 to 2048 characters
  stars: NumberType, //1 to 5
}
```

```typescript
export interface AddReviewForUserRouteSuccessResponse<T extends NumberType> {
  success: boolean
}
```

**POST /api/v0/deleteReview/:reviewId**&#x20;

```typescript
export interface DeleteReviewRouteRequestBody {
  reviewId: string
}
```

```typescript
export interface DeleteReviewRouteSuccessResponse<T extends NumberType> {
  success: boolean
}
```



### **Broadcasting**

Broadcast and simulate blockchain transactions to the official BitBadges node. See this [tutorial](../bitbadges-sdk/common-snippets/creating-signing-and-broadcasting-txs.md) for more information.

Also, check out [https://bitbadges.io/dev/broadcast](https://bitbadges.io/dev/broadcast), so you can simply copy and paste your transaction to a UI. All signing, API communication, etc is outsourced to the UI,

#### **POST /api/v0/broadcast - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/types/BroadcastTxRouteSuccessResponse.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/types/BroadcastTxRouteSuccessResponse.html)**)**

```typescript
export interface BroadcastPostBody {
    tx_bytes: Uint8Array;
    mode: string;
}
```

```typescript
export interface BroadcastTxRouteSuccessResponse<T extends NumberType> {
  tx_response: {
    code: number,
    codespace: string,
    data: string,
    events: {
      type: string,
      attributes: {
        key: string,
        value: string,
        index: boolean,
      }[]
    }[],
    gas_wanted: string,
    gas_used: string,
    height: string,
    info: string,
    logs: {
      events: {
        type: string,
        attributes: {
          key: string,
          value: string,
          index: boolean,
        }[]
      }[],
    }[],
    raw_log: string,
    timestamp: string,
    tx: object | null,
    txhash: string,
  }
}
```

#### **POST /api/v0/simulate - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/types/SimulateTxRouteSuccessResponse.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/types/SimulateTxRouteSuccessResponse.html)**)**

```typescript
export interface BroadcastPostBody {
    tx_bytes: Uint8Array;
    mode: string;
}
```

```typescript
export interface SimulateTxRouteSuccessResponse<T extends NumberType> {
  gas_info: {
    gas_used: string,
    gas_wanted: string,
  },
  result: {
    data: string,
    log: string,
    events: {
      type: string,
      attributes: {
        key: string,
        value: string,
        index: boolean,
      }[]
    }[],
    msg_responses: any[]
  }
}

```

### Full API SDK Reference&#x20;

{% @github-files/github-code-block url="https://github.com/BitBadges/bitbadgesjs/blob/main/packages/utils/src/types/api.ts" fullWidth="true" %}
