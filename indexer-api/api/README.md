# API

See [https://github.com/BitBadges/bitbadges-frontend/blob/main/src/bitbadges-api/api.ts](https://github.com/BitBadges/bitbadges-frontend/blob/main/src/bitbadges-api/api.ts) for an example implementation of all API routes.

### Getting Started

1. TODO API Key?
2. Start sending requests to the base URL of [https://api.bitbadges.io/](https://api.bitbadges.io/) or another indexer.

### Authentication

For certain requests, we require the user to be authenticated via [Blockin](http://127.0.0.1:5000/o/7VSYQvtb1QtdWFsEGoUn/s/AwjdYgEsUkK9cCca5DiU/). If the user is not signed in, the API will respond with a 401 error code. See [Authentication](authentication.md) for how to authenticate users.

### Errors

We attempt to use typical HTTP error codes. All errors should follow the [ErrorResponse](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/ErrorResponse.html) type which defines a human-readable message via **message**.

### **Routes**

See full documentation for descriptions of each route, request types, and response types.

```typescript
//Status
app.post("/api/v0/status", getStatusHandler);

//Search
app.post("/api/v0/search/:searchValue", searchHandler);

//Collections
app.post("/api/v0/collection/batch", getCollections)
app.post("/api/v0/collection/:collectionId", getCollectionById)
app.post('/api/v0/collection/:collectionId/:badgeId/owners', getOwnersForBadge);
app.post("/api/v0/collection/:collectionId/metadata", getMetadataForCollection)
app.post('/api/v0/collection/:collectionId/balance/:cosmosAddress', getBadgeBalanceByAddress);
app.post('/api/v0/collection/:collectionId/:badgeId/activity', getBadgeActivity);

app.post('/api/v0/collection/:collectionId/refreshMetadata', refreshMetadata); //Write route

app.post('/api/v0/collection/:collectionId/codes', authorizeBlockinRequest, getAllCodesAndPasswords);
app.post('/api/v0/collection/:collectionId/password/:cid/:password', authorizeBlockinRequest, getMerkleChallengeCodeViaPassword); //Write route

app.post('/api/v0/collection/:collectionId/addAnnouncement', authorizeBlockinRequest, addAnnouncement); //Write route
app.post('/api/v0/collection/:collectionId/addReview', authorizeBlockinRequest, addReviewForCollection); //Write route


//User
app.post('/api/v0/user/batch', getAccounts);
app.post('/api/v0/user/updateAccount', authorizeBlockinRequest, updateAccountInfo); //Write route
app.post('/api/v0/user/:addressOrUsername', getAccount);
app.post('/api/v0/user/:addressOrUsername/addReview', authorizeBlockinRequest, addReviewForUser); //Write route

//IPFS
app.post('/api/v0/addMetadataToIpfs', authorizeBlockinRequest, addMetadataToIpfsHandler); //
app.post('/api/v0/addMerkleChallengeToIpfs', authorizeBlockinRequest, addMerkleChallengeToIpfsHandler); //
app.post('/api/v0/addBalancesToIpfs', authorizeBlockinRequest, addBalancesToIpfsHandler); //

//Blockin Auth
app.post('/api/v0/auth/getChallenge', getChallenge);
app.post('/api/v0/auth/verify', verifyBlockinAndGrantSessionCookie);
app.post('/api/v0/auth/logout', removeBlockinSessionCookie);

//Browse
app.post('/api/v0/browse', getBrowseCollections);

//Broadcasting
app.post('/api/v0/broadcast', broadcastTx);
app.post('/api/v0/simulate', simulateTx);

//Fetch arbitrary metadata
app.post('/api/v0/metadata', fetchMetadataDirectly);

//Faucet
app.post('/api/v0/faucet', authorizeBlockinRequest, getTokensFromFaucet);

//Address Mappings
app.post('/api/v0/addressMappings', getAddressMappings);

//Approvals
app.post('/api/v0/approvals', getApprovals);

//Merkle Challenge Tracker
app.post('/api/v0/merkleChallenges', getMerkleChallengeTrackers);
```

