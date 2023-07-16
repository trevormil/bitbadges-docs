# Creating Badges

This will walk you through the process of creating badges and maintaining / defining their total supply over time.&#x20;

**Initial Creation**

During the initial creation transaction (MsgUpdateCollection), you can specify the badge amounts and supplys you wish to create via the BadgesToCreate field. This will transfer these badges to the special "Mint" address.&#x20;

The initial creation transaction's BadgesToCreate are considered "free". The creator can mint as much as they want in this initial transaction.

```go
BadgesToCreate: []*types.Balance{
	{
		Amount:         sdkmath.NewUint(1),  //x1
		BadgeIds:       GetFullUintRanges(), //1-MAX BADGE ID
		OwnedTimes: 	GetFullUintRanges(), //1-MAX TIME
	}
}
```

From the "Mint" address, you can transfer according to the collection's approved transfers (see [Distributing Badges](distributing-badges.md)).

**Creating More Over Time**&#x20;

Over time, you may want to add more badges to the collection. However, the BadgesToCreate are no longer considered "free", as they were in the initial creation transaction. They must obey the CanCreateMoreBadges permission (see Locking Total Supply below).

**Locking Total Supply**

Pre-Reading: [Permissions](../concepts/permissions.md)

The CanCreateMoreBadges permission defines what badges and at what ownership times, the manager can create more badges (no restriction on amounts). By default (when CanCreateMoreBadges is empty), the manager can mint as many badges as they want at any time they want because permissions are by default allowed. This can simply be done via another MsgUpdateCollection call and specifying BadgesToCreate.

By setting CanCreateMoreBadges, you can update the ability to create new badges to be permitted or forbidden. This can be done via the MsgUpdateCollection (either in the initial creation transaction) or a subsequent call.

For example, the following value for CanCreateMoreBadges locks the entire supply of badges

```go
{
	DefaultValues: &types.BalancesActionDefaultValues{
		BadgeIds:       GetFullUintRanges(), //1-MAX BADGE ID
		OwnedTimes: 	GetFullUintRanges(), //1-MAX TIME
		PermittedTimes: []*types.UintRange{},
		ForbiddenTimes: GetFullUintRanges(), //1-MAX TIME
	},
	Combinations: []*types.BalancesActionCombination{{
		//Empty options (use defaults)
	}},
}

```

The following only restricts the manager from creating Badge ID 1.

```go
{
        DefaultValues: &types.BalancesActionDefaultValues{
		BadgeIds:       GetOneUintRanges(), //1-1
		OwnedTimes: 	GetFullUintRanges(), //1-MAX TIME
		PermittedTimes: []*types.UintRange{},
		ForbiddenTimes: GetFullUintRanges(), //1-MAX TIME
	},
	Combinations: []*types.BalancesActionCombination{{
		//Empty options (use defaults)
	}},
}
```

And, the following forbids the manager from creating new supply of Badge ID 1 that is ownable for the year 2024 (e.g. a token unlock).

```go
{
        DefaultValues: &types.BalancesActionDefaultValues{
		BadgeIds:       GetOneUintRanges(), //1-1
		OwnedTimes: 	Get2024UintRanges(), //2024START - 2024END
		PermittedTimes: []*types.UintRange{},
		ForbiddenTimes: GetFullUintRanges(), //1-MAX TIME
	},
	Combinations: []*types.BalancesActionCombination{{
		//Empty options (use defaults)
	}},
}
```

As a reminder, remember that permissions are by default ALLOWED if not explicitly forbidden and also FIRST-MATCH-ONLY (see [Permissions](../concepts/permissions.md)).

**Need to Restrict Amounts As Well?**

You may have noticed that the CanCreateMoreBadges is an all-or-nothing permission, either the  manager can create an unlimited amount of badges or none at all. Sometimes, a use case may require to restrict the amounts that can be created as well, such as with token unlocks.&#x20;

For this, you should use the "Mint" address as an escrow and precreate all badges that you may potentially need in the future. You should 1) create the badges which are sent to "Mint" address, 2) lock the ability to create more badges, and then 3) use the CollectionApprovedTransfers and CanUpdateCollectionApprovedTransfers to keep badges escrowed in the "Mint" address for the desired times and amounts (see [Distributing Badges](distributing-badges.md)).
