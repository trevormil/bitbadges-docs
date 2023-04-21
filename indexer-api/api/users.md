# Users

The following is the Account object schema to be returned from these routes. We reference it by AccountResponse.

<pre class="language-typescript"><code class="lang-typescript"><strong>export interface AccountResponse {
</strong>    address: string; //address of native chain
    cosmosAddress: string; //equivalent cosmos address
    account_number: number; //account number
    pub_key: string; //public key, if registered. empty if not
    sequence: number; //sequence (nonce) for the blockchain
    chain: string; //name of native chain
    discord?: string;
    twitter?: string;
    github?: string;
    telegram?: string;
    seenActivity?: number; //Date.now() of latest seen activity
    name?: string; //ENS name or other naming services
    avatar?: string; //ENS avatar / other avatar services
    balance?: {
        readonly denom: string; //denom will equal $BADGE
        readonly amount: string; //amount of $BADGE owned
    }; //balance of $BADGE
}
</code></pre>

###

### POST /api/v0/user/batch

Fetches users in batch. Can specify either accountNums and/or addresses.

#### Request

* Body:
* <pre class="language-json"><code class="lang-json"><strong>{
  </strong>  "accountNums": number[],
    "addresses": string[]
  }
  </code></pre>

#### Response

* Status Code: 200
* Body:
* ```json
  {
     "accounts": AccountResponse[]
  }
  ```

### POST /api/v0/user/:accountNum/id

### POST /api/v0/user/:address/address

Fetches a single user by ID or address, depending on the route

#### Request

* Body: None

#### Response

* Status Code: 200
* Body:
* ```json
  {
      ...AccountResponse
  }
  ```

### POST /api/v0/user/:accountNum/portfolio

Fetches a user's portfolio information. Paginates everything by 25. To get the next batch (i.e. the next 25), provide the bookmark from the previous request in the req.body. We assume that if bookmarks are in the body, the user has already fetched the portfolio info, so we only fetch the info for the bookmarks provided.

#### Request

* Body:&#x20;
* <pre><code><strong>{
  </strong>    collectedBookmark: string,
      activityBookmark: string,
      announcementsBookmark: string,
  }
  </code></pre>

#### Response

* Status Code: 200
* Body:
* <pre class="language-json"><code class="lang-json"><strong>{
  </strong>    collected: StoredBadgeCollection[],
      activity: ActivityItem[], //transfer activity item
      announcements: ActivityItem[], //announcement item
      managing: StoredBadgeCollection[],
      pagination: {
          userActivity: {
              bookmark: string[],
              hasMore: boolean
          },
          announcements: {
              bookmark: string[],
              hasMore: boolean
          },
          collected: {
              bookmark: string[],
              hasMore: boolean
          },
      }
  }
  </code></pre>

### POST /api/v0/user/updateAccount

Updates the user's account settings / profile info.

**Authenticated Route**

Must be signed in

#### Request

* Body:&#x20;
* <pre><code><strong>{
  </strong>    discord: string
      twitter: string
      github: string
      telegram: string
      seenActivity: number
  }
  </code></pre>

All are optional. Will only update the ones that are defined.

#### Response

* Status Code: 200
* Body:
* ```json
  { message: 'Account info updated successfully' }
  ```
