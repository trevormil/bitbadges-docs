# Fetching Accounts

The main use case of the API are fetching collection and fetching account information. This page explains fetching accounts. Accounts are stored and fetched as the [BitBadgesUserInfo ](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/BitBadgesUserInfo.html)interface.

<pre class="language-typescript"><code class="lang-typescript"><strong>await BitBadgesApi.getAccounts([{
</strong><strong>    //example
</strong><strong>    address: 'cosmos...',
</strong><strong>    fetchSequence: true,
</strong><strong>    fetchBalance: true,
</strong><strong>    viewsToFetch: [{
</strong>        viewType: 'badgesCollected',
        viewId: 'badgesCollected',
        bookmark: ''
    }]
<strong>}])
</strong></code></pre>

### **Confined Responses**

Response details are confined to the request parameters passed in, so you will have to handle merging the collection response with previous responses. We have made it easy to do with the SDK. This handles everything from new bookmarks and pagination to appending newly fetched profile details.

```typescript
import { updateAccountWithResponse } from "bitbadgesjs-utils"

export function updateAccountWithResponse(
    oldAccount: BitBadgesUserInfo<bigint> | undefined, 
    newAccountResponse: BitBadgesUserInfo<bigint>
): BitBadgesUserInfo<bigint>
```

Or, we have also exported it all in one function as&#x20;

```typescript
public async getAccountsAndUpdate(requestBody: GetAccountsRouteRequestBody, currAccounts: BitBadgesUserInfo<bigint>[]): Promise<BitBadgesUserInfo<bigint>[]> 
```

```typescript
await BitBadgesApi.getAccountsAndUpdate(....)
```

### Chains

We will return the address corresponding to a user's preferred chain via **address**. This will match the chain returned in **chain**.

We also return each equivalent address in the respective corresponding field: **btcAddress**, **solAddress**, **ethAddress**, and **cosmosAddress**.

Note that **solAddress** may be blank if we do not have adequate information. This is because Solana addresses are one-way conversions.

### NSFW / Reported

NSFW or reported accounts will be flagged with the **nsfw** or **reported** properties. Everything is still fetched as normal with typical requests, and it is up to you to do what you want with these accounts.

On the app, we hide all profile details (links, bios, etc) and provide a warning message.

### Usernames and Avatars

**username** will be the user's BitBadges username that they have set. **resolvedName** will be from a naming service (currently only supports ENS for eth addresses).

In a similar manner, **profilePicUrl** will be the user's pofile pic set on BitBadges. **avatar** will be their resolved avatar from a naming service like ENS (if applicable).

### Sequences / Account Numbers

**sequence** and **accountNumber** will be their sequence (nonce) and accountNumber for the BitBadges blockchain. These are used for generating and signing transactions. **publicKey** is also used.

### $BADGE vs Badge Balances

The **balance** field is for $BADGE balance, whereas the **collected** array is an array of balance documents for collected badges.

### Custom Pages, Watchlists, Hidden

Each profile will return their custom pages to be displayed on their portfolio: **customPages**, **customListPages**. These are pages where the user has grouped together lists or badges and designated them to be displayed together on a page on their profile with a title and description.

Watchlists (**watchedListPages** and **watchedBadgePages**) follow the same interface but are for user watchlists. They are not to be displayed publicly on the profile but are rather a way for a user to keep track of badges.

Hidden badges (**hiddenLists** and **hiddenBadges**) follow the same interface, minus the title and description. These are badges that should be hidden from a user's portfolio. If they are hidden, we will hide them from standard search results.

### Alias

The **alias** field denotes whether this is an alias address for a list (address mapping) or a collection. If it isn't, this will be blank.

### **Views / Paginations**

The colleuserction interface uses a view system with paginations + bookmarks.

Views have a base **viewType** describing the query type and a unique **viewId** for identification. Each request you will pass in a bookmark obtained from the previous request (pass in '' for the first request). This will fetch the next 25 documents for that view. Once no more docs can be fetched, the returned **hasMore** will be false.

The account interface also supports different filtering options. Make sure that all fetches with the same viewId specify the same filter options.

The user interface supports the following base **viewType** values.

