# Timed Updates

Besides approved transfers, a collection also has many other details that can be customized, such as metadata, standards, etc. Some of these are timeline-based values ([see here](../concepts/timelines.md)).



Below, we will walk through setting a timeline value and locking it.



**Set a Timeline Value**

Setting timeline values can be done via MsgUpdateCollection and setting the corresponding update flag to be true such as:

<pre class="language-go"><code class="lang-go"><strong>BadgeMetadataTimeline: {
</strong>    {
        TimelineTimes: GetOneUintRanges(),  //1-1
        BadgeMetadata: ... //value 1
    },
    {
        TimelineTimes: GetTwoUintRanges(), //2-2
        BadgeMetadata: ... //value 2
    }
},
UpdateBadgeMetadataTimeline: true
</code></pre>

This sets the badge metadata to be different values at time 1 vs time 2.



Like BadgesToCreate, the first update upon initial creation is "free", but all subsequent updates require obeying the corresponding permission.



**Locking a Timeline Value**

Lets first look at the CanUpdateManager permission. This is a standard TimedUpdatePermission. By setting it to the values below, we lock the ability to update the manager at all times (meaning its value can nver be updated).

```go
CanUpdateManager: []*types.TimedUpdatePermission{
	{
		DefaultValues: &types.TimedUpdateDefaultValues{
			PermittedTimes: []*types.UintRange{},
			ForbiddenTimes: GetFullUintRanges(),
			TimelineTimes:  GetFullUintRanges(),
		},
		Combinations: []*types.TimedUpdateCombination{{
			//empty combinations (use default values)
		}},
	},
}
```



Certain timed update permissions also include specifying unique values for specific badge IDs, such as badge metadata. For these, we can set it to the following to freeze the updates of all badges.

```go
CanUpdateBadgeMetadata: []*types.TimedUpdateWithBadgeIdsPermission{
	{
		DefaultValues: &types.TimedUpdateWithBadgeIdsDefaultValues{
			PermittedTimes: []*types.UintRange{},
			ForbiddenTimes: GetFullUintRanges(),
			TimelineTimes:  GetFullUintRanges(),
			BadgeIds:  GetFullUintRanges(),
		},
		Combinations: []*types.TimedUpdateWithBadgeIdsCombination{{
			//empty combinations (use default values)
		}},
	},
}
```

Or, we can freeze specific badges (such as only badge ID 1 here).

```go
CanUpdateBadgeMetadata: []*types.TimedUpdateWithBadgeIdsPermission{
	{
		DefaultValues: &types.TimedUpdateWithBadgeIdsDefaultValues{
			PermittedTimes: []*types.UintRange{},
			ForbiddenTimes: GetFullUintRanges(),
			TimelineTimes:  GetFullUintRanges(),
			BadgeIds:  GetOneUintRanges(), //1-1
		},
		Combinations: []*types.TimedUpdateWithBadgeIdsCombination{{
			//empty combinations (use default values)
		}},
	},
}
```

Or, we can freeze specific badges (such as only badge ID 1 for time 1 here) for specific times.

```go
CanUpdateBadgeMetadata: []*types.TimedUpdateWithBadgeIdsPermission{
	{
		DefaultValues: &types.TimedUpdateWithBadgeIdsDefaultValues{
			PermittedTimes: []*types.UintRange{},
			ForbiddenTimes: GetFullUintRanges(),
			TimelineTimes:  GetOneUintRanges(), //1-1
			BadgeIds:  GetOneUintRanges(), //1-1
		},
		Combinations: []*types.TimedUpdateWithBadgeIdsCombination{{
			//empty combinations (use default values)
		}},
	},
}
```

Or, we can freeze all badges for specific times.

```go
CanUpdateBadgeMetadata: []*types.TimedUpdateWithBadgeIdsPermission{
	{
		DefaultValues: &types.TimedUpdateWithBadgeIdsDefaultValues{
			PermittedTimes: []*types.UintRange{},
			ForbiddenTimes: GetFullUintRanges(),
			TimelineTimes:  GetOneUintRanges(), //1-1
			BadgeIds:  GetFullUintRanges(), 
		},
		Combinations: []*types.TimedUpdateWithBadgeIdsCombination{{
			//empty combinations (use default values)
		}},
	},
}
```
