# Signing a Transaction

## BitBadges Transaction Signing and Generation Guide

This guide explains how to create, fund, and sign transactions for the BitBadges blockchain using Ethereum-compatible wallets and the BitBadges SDK.

### Prerequisites

```bash
npm install @cosmjs/crypto @cosmjs/proto-signing @cosmjs/stargate
npm install bitbadgesjs-sdk ethers
```

### Step 1: Create ETH Wallet

There are several ways to create an Ethereum wallet for BitBadges transactions:

#### Option A: Create Random Wallet

```javascript
import { ethers } from 'ethers';

// Create a random wallet
const ethWallet = ethers.Wallet.createRandom();
console.log('Address:', ethWallet.address);
console.log('Private Key:', ethWallet.privateKey);
console.log('Mnemonic:', ethWallet.mnemonic.phrase);
```

#### Option B: From Existing Mnemonic

```javascript
import { ethers } from 'ethers';

const mnemonic = "your twelve word mnemonic phrase here";
const ethWallet = ethers.Wallet.fromMnemonic(mnemonic);
```

#### Option C: From Private Key

```javascript
import { ethers } from 'ethers';

const privateKey = "0x..."; // Your private key
const ethWallet = new ethers.Wallet(privateKey);
```

### Step 2: Fund the Wallet

#### Option A: Using Cosmos SDK Direct Transfer (Requires Funded Account)

The easiest way to do this is through the BitBadges website. If you want to do it programmatically, you can do so via the snippet below.

```javascript
import { DirectSecp256k1HdWallet } from '@cosmjs/proto-signing';
import { SigningStargateClient } from '@cosmjs/stargate';
import { convertToBitBadgesAddress } from 'bitbadgesjs-sdk';

// If you have a funded mnemonic account
const fromMnemonic = "your funded account mnemonic";
const wallet = await DirectSecp256k1HdWallet.fromMnemonic(fromMnemonic);
const [firstAccount] = await wallet.getAccounts();

// Connect to BitBadges RPC
const rpcUrl = "https://rpc.bitbadges.io"; // or your preferred RPC
const signingClient = await SigningStargateClient.connectWithSigner(rpcUrl, wallet);

// Transfer tokens to your new wallet
const amount = {
  denom: 'ubadge', // BitBadges native token
  amount: '1000000' // Amount in micro-units
};

const fee = {
  amount: [{ denom: 'ubadge', amount: '5000' }],
  gas: '200000'
};

const result = await signingClient.sendTokens(
  firstAccount.address,
  convertToBitBadgesAddress(ethWallet.address),
  [amount],
  fee
);
```

### Step 3: Get Current Account Number and Sequence

#### Option A: Using Cosmos RPC Query

```javascript
import { SigningStargateClient } from '@cosmjs/stargate';
import { convertToBitBadgesAddress } from 'bitbadgesjs-sdk';

const rpcUrl = "https://node.bitbadges.io/rpc";
const client = await SigningStargateClient.connect(rpcUrl);

const bitbadgesAddress = convertToBitBadgesAddress(ethWallet.address);
const account = await client.getAccount(bitbadgesAddress);

if (!account) {
  throw new Error('Account not found - ensure it has been funded');
}

console.log('Account Number:', account.accountNumber);
console.log('Sequence:', account.sequence);
```

#### Option B: Direct REST API Query

```javascript
const bitbadgesAddress = convertToBitBadgesAddress(ethWallet.address);
const restUrl = `https://node.bitbadges.io/api/cosmos/auth/v1beta1/accounts/${bitbadgesAddress}`;

const response = await fetch(restUrl);
const data = await response.json();

const accountNumber = data.account.account_number;
const sequence = data.account.sequence;
```

### Step 4: Generate Transaction Payload

#### Get Public Key First

For Ethereum signatures, this is automatically detected via the SDK, so you do not need to pre-generate.

```javascript
const base64PubKey = '';
```

#### Create Transaction Context

```javascript
import { createTransactionPayload, convertToBitBadgesAddress, Numberify } from 'bitbadgesjs-sdk';

const sender = {
  address: ethWallet.address, // Ethereum address - not BitBadges address
  sequence: sequence,
  accountNumber: Numberify(accountNumber),
  pubkey: base64PubKey,
  publicKey: base64PubKey
};

const txContext = {
  testnet: false,
  sender,
  memo: 'My BitBadges transaction',
  fee: {
    denom: 'ubadge',
    amount: '5000',
    gas: '200000'
  }
};
```

#### Create Transaction Messages

```javascript
import { badges } from 'bitbadgesjs-sdk';

