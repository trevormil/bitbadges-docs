# Profile Information

We additionally allow you to customize your profile even further with details such as providing your socials, usernames, and more. This is handled off-chain via the indexer detabase.

These details will be returned along with the other account information (such as current balances, sequence, addresses, account numbers, etc).

```typescript
export interface ProfileInfoBase<T extends NumberType> {
  seenActivity?: T;
  createdAt?: T;

  //ProfileDoc customization
  discord?: string
  twitter?: string
  github?: string
  telegram?: string
  readme?: string
}
```

