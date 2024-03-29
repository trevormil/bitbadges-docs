# Balances Action Permission

The BalancesAction permission denotes for what (badge ID, ownership times), can an action be executed? For example, can I create more of badge ID 1-10 circulating in 2023, or can I not?

This permission refers to the UPDATABILITY of the balances and has no bearing on what the circulating supplys are currently set to.

```json
"collectionPermissions": {
    "canCreateMoreBadges": [...],
    ...
}
```

```typescript
export interface BalancesActionPermission<T extends NumberType> {
  badgeIds: UintRange<T>[];
  ownershipTimes: UintRange<T>[];
  
  permanentlyPermittedTimes: UintRange<T>[];
  permanentlyForbiddenTimes: UintRange<T>[];
}
```

{% content-ref url="../creating-badges.md" %}
[creating-badges.md](../creating-badges.md)
{% endcontent-ref %}

```json
"canCreateMoreBadges": [
  {
    "badgeIds": [
      {
        "start": "1",
        "end": "18446744073709551615"
      }
    ],
    "ownershipTimes": [
      {
        "start": "1",
        "end": "18446744073709551615"
      }
    ],
    "permanentlyPermittedTimes": [],
    "permanentlyForbiddenTimes": [
      {
        "start": "1",
        "end": "18446744073709551615"
      }
    ]
  }
]
```
