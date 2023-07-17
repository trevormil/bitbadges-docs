# 📊 Balances

For an overview, first read [Balances / Transfers](../../overview/how-it-works/balances-transfers.md).

```protobuf
message Balance {
  string amount = 1  [(gogoproto.customtype) = "Uint", (gogoproto.nullable) = false];
  repeated UintRange ownedTimes = 2;
  repeated UintRange badgeIds = 3;
}
```



**Interpreting Balances**

When interpreting balances, there are certain rules to keep in mind. If we have multiple ranges of badge IDs and ownership times defined within a single Balance structure, it means that we own all possible combinations. You can think of it as a nested loop, without any "OR" logic involved (e.g. for all badge IDs and for all owned times, we own X amount).

For example, lets say we have a balance of&#x20;

<pre class="language-json"><code class="lang-json"><strong>{ 
</strong>    amount: 1, 
    badgeIds: [{ start: 1, end: 10}, {start: 20, end: 30}], 
    ownedTimes: [{start: 20, end: 50}, {start: 100, end: 200}] 
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
    ownedTimes: [{start: 100, end: 200}] 
}
```

```
{ 
    amount: 1, 
    badgeIds: [{start: 20, end: 30}], 
    ownedTimes: [{start: 20, end: 50}}] 
}
```

**Duplicate IDs**

If you specify duplicate badge IDs in balances such as:

```
{ 
    amount: 1, 
    badgeIds: [{ start: 1, end: 10}, {start: 1, end: 10}], 
    ownedTimes: [{start: 100, end: 200}] 
}
```

This is equivalent and will be treated as:

```
{ 
    amount: 2, 
    badgeIds: [{ start: 1, end: 10}], 
    ownedTimes: [{start: 100, end: 200}] 
}
```
