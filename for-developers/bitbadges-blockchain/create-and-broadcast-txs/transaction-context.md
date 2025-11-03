# Transaction Context

For the signer, you will need their address, sequence, and account number. This can be done via our API like below or you can query directly via a blockchain node.

```typescript
// https://api.bitbadges.io/api/v0/user?address=0x
const res = await BitBadgesApi.getAccount({ address: '...' });
const account = res.account;
const { accountNumber, sequence, publicKey } = account; 
if (Number(accountNumber) <= 0) {
  // TODO: They are unregistered. Send them dust to register.
}
```

If you are signing with a Cosmos-based wallet, you will need the public key as well. All others (ETH, SOL, BTC) do not require this. This may be available in the account response if the user has already interacted with BitBadges, but if this is first time, you can fetch it like below.

```typescript
const getPublicKey = async () => {
    const account = await window?.keplr?.getKey('bitbadges-1');
    if (!account) return '';
    return Buffer.from(account.pubKey).toString('base64');
};
```

Then,  generate the context.

```typescript
//Pre-Reqs: Ensure users are registered (i.e. have a valid account number) or else this will fail
const txContext = {
    testnet: false,
    sender: {
        //Must be in native format ('0xabc..' vs 'bc1...' etc)
        address: account.address,
        sequence: account.sequence,
        accountNumber: account.accountNumber,
        //Public key is only needed for Cosmos native signatures. Leave "" if not.
        publicKey: account.publicKey,
    },
    //TODO: adjust accordingly
    fee: {
        amount: `0`,
        denom: 'ubadge',
        gas: `400000`,
    },
    memo: '',
};
```

**Fee Generation**

Oftentimes, transactions will not require fees depending on network congestion.

We leave this up to you to implement. You can get the gas used from simulating the transaction (see later in this tutorial). For our frontend, we currently use a base gas price of 0.025 per unit.

```typescript
const baseGasPrice = 0.025; 
const feeInUbadge = BigIntify(Math.round(Number(gasUsed) * baseGasPrice));
```
