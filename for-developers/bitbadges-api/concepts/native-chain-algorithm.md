# Native Chain Algorithm

### How is a user's "native" chain determined?

You may have noticed that on the BitBadges site and other places, a user's "preferred" or "main" blockchain is remembered and auto-populated. This is how we populate the **chain** and **address** field in account route responses. We determine the main chain in the following order.

1. Chain of last signed BitBadges transaction
2. Chain of last sign in on BitBadges app
3. Try to check any transaction history (e.g. submitted any transactions on Ethereum mainnet chain?)
4. Requested address format
5. Guess / default to Ethereum since it is the most popular
