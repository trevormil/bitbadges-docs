# Balances / Transfers

Typically, you may think of a balance in two parts: what you own and the amount you own. However, we introduce a third part: ownership times. For example, Bob owns x10 of Badge IDs 1-100 from January to March.

### **Benefits**

This enables many neat features such as:

* Subscription Tokens: Transfer auto-expiring tokens for subscriptions without having to revoke or trigger a blockhain transaction.
* Token Unlocks: Many projects have stock / token unlock schedules that are not currently enforced with code (just trust). This allows one to define and enforce it natively.
* Lending: Transfer your badges for a certain time without needing an escrow.

### **Examples**

This may be a little confusing to wrap ones head around (especially with transfers), so lets walk through some examples.

Lets reuse the starting balance from above: Bob owns x10 of Badge IDs 1-100 from January to March.



**Example 1:** Bob transfers x10 of Badge IDs 1-100 from January to February to Alice.

Result: Bob owns  x10 of Badge IDs 1-100 from February to March and Alice owns x10 of Badge IDs 1-100 from January to February.



**Example 2**: Bob transfers x5 of Badge IDs 1-100 from January to March to Alice.

Result: They both now own x5 of Badge IDs 1-100 from January to March.



**Example 3**: Bob transfers x10 of Badge IDs 1-50 from January to February to Alice.

Result: Bob owns x10 of Badge IDs 1-50 from February to March **and** x10 of Badge IDs 50-100 from January to March. Alice owns  x10 of Badge IDs 1-50 from January to February.
