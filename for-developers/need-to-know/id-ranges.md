# 🔢 ID Ranges

**What are ID Ranges?**

A core type behind the scenes in BitBadges s the IdRange type.

The IdRange type is simple. It is a struct that defines a start and an end number. IdRanges are used mainly for badge IDs but also for other uses like account ID ranges.

```protobuf
message IdRange {
    uint64 start = 1;
    uint64 end = 2;
}
```

For example, transferring badges will require an IdRange\[] of badgeIds to be specified. If you say to transfer \[{ start: 1, end: 10}, {start: 20, end: 50}], it will transfer the badge IDs 1-10 and 20-50.

**Why do we use ID Ranges?**

It enables batch operations which makes our token standard much more computationally efficient than existing solutions.

Since all badges in a collection will have an ID from 1 to N and all accounts will also be mapped to a unique account number, this enables sending tokens with IDs 1 to 1000 in a single, low-cost transfer or freezing the accounts 1-100000000 in one simple transaction.