// Example: Create a badge collection
const createCollectionMsg = new badges.MsgUniversalUpdateCollection({
  creator: convertToBitBadgesAddress(ethWallet.address),
  collectionId: '0', // 0 for new collection
  badgesToCreate: [
    {
      amount: '100',
      badgeIds: [{ start: '1', end: '100' }]
    }
  ],
  managerTimeline: [
    {
      manager: convertToBitBadgesAddress(ethWallet.address),
      timelineTimes: [{ start: '1', end: Number.MAX_SAFE_INTEGER.toString() }]
    }
  ],
  updateManagerTimeline: true,
  updateBadgeMetadataTimeline: true,
  updateCollectionMetadataTimeline: true
});

// Example: Transfer badges
const transferMsg = new badges.MsgTransferBadges({
  creator: convertToBitBadgesAddress(ethWallet.address),
  collectionId: '1',
  transfers: [
    {
      from: convertToBitBadgesAddress(ethWallet.address),
      toAddresses: ['bb1recipient...'],
      balances: [
        {
          amount: '1',
          badgeIds: [{ start: '1', end: '1' }],
          ownershipTimes: [{ start: '1', end: '18446744073709551615' }]
        }
      ]
    }
  ]
});

const messages = [createCollectionMsg]; // Add your messages here
```

#### Generate Transaction Payload

```javascript
const txPayload = createTransactionPayload(txContext, messages);

if (!txPayload.txnString) {
  throw new Error('Failed to generate transaction payload');
}

console.log('Transaction to sign:', txPayload.txnString);
```

### Step 5: Simulate Transaction

```typescript
import { createTxBroadcastBody } from 'bitbadgesjs-sdk';

const simulationBroadcastBody = createTxBroadcastBody(txContext, messages, '');
```

#### Option A: Using BitBadges API Simulation Endpoint

```javascript
const simulateUrl = "https://api.bitbadges.io/api/v0/simulate";

const simulationResponse = await fetch(simulateUrl, {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    tx_bytes: simulationBroadcastBody.tx_bytes,
    // Some APIs accept the full broadcast body for simulation
    ...simulationBroadcastBody
  })
});

const simulationResult = await simulationResponse.json();

if (simulationResult.error) {
  throw new Error(`Simulation failed: ${simulationResult.error}`);
}

// Extract gas used from simulation
const gasUsed = simulationResult.gas_info?.gas_used || simulationResult.gasUsed;
const gasWanted = simulationResult.gas_info?.gas_wanted || simulationResult.gasWanted;

console.log('Simulation successful!');
console.log('Gas used:', gasUsed);
console.log('Gas wanted:', gasWanted);
```

#### Option B: Using Cosmos REST API Simulation

```javascript
const restSimulateUrl = "https://rest.bitbadges.io/cosmos/tx/v1beta1/simulate";

const restSimulationResponse = await fetch(restSimulateUrl, {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    tx_bytes: simulationBroadcastBody.tx_bytes
  })
});

const restSimulationResult = await restSimulationResponse.json();

if (restSimulationResult.code && restSimulationResult.code !== 0) {
  throw new Error(`Simulation failed: ${restSimulationResult.raw_log}`);
}

const gasInfo = restSimulationResult.gas_info;
console.log('Gas used:', gasInfo.gas_used);
console.log('Gas wanted:', gasInfo.gas_wanted);
```

### Step 6: Sign and Broadcast Transaction

Note: Before signing and broadcasting, you will want to fix errors from the simulation and most likely adjust gas usage dynamically.

#### Sign the Transaction

```javascript
import { createTxBroadcastBody } from 'bitbadgesjs-sdk';

// Sign the transaction string with your Ethereum wallet
const signature = await ethWallet.signMessage(txPayload.txnString);

// Create the broadcast body
const broadcastBody = createTxBroadcastBody(txContext, messages, signature);
```

#### Broadcast the Transaction

```javascript
// Option A: Using BitBadges API
const broadcastUrl = "https://api.bitbadges.io/api/v0/broadcast";
const response = await fetch(broadcastUrl, {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify(broadcastBody)
});

const result = await response.json();
console.log('Transaction hash:', result.txhash);

// Option B: Using Cosmos RPC directly
const rpcBroadcastUrl = "https://node.bitbadges.io/rpc/broadcast_tx_sync";
const rpcResponse = await fetch(rpcBroadcastUrl, {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    jsonrpc: '2.0',
    id: 1,
    method: 'broadcast_tx_sync',
    params: {
      tx: broadcastBody.tx_bytes
    }
  })
});
```

### Important Notes

1. **Testnet vs Mainnet**: Ensure you're using the correct RPC URLs and chain IDs
2. **Gas Estimation**: Always estimate gas properly for complex transactions
3. **Sequence Management**: Increment sequence for each transaction from the same account
4. **Error Handling**: Always implement proper error handling for network calls
5. **Security**: Never expose private keys or mnemonics in production code

### Common Issues

* **Account not found**: Ensure the account has been funded at least once
* **Sequence mismatch**: Query the latest sequence before each transaction
* **Insufficient gas**: Increase gas limit for complex transactions
* **Invalid signature**: Ensure the public key extraction and signing process is correct
