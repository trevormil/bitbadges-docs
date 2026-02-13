# Transaction Context

For the signer, you will need their address, sequence, and account number. This can be done via our API like below or you can query directly via a blockchain node.

```typescript
// https://api.bitbadges.io/api/v0/user?address=bb1...
const res = await BitBadgesApi.getAccount({ address: '...' });
const account = res.account;
const { accountNumber, sequence, publicKey } = account;
if (Number(accountNumber) <= 0) {
    // TODO: They are unregistered. Send them dust to register.
}
```

For Cosmos-based wallets, you will need the public key. This may be available in the account response if the user has already interacted with BitBadges, but if this is first time, you can fetch it like below.

```typescript
const getPublicKey = async () => {
    const account = await window?.keplr?.getKey('bitbadges-1');
    if (!account) return '';
    return Buffer.from(account.pubKey).toString('base64');
};
```

Then, generate the context.

```typescript
//Pre-Reqs: Ensure users are registered (i.e. have a valid account number) or else this will fail
const txContext = {
    testnet: false,
    sender: {
        //Must be in native format ('bb1..')
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
    // Optional: Add evmAddress to enable EVM precompile conversion
    // When provided, createTransactionPayload will return evmTx field in payload
    evmAddress: '0x1234...', // Ethereum address (0x-prefixed)
};
```

**For EVM Transactions:**

When using Ethereum wallets, add the `evmAddress` field to enable automatic conversion to EVM precompile calls. The SDK will:
- Convert Cosmos SDK messages to EVM precompile function calls
- Return both Cosmos payload and EVM transaction details in `TransactionPayload`
- Handle address conversion between BitBadges (bb1...) and Ethereum (0x...) formats

See [Signing - Ethereum](./signing-ethereum.md) for complete EVM transaction examples.

**Fee Generation**

Oftentimes, transactions will not require fees depending on network congestion.

We leave this up to you to implement. You can get the gas used from simulating the transaction (see later in this tutorial). For our frontend, we currently use a base gas price of 0.025 per unit.

```typescript
const baseGasPrice = 0.025;
const feeInUbadge = BigIntify(Math.round(Number(gasUsed) * baseGasPrice));
```
