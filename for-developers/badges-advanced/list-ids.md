# List IDs

As you may have noticed, we use a list ID system for the from, to, and initiatedBy in the collection approval interfaces.&#x20;

```typescript
{
    ...restOfApproval,
    "fromListId": "Mint",
    "initiatedByListId": "bb1..."
}
```

A summary is:

* These can either be registered on-chain as a reusable list with an ID and referenced
* They can follow a specific pattern for reserved IDs.&#x20;
  * Plain addresses (BitBadges supported only) as-is
  * Separate multiple addresses in list with a :
  * Prefix with a ! for inversion (blacklist)

```
Mint
```

```
bb1...:bb1....
```

Learn more

{% content-ref url="../core-concepts/address-lists-lists.md" %}
[address-lists-lists.md](../core-concepts/address-lists-lists.md)
{% endcontent-ref %}

