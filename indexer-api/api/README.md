# API

See [https://github.com/BitBadges/bitbadges-frontend/blob/main/src/bitbadges-api/api.ts](https://github.com/BitBadges/bitbadges-frontend/blob/main/src/bitbadges-api/api.ts) for an example implementation of all API routes.

### Getting Started

1. TODO API Key?
2. Start sending requests with the base URL of [https://api.bitbadges.io/](https://api.bitbadges.io/).

### Authentication

For certain requests, we require the user to be authenticated via [Blockin](http://localhost:5000/o/7VSYQvtb1QtdWFsEGoUn/s/AwjdYgEsUkK9cCca5DiU/). If the user is not signed in, the API will respond with a 401 error code. See [Authentication](authentication.md) for how to authenticate users.

### **Routes**

#### Search

* `POST /api/v0/search/:searchValue`

#### Collections

* `POST /api/v0/collection/batch`
* `POST /api/v0/collection/query`
* `POST /api/v0/collection/:id`
* `POST /api/v0/collection/:id/:badgeId/owners`
* `POST /api/v0/collection/:id/metadata`
* `POST /api/v0/collection/:id/balance/:accountNum`
* `POST /api/v0/collection/:id/:badgeId/activity`
* `POST /api/v0/collection/:id/refreshMetadata`
* `POST /api/v0/collection/:id/:badgeId/refreshMetadata`
* `POST /api/v0/collection/:id/codes`
* `POST /api/v0/collection/:id/password/:claimId/:password`
* `POST /api/v0/collection/:id/addAnnouncement`

#### User

* `POST /api/v0/user/batch`
* `POST /api/v0/user/:accountNum/id`
* `POST /api/v0/user/:address/address`
* `POST /api/v0/user/:accountNum/portfolio`
* `POST /api/v0/user/:accountNum/activity`
* `POST /api/v0/user/updateAccount`

#### IPFS

* `POST /api/v0/addToIpfs`
* `POST /api/v0/addMerkleTreeToIpfs`

#### Blockin Auth

* `POST /api/v0/auth/getChallenge`
* `POST /api/v0/auth/verify`
* `POST /api/v0/auth/logout`

#### Browse

* `POST /api/v0/browse`

#### Broadcast

* `POST /api/v0/broadcast`

#### Fetch Arbitrary Metadata

* `POST /api/v0/metadata`

#### Faucet

* `POST /api/v0/faucet`

#### Status

* `POST /api/v0/status`

