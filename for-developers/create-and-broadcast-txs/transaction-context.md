# Transaction Context

You have two options for generating, signing, and brodcasting messages.

1. Use [https://bitbadges.io/dev/broadcast](https://bitbadges.io/dev/broadcast) - This is a visual UI that you can simply copy and paste your transaction Msg contents into. Generating all additional transaction details, gas, fees, and signing is all outsourced to the user interface. This is the recommended option if you do not require programmatically submitting TXs.
2. Generate, sign, and broadcast directly to a running blockchain node. This is more technical and has more steps but can be done programmatically.

If you plan to use Option 1, you may proceed to the next page because generating the transaction context is already handled via the user interface.

If you plan to use option 2, see below.

### Generating Transaction Context

The first step is to fetch and identify the transaction context and the account details for who is going to sign. You will need the following information below.

```typescript
import { createTxMsgSend, SupportedChain } from 'bitbadgesjs-sdk'

//TODO: Fetch the account details (see below)

//Pre-Reqs: Ensure users are registered (i.e. have a valid account number) or else this will fail
const txContext = {
  testnet: false,
  sender: {
    //Must be in native format ('0xabc..' vs 'bc1...' etc)
    address: account.address,
    sequence: account.sequence,
    accountNumber: account.accountNumber,
    //Public key is only needed for Cosmos native signatures (see below). '' if non-Cosmos
    publicKey: account.publicKey
  }, 
  //TODO: adjust accordingly
  fee: {
    amount: `0`,
    denom: 'ubadge',
    gas: `400000`
  },
  memo: ''
};
```

For a user who has not yet interacted with the blockchain, the fetched public key will be null and accountNumber will be -1. To get an account number, they need to receive $BADGE somehow (this is also a pre-requisite to pay for any fees).

**Get Public Key - Cosmos**

For Keplr / Cosmos, you will need to specify the public key in the txContext. You can simply use getKey() then convert to base64.&#x20;

```typescript
const getPublicKey = async () => {
    const account = await window?.keplr?.getKey('bitbadges-1')
    if (!account) return '';
    return Buffer.from(account.pubKey).toString('base64')
}
```

**Fee**

Generating the fee can be tricky. It should be reasonable for the current gas prices but also not too expensive. To get the **gas**, we recommend simulating the transaction right before broadcasting to see how much gas it uses on a dry run. We will walk you through how to do this in the broadcast tutorial. You can also fetch the gas prices via the BitBadgesApi.getStatus() route.

**Sender Details**

To fetch a user's account details, the easiest way is to use the routes from the BitBadges API in [Users](broken-reference/). You can also query a node directly.&#x20;

This will return the user's cosmos address, account ID, sequence (nonce), and public key. If the user has previously interacted with the blockchain, all this information will already be populated.

<figure><img src="../../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>
