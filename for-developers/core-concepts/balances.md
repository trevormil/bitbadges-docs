# 📊 Balances

For an overview, first read [Balances / Transfers](../../overview/concepts/time-dependent-ownership.md).

```typescript
export interface Balance<T extends NumberType> {
  amount: T;
  badgeIds: UintRange<T>[]
  ownershipTimes: UintRange<T>[]
}
```

**Interpreting Balances**

When interpreting balances, there are certain rules to keep in mind. If we have multiple ranges of badge IDs and ownership times defined within a single Balance structure, it means that we own all possible combinations.&#x20;

```
for (balance of balances) {
    for (badgeIdRange of balance.badgeIds) {
        for (ownershipTimeRange of balanace.ownershipTimes) {
            //User owns x(balance.amount) of (badgeIdRange) for the times (ownershipTimeRange)
        }
    }
}
```

For example, lets say we have a balance of&#x20;

<pre class="language-json"><code class="lang-json"><strong>{ 
</strong>    amount: 1, 
    badgeIds: [{ start: 1, end: 10}, {start: 20, end: 30}], 
    ownershipTimes: [{start: 20, end: 50}, {start: 100, end: 200}] 
}
</code></pre>

This can be expanded and thought of as owning:

* x1 of IDs 1-10 from times 20-50&#x20;
* x1 of IDs 1-10 from times 100-200
* x1 of IDs 20-30 from times 20-50
* x1 of IDs 20-30 from times 100-200

If we wanted to subtract the first set of balances (x1 of IDs 1-10 from times 20-50), we would then need to represent it as two separate balances:&#x20;

```
{ 
    amount: 1, 
    badgeIds: [{ start: 1, end: 10}, {start: 20, end: 30}], 
    ownershipTimes: [{start: 100, end: 200}] 
}
```

```
{ 
    amount: 1, 
    badgeIds: [{start: 20, end: 30}], 
    ownershipTimes: [{start: 20, end: 50}}] 
}
```

**Duplicates**

If you specify duplicate badge IDs in balances such as:

```
{ 
    amount: 1, 
    badgeIds: [{ start: 1, end: 10}, {start: 1, end: 10}], 
    ownershipTimes: [{start: 100, end: 200}] 
}
```

This is equivalent and will be treated as:

```
{ 
    amount: 2, 
    badgeIds: [{ start: 1, end: 10}], 
    ownershipTimes: [{start: 100, end: 200}] 
}
```
