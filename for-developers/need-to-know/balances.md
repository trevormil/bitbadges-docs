# 💸 Balances

### UserBalance

Balances of a badge are stored on the blockchain in the following format.&#x20;

First, we have balances, which is a [Balance](balances.md#balances)\[], representing a user's owned badges.&#x20;

Second, we have approvals, which is an [Approval](balances.md#approvals)\[], representing a user's approvals.

```protobuf
message UserBalance {
    repeated Balance balances = 1; 
    repeated Approval approvals = 2;
}
```

### Balances

The **Balance** type is stored in the following format where the balance corresponds to all the badgeIds defined.&#x20;

For example, if we have { balance: 2, badgeIds: \[{start: 1, end: 10}]}, this represents a balance of x2 for the badge IDs 1-10.&#x20;

```protobuf
message Balance {
    uint64 balance = 1;
    repeated IdRange badgeIds = 2;
}
```

This Balance type is used in multiple areas (e.g. balances, approvals, badge supplys, etc).

### Approvals

An approval represents how many badges the specified address (account ID number) can transfer on the owner's behalf.

```protobuf
message Approval {
    uint64 address = 1;
    repeated Balance balances = 2; 
}
```
