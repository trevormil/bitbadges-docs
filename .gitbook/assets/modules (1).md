[BitBadges](README.md) / Exports

# BitBadges

## Table of contents

### Enumerations

- [DistributionMethod](enums/DistributionMethod.md)
- [MetadataAddMethod](enums/MetadataAddMethod.md)
- [SupportedChain](enums/SupportedChain.md)
- [TransactionStatus](enums/TransactionStatus.md)

### Interfaces

- [AccountDocs](interfaces/AccountDocs.md)
- [AccountDocument](interfaces/AccountDocument.md)
- [AccountMap](interfaces/AccountMap.md)
- [AccountResponse](interfaces/AccountResponse.md)
- [ActivityItem](interfaces/ActivityItem.md)
- [Addresses](interfaces/Addresses.md)
- [AnnouncementActivityItem](interfaces/AnnouncementActivityItem.md)
- [Approval](interfaces/Approval.md)
- [BadgeMetadata](interfaces/BadgeMetadata.md)
- [BadgeMetadataMap](interfaces/BadgeMetadataMap.md)
- [BadgeSupplyAndAmount](interfaces/BadgeSupplyAndAmount.md)
- [BadgeUri](interfaces/BadgeUri.md)
- [Balance](interfaces/Balance.md)
- [BalanceObject](interfaces/BalanceObject.md)
- [BalancesMap](interfaces/BalancesMap.md)
- [BitBadgeCollection](interfaces/BitBadgeCollection.md)
- [BitBadgeMintObject](interfaces/BitBadgeMintObject.md)
- [BitBadgesUserInfo](interfaces/BitBadgesUserInfo.md)
- [ClaimItem](interfaces/ClaimItem.md)
- [ClaimItemWithTrees](interfaces/ClaimItemWithTrees.md)
- [Claims](interfaces/Claims.md)
- [CollectionDocs](interfaces/CollectionDocs.md)
- [CollectionMap](interfaces/CollectionMap.md)
- [CosmosAccountInformation](interfaces/CosmosAccountInformation.md)
- [DbStatus](interfaces/DbStatus.md)
- [Docs](interfaces/Docs.md)
- [GetAccountResponse](interfaces/GetAccountResponse.md)
- [GetBadgeBalanceResponse](interfaces/GetBadgeBalanceResponse.md)
- [GetBalanceResponse](interfaces/GetBalanceResponse.md)
- [GetCollectionResponse](interfaces/GetCollectionResponse.md)
- [GetOwnersResponse](interfaces/GetOwnersResponse.md)
- [GetPortfolioResponse](interfaces/GetPortfolioResponse.md)
- [IdRange](interfaces/IdRange.md)
- [IndexerStatus](interfaces/IndexerStatus.md)
- [LatestBlockStatus](interfaces/LatestBlockStatus.md)
- [MetadataDocs](interfaces/MetadataDocs.md)
- [MetadataDocument](interfaces/MetadataDocument.md)
- [PaginationInfo](interfaces/PaginationInfo.md)
- [PasswordDocument](interfaces/PasswordDocument.md)
- [PendingTransfer](interfaces/PendingTransfer.md)
- [Proof](interfaces/Proof.md)
- [SearchResponse](interfaces/SearchResponse.md)
- [StoredBadgeCollection](interfaces/StoredBadgeCollection.md)
- [SubassetSupply](interfaces/SubassetSupply.md)
- [TransferActivityItem](interfaces/TransferActivityItem.md)
- [TransferList](interfaces/TransferList.md)
- [TransferListWithUnregisteredUsers](interfaces/TransferListWithUnregisteredUsers.md)
- [Transfers](interfaces/Transfers.md)
- [TransfersExtended](interfaces/TransfersExtended.md)
- [UserBalance](interfaces/UserBalance.md)

### Type Aliases

