# SDK -> Routes

In this documentation, we often use the following format for explanation purposes

```typescript
await BitBadgesApi.routeFn(...)
```

Please convert the corresponding function name to vanilla HTTP if you are not using the SDK.

```
POST https://api.bitbadges.io/api/v0/routeFn
```

Below, we provide these for convenience, but please use the docs for the latest values at  [https://bitbadges.stoplight.io/docs/bitbadges](https://bitbadges.stoplight.io/docs/bitbadges).



### Accounts

* `getAccounts` -> POST `/users`
* `updateAccountInfo` -> POST `/user/updateAccount`

### Badges

* `getCollections` -> POST `/collections`
* `getBadgeBalanceByAddress` -> POST `/collection/{collectionId}/balance/{address}`
* `getOwnersForBadge` -> POST `/collection/{collectionId}/{badgeId}/owners`
* `getBadgeActivity` -> POST `/collection/{collectionId}/{badgeId}/activity`
* `refreshMetadata` -> POST `/collection/{collectionId}/refresh`
* `getRefreshStatus` -> POST `/collection/{collectionId}/refreshStatus`

### Claims

* `completeClaim` -> POST `/claims/complete/{claimId}/{address}`
* `simulateClaim` -> POST `/claims/simulate/{claimId}/{address}`
* `getReservedCodes` -> POST `/claims/reserved/{claimId}/{address}`
* `getClaimAttemptStatus` -> POST `/claims/status/{claimAttemptId}`
* `getClaims` -> POST `/claims/fetch`
* `createClaim` -> POST `/claims`
* `updateClaim` -> PUT `/claims`
* `deleteClaim` -> DELETE `/claims`
* `generateCode` -> POST `/codes`
* `getClaimAttempts` -> POST `/claims/{claimId}/attempts`
* `getGatedContentForClaim` -> POST `/claims/gatedContent/{claimId}`

### Claim Alerts

* `sendClaimAlert` -> POST `/claimAlerts/send`
* `getClaimAlerts` -> POST `/claimAlerts`

### Address Lists

* `updateAddressLists` -> PUT `/addressLists`
* `createAddressLists` -> POST `/addressLists`
* `deleteAddressLists` -> DELETE `/addressLists`
* `getAddressLists` -> POST `/addressLists/fetch`

### Sign In with BitBadges

* `rotateSIWBBRequest` -> POST `/siwbbRequest/rotate`
* `deleteSIWBBRequest` -> DELETE `/siwbbRequest`
* `createSIWBBRequest` -> POST `/siwbbRequest`
* `getSIWBBRequestsForDeveloperApp` -> POST `/developerApp/siwbbRequests`
* `verifySIWBBRequest` -> POST `/siwbbRequest/verify`
* `exchangeSIWBBAuthorizationCode` -> POST `/siwbb/token`
* `revokeOauthAuthorization` -> POST `/siwbb/token/revoke`
* `generateAppleWalletPass` -> POST `/siwbbRequest/appleWalletPass`
* `generateGoogleWalletPass` -> POST `/siwbbRequest/googleWalletPass`

### Dynamic Stores

* `performBinActionSingle` -> POST `/bin-actions/{actionName}/{binId}/{binSecret}`
* `performBinActionSingleWithBodyAuth` -> POST `/bin-actions/single`
* `performBinActionBatch` -> POST `/bin-actions/batch/{binId}/{binSecret}`
* `performBinActionBatchWithBodyAuth` -> POST `/bin-actions/batch`
* `getDynamicDataBins` -> POST `/bins/fetch`
* `getDynamicDataActivity` -> POST `/bins/activity`

### Groups

* `getGroups` -> POST `/groups/fetch`
* `createGroup` -> POST `/groups`
* `updateGroup` -> PUT `/groups`
* `deleteGroup` -> DELETE `/groups`
* `calculatePoints` -> POST `/groups/points`
* `getPointsActivity` -> POST `/groups/points/activity`

### Utility Listings

* `getUtilityListings` -> POST `/utilityListings/fetch`
* `createUtilityListing` -> POST `/utilityListings`
* `updateUtilityListing` -> PUT `/utilityListings`
* `deleteUtilityListing` -> DELETE `/utilityListings`

### Miscellaneous

* `getStatus` -> POST `/status`
* `searchByValue` -> POST `/search/{searchValue}`
* `GetBrowse` -> POST `/browse`
* `broadcastTx` -> POST `/broadcast`
* `simulateTx` -> POST `/simulate`
* `verifyOwnershipRequirements` -> POST `/verifyOwnershipRequirements`
* `verifyAttestation` -> POST `/attestation/verify`
