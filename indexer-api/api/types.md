# Types

In the CouchDB database, collections are stored in the following schema. See [here](broken-reference) for more info on what specific fields mean.

```typescript
export interface StoredBadgeCollection {
    collectionId: number;
    collectionUri: string;
    badgeUris: BadgeUri[];
    bytes: string;
    manager: number;
    permissions: number;
    disallowedTransfers: TransferMapping[];
    managerApprovedTransfers: TransferMapping[];
    nextBadgeId: number;
    unmintedSupplys: Balance[];
    maxSupplys: Balance[];
    claims: Claims[];
    standard: number;
    collectionMetadata: BadgeMetadata,
    badgeMetadata: BadgeMetadataMap,
    usedClaims: {
        [claimId: string]: {
            codes: {
                [code: string]: number;
            },
            numUsed: number,
            addresses: {
                [cosmosAddress: string]: number;
            }
        }
    };
    originalClaims: Claims[];
    managerRequests: number[];
    balances: BalancesMap;
    createdBlock: number;
}
```