* 'latestActivity' : Fetches latest transfer activity documents for the user.
* 'listsActivity': Fetches latest list activity documents for the user
* 'latestReviews': Fetches latest reviews for the user
* 'badgesCollected': Fetches badges owned by the user
* 'createdBy': Collections that the user has created
* 'managing': Collections that the user is managing
* 'addressMappings': Fetches lists that the user are explicitly defined on
* 'explicitlyIncludedAddressMappings': Fetches lists that the user are explicitly included
* 'explicitlyExcludedAddressMappings':Fetches lists that the user are explicitly excluded
* 'createdLists': Lists that the user has created (excludes private lists)

The following require authentication:

* 'latestClaimAlerts': Latest claim alerts
* 'authCodes': Authentication QR codes for the user
* 'privateLists': Private lists created by the user

```typescript
export type AccountViewKey = 'createdLists' | 'privateLists' | 'authCodes' | 'latestActivity' | 'latestAnnouncements' | 'latestReviews' | 'badgesCollected' | 'addressMappings' | 'latestClaimAlerts' | 'latestAddressMappings' | 'explicitlyIncludedAddressMappings' | 'explicitlyExcludedAddressMappings' | 'badgesCollectedWithHidden' | 'createdBy' | 'managing' | 'listsActivity'
```

Request:

<pre class="language-json"><code class="lang-json"><strong>viewsToFetch: [{
</strong>    viewType: 'badgesCollected',
    viewId: 'uniqueCollectedId',
    bookmark: ''
}]
</code></pre>

Response:

```json
{
    ...,
    collected: [....],
    views: {
        uniqueCollectedId: {
            ids: [...],
            pagination: {
                bookmark: 'abc',
                hasMore: true
            }
        }
    }
}
```

Next Request:

<pre class="language-json"><code class="lang-json"><strong>viewsToFetch: [{
</strong>    viewType: 'badgesCollected',
    viewId: 'uniqueCollectedId',
    bookmark: 'abc'
}]
</code></pre>

And so on. Remember, each response is confined to its request, so it will fetch the next 25 docs, but you have to append it to the previous 25 docs as explained above.

The **ids** returned in each view will correspond to the **\_docId** field in its corresponding array (e.g. **activity** for the 'latestActivity' view).

```typescript
export function getAuthCodesView(account: BitBadgesUserInfo<bigint> | undefined, viewId: string) {
  if (!account) return [];

  return (account.views[viewId]?.ids.map(x => {
    return account.authCodes.find(y => y._docId === x);
  }) ?? []) as BlockinAuthSignatureDoc<bigint>[];
}


export function getAccountActivityView(account: BitBadgesUserInfo<bigint> | undefined, viewId: string) {
  if (!account) return [];

  return (account.views[viewId]?.ids.map(x => {
    return account.activity.find(y => y._docId === x);
  }) ?? []) as TransferActivityDoc<bigint>[];
}

export function getAccountListsActivityView(account: BitBadgesUserInfo<bigint> | undefined, viewId: string) {
  if (!account) return [];

  return (account.views[viewId]?.ids.map(x => {
    return account.listsActivity.find(y => y._docId === x);
  }) ?? []) as ListActivityDoc<bigint>[];
}


export function getAccountReviewsView(account: BitBadgesUserInfo<bigint> | undefined, viewId: string) {
  if (!account) return [];

  return (account.views[viewId]?.ids.map(x => {
    return account.reviews.find(y => y._docId === x);
  }) ?? []) as ReviewDoc<bigint>[];
}

export function getAccountAnnouncementsView(account: BitBadgesUserInfo<bigint> | undefined, viewId: string) {
  if (!account) return [];

  return (account.views[viewId]?.ids.map(x => {
    return account.announcements.find(y => y._docId === x);
  }) ?? []) as AnnouncementDoc<bigint>[];
}

export function getAccountBalancesView(account: BitBadgesUserInfo<bigint> | undefined, viewId: string) {
  if (!account) return [];

  return (account.views[viewId]?.ids.map(x => {
    return account.collected.find(y => y._docId === x)
  }) ?? []) as BalanceDoc<bigint>[];
}

export function getAccountAddressMappingsView(account: BitBadgesUserInfo<bigint> | undefined, viewId: string) {
  if (!account) return [];

  return (account.views[viewId]?.ids.map(x => {
    return account.addressMappings.find(y => y.mappingId === x);
  }) ?? []) as AddressMappingDoc<bigint>[];
}

export function getAccountClaimAlertsView(account: BitBadgesUserInfo<bigint> | undefined, viewId: string) {
  if (!account) return [];

  return (account.views[viewId]?.ids.map(x => {
    return account.claimAlerts.find(y => y._docId === x);
  }) ?? []) as ClaimAlertDoc<bigint>[];
}
```

