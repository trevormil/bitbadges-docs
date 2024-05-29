# Managing Claims

The BitBadges API allows you to manage claims through various functions. Typically, you will not need this as claims can be managed and updated via the BitBadges site. However, for more advanced cases, you may want to use the API. All require the proper permissions and authorizations.

### Creating Claims

To create a claim using the BitBadges API, use the following function. Not all fields will be applicable depending on the type of claim you are creating. Please read below because certain claim types have specific requirements for creation.

```javascript
await BitBadgesApi.createClaim({
  claims: [{
    claimId: 'unique-claim-id',
    balancesToSet: { /* your balances here */ }, //for off-chain indexed
    plugins: [/* your plugins here */],
    manualDistribution: false,
    automatic: true,
    seedCode: 'your-seed-code', //if applicable
    metadata: { /* your metadata here */ }, 
    listId: 'your-list-id', // for address list claims
    collectionId: 1, // for badge claims
    cid: 'your-cid'
  }]
});
```

#### Address List Claims

For address list claims, simply specify the list ID. The list creator must equal the claim creator.

```javascript
await BitBadgesApi.createClaim({
  claims: [{
    claimId: 'unique-claim-id',
    listId: 'your-list-id',
    plugins: [/* your plugins here */],
    metadata: { /* your metadata here */ }
    ...
  }]
});
```

#### Collections with On-Chain Balances

For claims that map to an on-chain approval, you will need to make sure the claim ID == the challenge tracker ID of your on-chain approval. This will tie the two together inherently. The ID must be unique from any other claim, and you must use the same address to create the claim and blockchain transaction (that creates the approval). This address also needs to be the manager address.&#x20;

You must create the claim first (reserves the ID behind the scenes) and then the approval after. This does not require the collection ID, so this can be done before the collection is even created.

```javascript
await BitBadgesApi.createClaim({
  claims: [{
    claimId: 'challenge-tracker-id',
    plugins: [/* your plugins here */],
    metadata: { /* your metadata here */ }
  }]
});
// Then handle the blockchain transaction with the same address.
```

#### Collections with Off-Chain Balances

For off-chain balances, the collection must be created first. We check that the claim creator is the current manager of the collection.

```javascript
await BitBadgesApi.createClaim({
  claims: [{
    claimId: 'unique-claim-id',
    collectionId: 'existing-collection-id',
    plugins: [/* your plugins here */],
    metadata: { /* your metadata here */ }
  }]
});
```

### Updating Claims

To update a claim, you can do the following:

```javascript
await BitBadgesApi.updateClaim({
  claims: [{
    claimId: 'unique-claim-id',
    plugins: [/* updated plugins here */],
    metadata: { /* updated metadata here */ }
    ...
  }]
});
```

Each plugin can have a `resetState` or `newState` field to manage its state. It is important that if you are using the new state field, you must configure this properly (see docs for the respective plugin).  You may also find the `onlyUpdateProvidedNewState` useful to only set the specific properties in `newState` and not overwrite everything.

```javascript
await BitBadgesApi.updateClaim({
  claims: [{
    claimId: 'unique-claim-id',
    plugins: [{
      ...
      resetState: true // or newState: {/* new state configuration */}
    }]
  }]
});
```

It is best practice to perform any claim updates when they are not active, if possible, because of race conditions.&#x20;

### Deleting Claims

Claims can be deleted as well. However, note this is a soft delete (will not be displayed but state and everything can be reinstated later).

```javascript
await BitBadgesApi.deleteClaim({
  claimIds: ['unique-claim-id']
});
```