- [Permissions](modules.md#permissions)

### Variables

- [AllAddressesTransferList](modules.md#alladdressestransferlist)
- [CHAIN\_DETAILS](modules.md#chain_details)
- [CanCreateMoreBadgesDigit](modules.md#cancreatemorebadgesdigit)
- [CanDeleteDigit](modules.md#candeletedigit)
- [CanManagerBeTransferredDigit](modules.md#canmanagerbetransferreddigit)
- [CanUpdateBytesDigit](modules.md#canupdatebytesdigit)
- [CanUpdateDisallowedDigit](modules.md#canupdatedisalloweddigit)
- [CanUpdateUrisDigit](modules.md#canupdateurisdigit)
- [DefaultPlaceholderMetadata](modules.md#defaultplaceholdermetadata)
- [ErrorMetadata](modules.md#errormetadata)
- [GO\_MAX\_UINT\_64](modules.md#go_max_uint_64)
- [MAX\_DATE\_TIMESTAMP](modules.md#max_date_timestamp)
- [METADATA\_PAGE\_LIMIT](modules.md#metadata_page_limit)
- [MINT\_ACCOUNT](modules.md#mint_account)
- [NUM\_PERMISSIONS](modules.md#num_permissions)

### Functions

- [AddBalancesForIdRanges](modules.md#addbalancesforidranges)
- [DeleteBalanceForIdRanges](modules.md#deletebalanceforidranges)
- [GetAccountByNumberRoute](modules.md#getaccountbynumberroute)
- [GetAccountRoute](modules.md#getaccountroute)
- [GetAccountsRoute](modules.md#getaccountsroute)
- [GetBadgeBalanceRoute](modules.md#getbadgebalanceroute)
- [GetBalanceInfoToInsertToStorage](modules.md#getbalanceinfotoinserttostorage)
- [GetBalanceRoute](modules.md#getbalanceroute)
- [GetBalancesForIdRanges](modules.md#getbalancesforidranges)
- [GetCollectionRoute](modules.md#getcollectionroute)
- [GetCollectionsRoute](modules.md#getcollectionsroute)
- [GetIdRangeToInsert](modules.md#getidrangetoinsert)
- [GetIdRangesToInsertToStorage](modules.md#getidrangestoinserttostorage)
- [GetIdRangesWithOmitEmptyCaseHandled](modules.md#getidrangeswithomitemptycasehandled)
- [GetIdxSpanForRange](modules.md#getidxspanforrange)
- [GetIdxToInsertForNewId](modules.md#getidxtoinsertfornewid)
- [GetMetadataRoute](modules.md#getmetadataroute)
- [GetOwnersRoute](modules.md#getownersroute)
- [GetPermissionNumberValue](modules.md#getpermissionnumbervalue)
- [GetPermissions](modules.md#getpermissions)
- [GetPortfolioRoute](modules.md#getportfolioroute)
- [GetSearchRoute](modules.md#getsearchroute)
- [GetStatusRoute](modules.md#getstatusroute)
- [InsertRangeToIdRanges](modules.md#insertrangetoidranges)
- [MergePrevOrNextIfPossible](modules.md#mergeprevornextifpossible)
- [NormalizeIdRange](modules.md#normalizeidrange)
- [RemoveIdsFromIdRange](modules.md#removeidsfromidrange)
- [SafeAdd](modules.md#safeadd)
- [SafeSubtract](modules.md#safesubtract)
- [SearchBalances](modules.md#searchbalances)
- [SearchIdRangesForId](modules.md#searchidrangesforid)
- [SetBalanceForIdRanges](modules.md#setbalanceforidranges)
- [SortIdRangesAndMergeIfNecessary](modules.md#sortidrangesandmergeifnecessary)
- [SubtractBalancesForIdRanges](modules.md#subtractbalancesforidranges)
- [UpdateBalancesForIdRanges](modules.md#updatebalancesforidranges)
- [UpdatePermissions](modules.md#updatepermissions)
- [ValidatePermissions](modules.md#validatepermissions)
- [ValidatePermissionsUpdate](modules.md#validatepermissionsupdate)
- [checkIfApproved](modules.md#checkifapproved)
- [checkIfApprovedInTransferList](modules.md#checkifapprovedintransferlist)
- [checkIfIdRangesOverlap](modules.md#checkifidrangesoverlap)
- [convertToBitBadgesUserInfo](modules.md#converttobitbadgesuserinfo)
- [convertToBitBadgesAddress](modules.md#converttocosmosaddress)
- [createCollectionFromMsgNewCollection](modules.md#createcollectionfrommsgnewcollection)
- [doesChainMatchName](modules.md#doeschainmatchname)
- [filterBadgeActivityForBadgeId](modules.md#filterbadgeactivityforbadgeid)
- [getAbbreviatedAddress](modules.md#getabbreviatedaddress)
- [getBadgeIdsToDisplayForPageNumber](modules.md#getbadgeidstodisplayforpagenumber)
- [getBalanceAfterTransfer](modules.md#getbalanceaftertransfer)
- [getBalanceAfterTransfers](modules.md#getbalanceaftertransfers)
- [getBlankBalance](modules.md#getblankbalance)
- [getChainForAddress](modules.md#getchainforaddress)
- [getClaimsFromClaimItems](modules.md#getclaimsfromclaimitems)
- [getIdRangesForAllBadgeIdsInCollection](modules.md#getidrangesforallbadgeidsincollection)
- [getMatchingAddressesFromTransferList](modules.md#getmatchingaddressesfromtransferlist)
- [getMaxBatchId](modules.md#getmaxbatchid)
- [getMetadataForBadgeId](modules.md#getmetadataforbadgeid)
- [getMetadataMapObjForBadgeId](modules.md#getmetadatamapobjforbadgeid)
- [getNonTransferableDisallowedTransfers](modules.md#getnontransferabledisallowedtransfers)
- [getSupplyByBadgeId](modules.md#getsupplybybadgeid)
- [getTransferListForSelectOptions](modules.md#gettransferlistforselectoptions)
- [getTransfersFromClaimItems](modules.md#gettransfersfromclaimitems)
- [isAddressValid](modules.md#isaddressvalid)
- [isTransferListFull](modules.md#istransferlistfull)
- [populateFieldsOfOtherBadges](modules.md#populatefieldsofotherbadges)
- [updateMetadataForBadgeIdsFromIndexerIfAbsent](modules.md#updatemetadataforbadgeidsfromindexerifabsent)
- [updateMetadataMap](modules.md#updatemetadatamap)
- [updateTransferListAccountNums](modules.md#updatetransferlistaccountnums)

## Type Aliases

### Permissions

Ƭ **Permissions**: `Object`

#### Type declaration

| Name | Type |
| :------ | :------ |
| `CanCreateMoreBadges` | `boolean` |
| `CanDelete` | `boolean` |
| `CanManagerBeTransferred` | `boolean` |
| `CanUpdateBytes` | `boolean` |
| `CanUpdateDisallowed` | `boolean` |
| `CanUpdateUris` | `boolean` |

#### Defined in

[permissions.ts:3](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/permissions.ts#L3)

## Variables

### AllAddressesTransferList

• `Const` **AllAddressesTransferList**: [`TransferList`](interfaces/TransferList.md)

#### Defined in

[badges.ts:109](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/badges.ts#L109)

___

### CHAIN\_DETAILS

• `Const` **CHAIN\_DETAILS**: `Object`

#### Type declaration

| Name | Type |
| :------ | :------ |
| `chainId` | `number` |
| `cosmosChainId` | `string` |

#### Defined in

[constants.ts:9](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/constants.ts#L9)

___

### CanCreateMoreBadgesDigit

• `Const` **CanCreateMoreBadgesDigit**: ``2``

#### Defined in

[permissions.ts:16](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/permissions.ts#L16)

___

### CanDeleteDigit

• `Const` **CanDeleteDigit**: ``6``

#### Defined in

[permissions.ts:12](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/permissions.ts#L12)

___

### CanManagerBeTransferredDigit

• `Const` **CanManagerBeTransferredDigit**: ``4``

#### Defined in

[permissions.ts:14](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/permissions.ts#L14)

___

### CanUpdateBytesDigit

• `Const` **CanUpdateBytesDigit**: ``5``

#### Defined in

[permissions.ts:13](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/permissions.ts#L13)

___

### CanUpdateDisallowedDigit

• `Const` **CanUpdateDisallowedDigit**: ``1``

#### Defined in

[permissions.ts:17](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/permissions.ts#L17)

___

### CanUpdateUrisDigit

• `Const` **CanUpdateUrisDigit**: ``3``

#### Defined in

[permissions.ts:15](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/permissions.ts#L15)

___

### DefaultPlaceholderMetadata

• `Const` **DefaultPlaceholderMetadata**: [`BadgeMetadata`](interfaces/BadgeMetadata.md)

#### Defined in

[constants.ts:14](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/constants.ts#L14)

___

### ErrorMetadata

• `Const` **ErrorMetadata**: [`BadgeMetadata`](interfaces/BadgeMetadata.md)

#### Defined in

[constants.ts:20](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/constants.ts#L20)

___

### GO\_MAX\_UINT\_64

• `Const` **GO\_MAX\_UINT\_64**: ``1000000000000000``

#### Defined in

[constants.ts:7](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/constants.ts#L7)

___

### MAX\_DATE\_TIMESTAMP

• `Const` **MAX\_DATE\_TIMESTAMP**: `number`

#### Defined in

[constants.ts:5](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/constants.ts#L5)

___

### METADATA\_PAGE\_LIMIT

• `Const` **METADATA\_PAGE\_LIMIT**: ``100``

#### Defined in

[constants.ts:3](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/constants.ts#L3)

___

### MINT\_ACCOUNT

• `Const` **MINT\_ACCOUNT**: [`BitBadgesUserInfo`](interfaces/BitBadgesUserInfo.md)

#### Defined in

[chains.ts:5](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/chains.ts#L5)

___

### NUM\_PERMISSIONS

• `Const` **NUM\_PERMISSIONS**: ``6``

#### Defined in

[permissions.ts:1](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/permissions.ts#L1)

## Functions

### AddBalancesForIdRanges

▸ **AddBalancesForIdRanges**(`userBalanceInfo`, `ranges`, `balanceToAdd`): [`UserBalance`](interfaces/UserBalance.md)

#### Parameters

| Name | Type |
| :------ | :------ |
| `userBalanceInfo` | [`UserBalance`](interfaces/UserBalance.md) |
| `ranges` | [`IdRange`](interfaces/IdRange.md)[] |
| `balanceToAdd` | `number` |

#### Returns

[`UserBalance`](interfaces/UserBalance.md)

#### Defined in

[balances-gpt.ts:116](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/balances-gpt.ts#L116)

___

### DeleteBalanceForIdRanges

▸ **DeleteBalanceForIdRanges**(`ranges`, `balanceObjects`): [`Balance`](interfaces/Balance.md)[]

#### Parameters

| Name | Type |
| :------ | :------ |
| `ranges` | [`IdRange`](interfaces/IdRange.md)[] |
| `balanceObjects` | [`Balance`](interfaces/Balance.md)[] |

#### Returns

[`Balance`](interfaces/Balance.md)[]

#### Defined in

[balances-gpt.ts:142](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/balances-gpt.ts#L142)

___

### GetAccountByNumberRoute

▸ **GetAccountByNumberRoute**(`id`): `string`

#### Parameters

| Name | Type |
| :------ | :------ |
| `id` | `number` |

#### Returns

`string`

#### Defined in

[routes.ts:8](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/routes.ts#L8)

___

### GetAccountRoute

▸ **GetAccountRoute**(`bech32address`): `string`

#### Parameters

| Name | Type |
| :------ | :------ |
| `bech32address` | `string` |

#### Returns

`string`

#### Defined in

[routes.ts:4](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/routes.ts#L4)

___

### GetAccountsRoute

▸ **GetAccountsRoute**(): `string`

#### Returns

`string`

#### Defined in

[routes.ts:12](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/routes.ts#L12)

___

### GetBadgeBalanceRoute

▸ **GetBadgeBalanceRoute**(`collectionId`, `accountNumber`): `string`

#### Parameters

| Name | Type |
| :------ | :------ |
| `collectionId` | `number` |
| `accountNumber` | `number` |

#### Returns

`string`

#### Defined in

[routes.ts:28](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/routes.ts#L28)

___

### GetBalanceInfoToInsertToStorage

▸ **GetBalanceInfoToInsertToStorage**(`balanceInfo`): [`UserBalance`](interfaces/UserBalance.md)

#### Parameters

| Name | Type |
| :------ | :------ |
| `balanceInfo` | [`UserBalance`](interfaces/UserBalance.md) |

#### Returns

[`UserBalance`](interfaces/UserBalance.md)

#### Defined in

[balances-gpt.ts:254](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/balances-gpt.ts#L254)

___

### GetBalanceRoute

▸ **GetBalanceRoute**(`bech32address`): `string`

#### Parameters

| Name | Type |
| :------ | :------ |
| `bech32address` | `string` |

#### Returns

`string`

#### Defined in

[routes.ts:16](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/routes.ts#L16)

___

### GetBalancesForIdRanges

▸ **GetBalancesForIdRanges**(`badgeIds`, `currentUserBalances`): [`Balance`](interfaces/Balance.md)[]

#### Parameters

| Name | Type |
| :------ | :------ |
| `badgeIds` | [`IdRange`](interfaces/IdRange.md)[] |
| `currentUserBalances` | [`Balance`](interfaces/Balance.md)[] |

#### Returns

[`Balance`](interfaces/Balance.md)[]

#### Defined in

[balances-gpt.ts:36](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/balances-gpt.ts#L36)

___

### GetCollectionRoute

▸ **GetCollectionRoute**(`collectionId`): `string`

#### Parameters

| Name | Type |
| :------ | :------ |
| `collectionId` | `number` |

#### Returns

`string`

#### Defined in

[routes.ts:20](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/routes.ts#L20)

___

### GetCollectionsRoute

▸ **GetCollectionsRoute**(): `string`

#### Returns

`string`

#### Defined in

[routes.ts:24](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/routes.ts#L24)

___

### GetIdRangeToInsert

▸ **GetIdRangeToInsert**(`start`, `end`): `Object`

#### Parameters

| Name | Type |
| :------ | :------ |
| `start` | `number` |
| `end` | `number` |

#### Returns

`Object`

| Name | Type |
| :------ | :------ |
| `end` | `number` |
| `start` | `number` |

#### Defined in

[idRanges.ts:152](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/idRanges.ts#L152)

___

### GetIdRangesToInsertToStorage

▸ **GetIdRangesToInsertToStorage**(`idRanges`): [`IdRange`](interfaces/IdRange.md)[]

#### Parameters

| Name | Type |
| :------ | :------ |
| `idRanges` | [`IdRange`](interfaces/IdRange.md)[] |

#### Returns

[`IdRange`](interfaces/IdRange.md)[]

#### Defined in

[idRanges.ts:31](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/idRanges.ts#L31)

___

### GetIdRangesWithOmitEmptyCaseHandled

▸ **GetIdRangesWithOmitEmptyCaseHandled**(`ids`): [`IdRange`](interfaces/IdRange.md)[]

#### Parameters

| Name | Type |
| :------ | :------ |
| `ids` | [`IdRange`](interfaces/IdRange.md)[] |

#### Returns

[`IdRange`](interfaces/IdRange.md)[]

#### Defined in

[idRanges.ts:144](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/idRanges.ts#L144)

___

### GetIdxSpanForRange

▸ **GetIdxSpanForRange**(`targetRange`, `targetIdRanges`): [[`IdRange`](interfaces/IdRange.md), `boolean`]

#### Parameters

| Name | Type |
| :------ | :------ |
| `targetRange` | [`IdRange`](interfaces/IdRange.md) |
| `targetIdRanges` | [`IdRange`](interfaces/IdRange.md)[] |

#### Returns

[[`IdRange`](interfaces/IdRange.md), `boolean`]

#### Defined in

[idRanges.ts:111](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/idRanges.ts#L111)

___

### GetIdxToInsertForNewId

▸ **GetIdxToInsertForNewId**(`id`, `targetIds`): `number`

#### Parameters

| Name | Type |
| :------ | :------ |
| `id` | `number` |
| `targetIds` | [`IdRange`](interfaces/IdRange.md)[] |

#### Returns

`number`

#### Defined in

[idRanges.ts:176](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/idRanges.ts#L176)

___

### GetMetadataRoute

▸ **GetMetadataRoute**(`collectionId`): `string`

#### Parameters

| Name | Type |
| :------ | :------ |
| `collectionId` | `number` |

#### Returns

`string`

#### Defined in

[routes.ts:40](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/routes.ts#L40)

___

### GetOwnersRoute

▸ **GetOwnersRoute**(`collectionId`, `badgeId`): `string`

#### Parameters

| Name | Type |
| :------ | :------ |
| `collectionId` | `number` |
| `badgeId` | `number` |

#### Returns

`string`

#### Defined in

[routes.ts:32](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/routes.ts#L32)

___

### GetPermissionNumberValue

▸ **GetPermissionNumberValue**(`permissions`): `number`

#### Parameters

| Name | Type |
| :------ | :------ |
| `permissions` | [`Permissions`](modules.md#permissions) |

#### Returns

`number`

#### Defined in

[permissions.ts:19](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/permissions.ts#L19)

___

### GetPermissions

▸ **GetPermissions**(`permissions`): [`Permissions`](modules.md#permissions)

#### Parameters

| Name | Type |
| :------ | :------ |
| `permissions` | `number` |

#### Returns

[`Permissions`](modules.md#permissions)

#### Defined in

[permissions.ts:118](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/permissions.ts#L118)

___

### GetPortfolioRoute

▸ **GetPortfolioRoute**(`accountNumber`): `string`

#### Parameters

| Name | Type |
| :------ | :------ |
| `accountNumber` | `number` |

#### Returns

`string`

#### Defined in

[routes.ts:36](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/routes.ts#L36)

___

### GetSearchRoute

▸ **GetSearchRoute**(`query`): `string`

#### Parameters

| Name | Type |
| :------ | :------ |
| `query` | `string` |

#### Returns

`string`

#### Defined in

[routes.ts:44](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/routes.ts#L44)

___

### GetStatusRoute

▸ **GetStatusRoute**(): `string`

#### Returns

`string`

#### Defined in

[routes.ts:48](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/routes.ts#L48)

___

### InsertRangeToIdRanges

▸ **InsertRangeToIdRanges**(`rangeToAdd`, `targetIds`): [`IdRange`](interfaces/IdRange.md)[]

#### Parameters

| Name | Type |
| :------ | :------ |
| `rangeToAdd` | [`IdRange`](interfaces/IdRange.md) |
| `targetIds` | [`IdRange`](interfaces/IdRange.md)[] |

#### Returns

[`IdRange`](interfaces/IdRange.md)[]

#### Defined in

[idRanges.ts:274](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/idRanges.ts#L274)

___

### MergePrevOrNextIfPossible

▸ **MergePrevOrNextIfPossible**(`targetIds`, `insertedAtIdx`): [`IdRange`](interfaces/IdRange.md)[]

#### Parameters

| Name | Type |
| :------ | :------ |
| `targetIds` | [`IdRange`](interfaces/IdRange.md)[] |
| `insertedAtIdx` | `number` |

#### Returns

[`IdRange`](interfaces/IdRange.md)[]

#### Defined in

[idRanges.ts:218](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/idRanges.ts#L218)

___

### NormalizeIdRange

▸ **NormalizeIdRange**(`rangeToNormalize`): `Object`

#### Parameters

| Name | Type |
| :------ | :------ |
| `rangeToNormalize` | [`IdRange`](interfaces/IdRange.md) |

#### Returns

`Object`

| Name | Type |
| :------ | :------ |
| `end` | `number` |
| `start` | `number` |

#### Defined in

[idRanges.ts:164](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/idRanges.ts#L164)

___

### RemoveIdsFromIdRange

▸ **RemoveIdsFromIdRange**(`rangeToRemove`, `rangeObject`): [`IdRange`](interfaces/IdRange.md)[]

#### Parameters

| Name | Type |
| :------ | :------ |
| `rangeToRemove` | [`IdRange`](interfaces/IdRange.md) |
| `rangeObject` | [`IdRange`](interfaces/IdRange.md) |

#### Returns

[`IdRange`](interfaces/IdRange.md)[]

#### Defined in

[idRanges.ts:47](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/idRanges.ts#L47)

___

### SafeAdd

▸ **SafeAdd**(`left`, `right`): `number`

#### Parameters

| Name | Type |
| :------ | :------ |
| `left` | `number` |
| `right` | `number` |

#### Returns

`number`

#### Defined in

[balances-gpt.ts:5](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/balances-gpt.ts#L5)

___

### SafeSubtract

▸ **SafeSubtract**(`left`, `right`): `number`

#### Parameters

| Name | Type |
| :------ | :------ |
| `left` | `number` |
| `right` | `number` |

#### Returns

`number`

#### Defined in

[balances-gpt.ts:14](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/balances-gpt.ts#L14)

___

### SearchBalances

▸ **SearchBalances**(`targetAmount`, `balanceObjects`): (`number` \| `boolean`)[]

#### Parameters

| Name | Type |
| :------ | :------ |
| `targetAmount` | `number` |
| `balanceObjects` | [`Balance`](interfaces/Balance.md)[] |

#### Returns

(`number` \| `boolean`)[]

#### Defined in

[balances-gpt.ts:224](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/balances-gpt.ts#L224)

___

### SearchIdRangesForId

▸ **SearchIdRangesForId**(`id`, `idRanges`): [`number`, `boolean`]

#### Parameters

| Name | Type |
| :------ | :------ |
| `id` | `number` |
| `idRanges` | [`IdRange`](interfaces/IdRange.md)[] |

#### Returns

[`number`, `boolean`]

#### Defined in

[idRanges.ts:92](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/idRanges.ts#L92)

___

### SetBalanceForIdRanges

▸ **SetBalanceForIdRanges**(`ranges`, `amount`, `balanceObjects`): [`Balance`](interfaces/Balance.md)[]

#### Parameters

| Name | Type |
| :------ | :------ |
| `ranges` | [`IdRange`](interfaces/IdRange.md)[] |
| `amount` | `number` |
| `balanceObjects` | [`Balance`](interfaces/Balance.md)[] |

#### Returns

[`Balance`](interfaces/Balance.md)[]

#### Defined in

[balances-gpt.ts:183](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/balances-gpt.ts#L183)

___

### SortIdRangesAndMergeIfNecessary

▸ **SortIdRangesAndMergeIfNecessary**(`idRanges`): [`IdRange`](interfaces/IdRange.md)[]

#### Parameters

| Name | Type |
| :------ | :------ |
| `idRanges` | [`IdRange`](interfaces/IdRange.md)[] |

#### Returns

[`IdRange`](interfaces/IdRange.md)[]

#### Defined in

[idRanges.ts:3](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/idRanges.ts#L3)

___

### SubtractBalancesForIdRanges

▸ **SubtractBalancesForIdRanges**(`userBalanceInfo`, `ranges`, `balanceToRemove`): [`UserBalance`](interfaces/UserBalance.md)

#### Parameters

| Name | Type |
| :------ | :------ |
| `userBalanceInfo` | [`UserBalance`](interfaces/UserBalance.md) |
| `ranges` | [`IdRange`](interfaces/IdRange.md)[] |
| `balanceToRemove` | `number` |

#### Returns

[`UserBalance`](interfaces/UserBalance.md)

#### Defined in

[balances-gpt.ts:129](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/balances-gpt.ts#L129)

___

### UpdateBalancesForIdRanges

▸ **UpdateBalancesForIdRanges**(`ranges`, `newAmount`, `balanceObjects`): [`Balance`](interfaces/Balance.md)[]

#### Parameters

| Name | Type |
| :------ | :------ |
| `ranges` | [`IdRange`](interfaces/IdRange.md)[] |
| `newAmount` | `number` |
| `balanceObjects` | [`Balance`](interfaces/Balance.md)[] |

#### Returns

[`Balance`](interfaces/Balance.md)[]

#### Defined in

[balances-gpt.ts:22](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/balances-gpt.ts#L22)

___

### UpdatePermissions

▸ **UpdatePermissions**(`currPermissions`, `permissionDigit`, `value`): `number`

#### Parameters

| Name | Type |
| :------ | :------ |
| `currPermissions` | `number` |
| `permissionDigit` | `number` |
| `value` | `boolean` |

#### Returns

`number`

#### Defined in

[permissions.ts:97](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/permissions.ts#L97)

___

### ValidatePermissions

▸ **ValidatePermissions**(`permissions`): `void`

#### Parameters

| Name | Type |
| :------ | :------ |
| `permissions` | `number` |

#### Returns

`void`

#### Defined in

[permissions.ts:53](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/permissions.ts#L53)

___

### ValidatePermissionsUpdate

▸ **ValidatePermissionsUpdate**(`oldPermissions`, `newPermissions`): `void`

#### Parameters

| Name | Type |
| :------ | :------ |
| `oldPermissions` | `number` |
| `newPermissions` | `number` |

#### Returns

`void`

#### Defined in

[permissions.ts:60](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/permissions.ts#L60)

___

### checkIfApproved

▸ **checkIfApproved**(`userBalance`, `accountNumber`, `balancesToCheck`): `boolean`

#### Parameters

| Name | Type |
| :------ | :------ |
| `userBalance` | [`UserBalance`](interfaces/UserBalance.md) |
| `accountNumber` | `number` |
| `balancesToCheck` | [`Balance`](interfaces/Balance.md)[] |

#### Returns

`boolean`

#### Defined in

[badges.ts:311](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/badges.ts#L311)

___

### checkIfApprovedInTransferList

▸ **checkIfApprovedInTransferList**(`addresses`, `connectedUser`, `managerAccountNumber`): `boolean`

#### Parameters

| Name | Type |
| :------ | :------ |
| `addresses` | [`Addresses`](interfaces/Addresses.md) |
| `connectedUser` | [`BitBadgesUserInfo`](interfaces/BitBadgesUserInfo.md) |
| `managerAccountNumber` | `number` |

#### Returns

`boolean`

#### Defined in

[badges.ts:130](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/badges.ts#L130)

___

### checkIfIdRangesOverlap

▸ **checkIfIdRangesOverlap**(`idRanges`): `boolean`

#### Parameters

| Name | Type |
| :------ | :------ |
| `idRanges` | [`IdRange`](interfaces/IdRange.md)[] |

#### Returns

`boolean`

#### Defined in

[idRanges.ts:301](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/idRanges.ts#L301)

___

### convertToBitBadgesUserInfo

▸ **convertToBitBadgesUserInfo**(`accountInfo`): [`BitBadgesUserInfo`](interfaces/BitBadgesUserInfo.md)

#### Parameters

| Name | Type |
| :------ | :------ |
| `accountInfo` | [`AccountResponse`](interfaces/AccountResponse.md) |

#### Returns

[`BitBadgesUserInfo`](interfaces/BitBadgesUserInfo.md)

#### Defined in

[users.ts:4](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/users.ts#L4)

___

### convertToBitBadgesAddress

▸ **convertToBitBadgesAddress**(`address`): `string`

#### Parameters

| Name | Type |
| :------ | :------ |
| `address` | `string` |

#### Returns

`string`

#### Defined in

[chains.ts:12](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/chains.ts#L12)

___

### createCollectionFromMsgNewCollection

▸ **createCollectionFromMsgNewCollection**(`msgNewCollection`, `collectionMetadata`, `individualBadgeMetadata`, `connectedUser`, `claimItems`, `existingCollection?`): [`BitBadgeCollection`](interfaces/BitBadgeCollection.md)

#### Parameters

| Name | Type |
| :------ | :------ |
| `msgNewCollection` | `MessageMsgNewCollection` |
| `collectionMetadata` | [`BadgeMetadata`](interfaces/BadgeMetadata.md) |
| `individualBadgeMetadata` | [`BadgeMetadataMap`](interfaces/BadgeMetadataMap.md) |
| `connectedUser` | [`BitBadgesUserInfo`](interfaces/BitBadgesUserInfo.md) |
| `claimItems` | [`ClaimItemWithTrees`](interfaces/ClaimItemWithTrees.md)[] |
| `existingCollection?` | [`BitBadgeCollection`](interfaces/BitBadgeCollection.md) |

#### Returns

[`BitBadgeCollection`](interfaces/BitBadgeCollection.md)

#### Defined in

[badges.ts:29](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/badges.ts#L29)

___

### doesChainMatchName

▸ **doesChainMatchName**(`chain`, `name?`): `boolean`

#### Parameters

| Name | Type |
| :------ | :------ |
| `chain` | [`SupportedChain`](enums/SupportedChain.md) |
| `name?` | `string` |

#### Returns

`boolean`

#### Defined in

[chains.ts:87](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/chains.ts#L87)

___

### filterBadgeActivityForBadgeId

▸ **filterBadgeActivityForBadgeId**(`badgeId`, `activity`): [`TransferActivityItem`](interfaces/TransferActivityItem.md)[]

#### Parameters

| Name | Type |
| :------ | :------ |
| `badgeId` | `number` |
| `activity` | [`TransferActivityItem`](interfaces/TransferActivityItem.md)[] |

#### Returns

[`TransferActivityItem`](interfaces/TransferActivityItem.md)[]

#### Defined in

[badges.ts:10](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/badges.ts#L10)

___

### getAbbreviatedAddress

▸ **getAbbreviatedAddress**(`address`): `string`

#### Parameters

| Name | Type |
| :------ | :------ |
| `address` | `string` |

#### Returns

`string`

#### Defined in

[chains.ts:46](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/chains.ts#L46)

___

### getBadgeIdsToDisplayForPageNumber

▸ **getBadgeIdsToDisplayForPageNumber**(`collections?`, `startIdxNum`, `pageSize`): { `badgeIds`: `number`[] ; `collection`: [`BitBadgeCollection`](interfaces/BitBadgeCollection.md)  }[]

#### Parameters

| Name | Type | Default value |
| :------ | :------ | :------ |
| `collections` | { `badgeIds`: [`IdRange`](interfaces/IdRange.md)[] ; `collection`: [`BitBadgeCollection`](interfaces/BitBadgeCollection.md)  }[] | `[]` |
| `startIdxNum` | `number` | `undefined` |
| `pageSize` | `number` | `undefined` |

#### Returns

{ `badgeIds`: `number`[] ; `collection`: [`BitBadgeCollection`](interfaces/BitBadgeCollection.md)  }[]

#### Defined in

[badges.ts:197](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/badges.ts#L197)

___

### getBalanceAfterTransfer

▸ **getBalanceAfterTransfer**(`balance`, `startSubbadgeId`, `endSubbadgeId`, `amountToTransfer`, `numRecipients`): [`UserBalance`](interfaces/UserBalance.md)

#### Parameters

| Name | Type |
| :------ | :------ |
| `balance` | [`UserBalance`](interfaces/UserBalance.md) |
| `startSubbadgeId` | `number` |
| `endSubbadgeId` | `number` |
| `amountToTransfer` | `number` |
| `numRecipients` | `number` |

#### Returns

[`UserBalance`](interfaces/UserBalance.md)

#### Defined in

[balances.ts:14](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/balances.ts#L14)

___

### getBalanceAfterTransfers

▸ **getBalanceAfterTransfers**(`balance`, `transfers`): [`UserBalance`](interfaces/UserBalance.md)

#### Parameters

| Name | Type |
| :------ | :------ |
| `balance` | [`UserBalance`](interfaces/UserBalance.md) |
| `transfers` | [`TransfersExtended`](interfaces/TransfersExtended.md)[] |

#### Returns

[`UserBalance`](interfaces/UserBalance.md)

#### Defined in

[balances.ts:21](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/balances.ts#L21)

___

### getBlankBalance

▸ **getBlankBalance**(): [`UserBalance`](interfaces/UserBalance.md)

#### Returns

[`UserBalance`](interfaces/UserBalance.md)

#### Defined in

[balances.ts:5](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/balances.ts#L5)

___

### getChainForAddress

▸ **getChainForAddress**(`address`): `SupportedChain`

#### Parameters

| Name | Type |
| :------ | :------ |
| `address` | `string` |

#### Returns

`SupportedChain`

#### Defined in

[chains.ts:26](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/chains.ts#L26)

___

### getClaimsFromClaimItems

▸ **getClaimsFromClaimItems**(`balance`, `claimItems`): `Object`

#### Parameters

| Name | Type |
| :------ | :------ |
| `balance` | [`UserBalance`](interfaces/UserBalance.md) |
| `claimItems` | [`ClaimItemWithTrees`](interfaces/ClaimItemWithTrees.md)[] |

#### Returns

`Object`

| Name | Type |
| :------ | :------ |
| `claims` | [`Claims`](interfaces/Claims.md)[] |
| `undistributedBalance` | `any` |

#### Defined in

[claims.ts:63](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/claims.ts#L63)

___

### getIdRangesForAllBadgeIdsInCollection

▸ **getIdRangesForAllBadgeIdsInCollection**(`collection`): `any`

#### Parameters

| Name | Type |
| :------ | :------ |
| `collection` | [`BitBadgeCollection`](interfaces/BitBadgeCollection.md) |

#### Returns

`any`

#### Defined in

[badges.ts:20](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/badges.ts#L20)

___

### getMatchingAddressesFromTransferList

▸ **getMatchingAddressesFromTransferList**(`list`, `toAddresses`, `chain`, `managerAccountNumber`): `any`[]

#### Parameters

| Name | Type |
| :------ | :------ |
| `list` | [`TransferList`](interfaces/TransferList.md)[] |
| `toAddresses` | [`BitBadgesUserInfo`](interfaces/BitBadgesUserInfo.md)[] |
| `chain` | [`BitBadgesUserInfo`](interfaces/BitBadgesUserInfo.md) |
| `managerAccountNumber` | `number` |

#### Returns

`any`[]

#### Defined in

[badges.ts:153](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/badges.ts#L153)

___

### getMaxBatchId

▸ **getMaxBatchId**(`collection`): `number`

#### Parameters

| Name | Type |
| :------ | :------ |
| `collection` | [`BitBadgeCollection`](interfaces/BitBadgeCollection.md) |

#### Returns

`number`

#### Defined in

[badges.ts:245](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/badges.ts#L245)

___

### getMetadataForBadgeId

▸ **getMetadataForBadgeId**(`badgeId`, `metadataMap`): `undefined` \| [`BadgeMetadata`](interfaces/BadgeMetadata.md)

#### Parameters

| Name | Type |
| :------ | :------ |
| `badgeId` | `number` |
| `metadataMap` | [`BadgeMetadataMap`](interfaces/BadgeMetadataMap.md) |

#### Returns

`undefined` \| [`BadgeMetadata`](interfaces/BadgeMetadata.md)

#### Defined in

[badges.ts:183](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/badges.ts#L183)

___

### getMetadataMapObjForBadgeId

▸ **getMetadataMapObjForBadgeId**(`badgeId`, `metadataMap`): `undefined` \| { `badgeIds`: [`IdRange`](interfaces/IdRange.md)[] ; `metadata`: [`BadgeMetadata`](interfaces/BadgeMetadata.md) ; `uri`: `string`  }

#### Parameters

| Name | Type |
| :------ | :------ |
| `badgeId` | `number` |
| `metadataMap` | [`BadgeMetadataMap`](interfaces/BadgeMetadataMap.md) |

#### Returns

`undefined` \| { `badgeIds`: [`IdRange`](interfaces/IdRange.md)[] ; `metadata`: [`BadgeMetadata`](interfaces/BadgeMetadata.md) ; `uri`: `string`  }

#### Defined in

[badges.ts:170](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/badges.ts#L170)

___

### getNonTransferableDisallowedTransfers

▸ **getNonTransferableDisallowedTransfers**(): `any`[]

#### Returns

`any`[]

#### Defined in

[badges.ts:105](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/badges.ts#L105)

___

### getSupplyByBadgeId

▸ **getSupplyByBadgeId**(`badgeId`, `balances`): `number`

#### Parameters

| Name | Type |
| :------ | :------ |
| `badgeId` | `number` |
| `balances` | [`Balance`](interfaces/Balance.md)[] |

#### Returns

`number`

#### Defined in

[balances.ts:48](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/balances.ts#L48)

___

### getTransferListForSelectOptions

▸ **getTransferListForSelectOptions**(`isFromList`, `unregistered`, `users`, `all`, `none`, `everyoneExcept`): `undefined` \| [`TransferListWithUnregisteredUsers`](interfaces/TransferListWithUnregisteredUsers.md)

#### Parameters

| Name | Type |
| :------ | :------ |
| `isFromList` | `boolean` |
| `unregistered` | `string`[] |
| `users` | [`BitBadgesUserInfo`](interfaces/BitBadgesUserInfo.md)[] |
| `all` | `boolean` |
| `none` | `boolean` |
| `everyoneExcept` | `boolean` |

#### Returns

`undefined` \| [`TransferListWithUnregisteredUsers`](interfaces/TransferListWithUnregisteredUsers.md)

#### Defined in

[transferLists.ts:36](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/transferLists.ts#L36)

___

### getTransfersFromClaimItems

▸ **getTransfersFromClaimItems**(`claimItems`, `accounts`): [`TransfersExtended`](interfaces/TransfersExtended.md)[]

#### Parameters

| Name | Type |
| :------ | :------ |
| `claimItems` | [`ClaimItem`](interfaces/ClaimItem.md)[] |
| `accounts` | [`AccountMap`](interfaces/AccountMap.md) |

#### Returns

[`TransfersExtended`](interfaces/TransfersExtended.md)[]

#### Defined in

[claims.ts:6](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/claims.ts#L6)

___

### isAddressValid

▸ **isAddressValid**(`address`, `chain?`): `boolean`

#### Parameters

| Name | Type |
| :------ | :------ |
| `address` | `string` |
| `chain?` | `string` |

#### Returns

`boolean`

#### Defined in

[chains.ts:55](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/chains.ts#L55)

___

### isTransferListFull

▸ **isTransferListFull**(`transfersList`): `boolean`

#### Parameters

| Name | Type |
| :------ | :------ |
| `transfersList` | [`TransferList`](interfaces/TransferList.md)[] |

#### Returns

`boolean`

#### Defined in

[transferLists.ts:27](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/transferLists.ts#L27)

___

### populateFieldsOfOtherBadges

▸ **populateFieldsOfOtherBadges**(`individualBadgeMetadata`, `badgeIds`, `key`, `value`, `metadataToSet?`): [`BadgeMetadataMap`](interfaces/BadgeMetadataMap.md)

#### Parameters

| Name | Type |
| :------ | :------ |
| `individualBadgeMetadata` | [`BadgeMetadataMap`](interfaces/BadgeMetadataMap.md) |
| `badgeIds` | [`IdRange`](interfaces/IdRange.md)[] |
| `key` | `string` |
| `value` | `any` |
| `metadataToSet?` | [`BadgeMetadata`](interfaces/BadgeMetadata.md) |

#### Returns

[`BadgeMetadataMap`](interfaces/BadgeMetadataMap.md)

#### Defined in

[metadata.ts:61](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/metadata.ts#L61)

___

### updateMetadataForBadgeIdsFromIndexerIfAbsent

▸ **updateMetadataForBadgeIdsFromIndexerIfAbsent**(`badgeIdsToDisplay`, `collection`): `number`[]

#### Parameters

| Name | Type |
| :------ | :------ |
| `badgeIdsToDisplay` | `number`[] |
| `collection` | [`BitBadgeCollection`](interfaces/BitBadgeCollection.md) |

#### Returns

`number`[]

#### Defined in

[badges.ts:264](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/badges.ts#L264)

___

### updateMetadataMap

▸ **updateMetadataMap**(`currMetadataMap`, `metadata`, `badgeIds`, `uri`): [`BadgeMetadataMap`](interfaces/BadgeMetadataMap.md)

#### Parameters

| Name | Type |
| :------ | :------ |
| `currMetadataMap` | [`BadgeMetadataMap`](interfaces/BadgeMetadataMap.md) |
| `metadata` | [`BadgeMetadata`](interfaces/BadgeMetadata.md) |
| `badgeIds` | [`IdRange`](interfaces/IdRange.md) |
| `uri` | `string` |

#### Returns

[`BadgeMetadataMap`](interfaces/BadgeMetadataMap.md)

#### Defined in

[metadata.ts:5](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/metadata.ts#L5)

___

### updateTransferListAccountNums

▸ **updateTransferListAccountNums**(`accountNumber`, `remove`, `transferListAddresses`): [`Addresses`](interfaces/Addresses.md)

#### Parameters

| Name | Type |
| :------ | :------ |
| `accountNumber` | `number` |
| `remove` | `boolean` |
| `transferListAddresses` | [`Addresses`](interfaces/Addresses.md) |

#### Returns

[`Addresses`](interfaces/Addresses.md)

#### Defined in

[transferLists.ts:5](https://github.com/trevormil/bitbadges-sdk/blob/80ff4be/src/transferLists.ts#L5)