### **Fetch Route**

#### **POST /api/v0/user/batch - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetAccountsRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetAccountsRouteSuccessResponse.html)**)**

Batch fetch details about multiple collections.

```typescript
export type AccountFetchDetails = {
  address?: string;
  username?: string;
  fetchSequence?: boolean;
  fetchBalance?: boolean;
  noExternalCalls?: boolean;
  viewsToFetch?: {
    viewId: string,
    viewType: AccountViewKey,
    filteredCollections?: {
      badgeIds: UintRange<NumberType>[];
      collectionId: NumberType;
    }[];
    filteredLists?: string[];
    bookmark: string
  }[];
};

export interface GetAccountsRouteRequestBody {
  accountsToFetch: AccountFetchDetails[];
}
```

```typescript
export interface GetAccountsRouteSuccessResponse<T extends NumberType> {
  accounts: BitBadgesUserInfo<T>[],
}
```

### **Interfaces**

```ts
export interface BitBadgesUserInfo<T extends NumberType> extends ProfileInfoBase<T>, AccountInfoBase<T> {
  resolvedName?: string
  avatar?: string
  solAddress: string
  airdropped?: boolean
  address: string

  //Dynamically loaded as needed
  collected: BalanceDocWithDetails<T>[],
  activity: TransferActivityDoc<T>[],
  listsActivity: ListActivityDoc<T>[],
  announcements: AnnouncementDoc<T>[],
  reviews: ReviewDoc<T>[],
  merkleChallenges: MerkleChallengeDoc<T>[],
  approvalsTrackers: ApprovalsTrackerDoc<T>[],
  addressMappings: AddressMappingWithMetadata<T>[],
  claimAlerts: ClaimAlertDoc<T>[],
  authCodes: BlockinAuthSignatureDoc<T>[],

  nsfw?: { reason: string };
  reported?: { reason: string };

  views: {
    [viewId: string]: {
      ids: string[],
      type: string,
      pagination: PaginationInfo,
    } | undefined
  }

  alias?: {
    collectionId?: T,
    mappingId?: string
  }
}

export interface AccountInfoBase<T extends NumberType> {
  //stored in DB and cached for fast access and permanence

  publicKey: string
  chain: SupportedChain
  cosmosAddress: string
  ethAddress: string
  solAddress: string
  btcAddress: string
  accountNumber: T

  //dynamically fetched

  sequence?: T
  balance?: CosmosCoin<T>
}


export interface ProfileInfoBase<T extends NumberType> {
  fetchedProfile?: boolean

  seenActivity?: T;
  createdAt?: T;

  //ProfileDoc customization
  discord?: string
  twitter?: string
  github?: string
  telegram?: string
  readme?: string

  customLinks?: CustomLink[]

  hiddenBadges?: {
    collectionId: T,
    badgeIds: UintRange<T>[],
  }[],
  hiddenLists?: string[];

  customListPages?: CustomListPage[],
  customPages?: CustomPage<T>[],

  watchedBadgePages?: CustomPage<T>[],
  watchedListPages?: CustomListPage[],

  profilePicUrl?: string
  username?: string

  latestSignedInChain?: SupportedChain
  solAddress?: string
}



export interface CustomPage<T extends NumberType> {
  title: string,
  description: string,
  badges: {
    collectionId: T,
    badgeIds: UintRange<T>[],
  }[]
}

export interface CustomListPage {
  title: string,
  description: string,
  mappingIds: string[],
}

export enum SupportedChain {
  BTC = 'Bitcoin',
  ETH = 'Ethereum',
  COSMOS = 'Cosmos',
  SOLANA = 'Solana',
  UNKNOWN = 'Unknown' //If unknown address, we don't officially know the chain yet. For now, we assume it's Ethereum
}

export interface CosmosCoin<T extends NumberType> {
  amount: T,
  denom: string,
}
```
