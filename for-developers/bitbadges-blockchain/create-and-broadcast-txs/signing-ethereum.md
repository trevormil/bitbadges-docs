# Signing - Ethereum

BitBadges Chain supports Ethereum-compatible transactions through EVM precompiles. The SDK automatically converts Cosmos SDK messages to EVM precompile function calls when an EVM address is provided in the transaction context.

## Overview

When using Ethereum wallets (MetaMask, Privy, etc.), transactions are signed as EVM transactions and sent to precompile contracts. The SDK handles the conversion from Cosmos SDK messages to EVM precompile calls automatically.

## Transaction Payload with EVM Support

The `createTransactionPayload` function now supports optional EVM precompile conversion. When you provide an `evmAddress` in the `TxContext`, the SDK will:

1. Create the standard Cosmos transaction payload (for compatibility)
2. Convert messages to EVM precompile calls (if supported)
3. Return both in the `TransactionPayload` with an optional `evmTx` field

### Basic Usage

```typescript
import { createTransactionPayload, TxContext, MsgTransferTokens } from 'bitbadgesjs-sdk';

// Create your messages
const msg = new MsgTransferTokens({
  creator: 'bb1...', // BitBadges address
  collectionId: '1',
  transfers: [/* ... */]
});

// Create transaction context with evmAddress
const txContext: TxContext = {
  testnet: false,
  sender: {
    address: 'bb1...', // BitBadges address (bb-prefixed)
    sequence: 0,
    accountNumber: 1,
    publicKey: '', // Not needed for EVM transactions
  },
  fee: {
    amount: '0',
    denom: 'ubadge',
    gas: '200000',
  },
  memo: '',
  evmAddress: '0x1234...', // ✅ Add EVM address to enable precompile conversion
};

// Create payload - SDK will automatically convert to EVM format
const payload = createTransactionPayload(txContext, msg);

// Check if EVM transaction is available
if (payload.evmTx) {
  // Use EVM transaction details
  const { to, data, value, functionName } = payload.evmTx;
  // to: precompile address (0x1001, 0x1002, or 0x1003)
  // data: encoded function call data
  // value: always "0" for precompiles
  // functionName: function name for debugging
}
```

## Signing EVM Transactions

### Using Privy (Recommended)

```typescript
import { useSendTransaction } from '@privy-io/react-auth';

const { sendTransaction } = useSendTransaction();

const signTxn = async (
  context: TxContext,
  payload: TransactionPayload,
  messages: unknown[],
  simulate: boolean
) => {
  if (messages.length === 0) {
    throw new Error('No messages provided');
  }

  // Check if payload has evmTx field (SDK converted messages to EVM format)
  if (!payload.evmTx) {
    throw new Error('Messages are not supported for EVM transactions');
  }

  // Use evmTx from payload (SDK has already converted the messages)
  const { data, to: precompileAddress } = payload.evmTx;

  if (simulate) {
    // For simulation, return transaction body with EVM details
    const txBody = createTxBroadcastBody(context, messages, '');
    return {
      ...txBody,
      mode: 'evm',
      evmTx: {
        to: precompileAddress,
        data: data,
        value: '0',
        gasLimit: context.fee?.gas || '200000'
      }
    };
  }

  // Send transaction using Privy
  const result = await sendTransaction(
    {
      to: precompileAddress,
      data: data,
      value: 0n
    },
    {
      address: evmAddress // Specify which wallet to use
    }
  );

  // Return transaction hash
  return {
    mode: 'evm',
    txHash: result.hash,
    evmTx: {
      to: precompileAddress,
      data: data,
      value: '0'
    },
    messages: messages,
    context: context
  };
};
```

### Using ethers.js

```typescript
import { ethers } from 'ethers';

const signTxn = async (
  context: TxContext,
  payload: TransactionPayload,
  messages: unknown[],
  simulate: boolean
) => {
  if (!payload.evmTx) {
    throw new Error('Messages are not supported for EVM transactions');
  }

  const { data, to: precompileAddress } = payload.evmTx;

  // Get provider and signer
  const provider = new ethers.BrowserProvider(window.ethereum);
  const signer = await provider.getSigner();

  if (simulate) {
    // Estimate gas for simulation
    const gasEstimate = await provider.estimateGas({
      to: precompileAddress,
      data: data,
      value: 0n
    });
    
    return {
      mode: 'evm',
      evmTx: {
        to: precompileAddress,
        data: data,
        value: '0',
        gasLimit: gasEstimate.toString()
      }
    };
  }

  // Send transaction
  const tx = await signer.sendTransaction({
    to: precompileAddress,
    data: data,
    value: 0n
  });

  // Wait for transaction
  const receipt = await tx.wait();

  return {
    mode: 'evm',
    txHash: receipt.hash,
    evmTx: {
      to: precompileAddress,
      data: data,
      value: '0'
    }
  };
};
```

## Precompile Addresses

The SDK automatically selects the correct precompile address based on message type:

| Precompile | Address | Message Types |
|------------|---------|---------------|
| Tokenization | `0x0000000000000000000000000000000000001001` | All tokenization module messages |
| Gamm | `0x0000000000000000000000000000000000001002` | Pool operations (JoinPool, ExitPool, SwapExactAmountIn, etc.) |
| SendManager | `0x0000000000000000000000000000000000001003` | MsgSend (native coin transfers) |

## Supported Message Types

### Tokenization Messages

All tokenization module messages are supported:

- `MsgTransferTokens`
- `MsgCreateCollection`
- `MsgUniversalUpdateCollection`
- `MsgSetIncomingApproval`
- `MsgSetOutgoingApproval`
- `MsgSetCollectionApprovals`
- `MsgSetTokenMetadata`
- `MsgSetCollectionMetadata`
- `MsgCreateDynamicStore`
- `MsgUpdateDynamicStore`
- `MsgDeleteDynamicStore`
- `MsgSetDynamicStoreValue`
- And all other tokenization messages

### Gamm Messages

- `MsgCreateBalancerPool`
- `MsgJoinPool`
- `MsgExitPool`
- `MsgSwapExactAmountIn`
- `MsgSwapExactAmountInWithIBCTransfer`

### Bank Messages

- `MsgSend` (via SendManager precompile)

## Multiple Messages

For multiple tokenization messages, the SDK uses the `executeMultiple` precompile function:

```typescript
const msg1 = new MsgTransferTokens({ /* ... */ });
const msg2 = new MsgSetTokenMetadata({ /* ... */ });

const payload = createTransactionPayload(
  {
    ...txContext,
    evmAddress: '0x1234...'
  },
  [msg1, msg2] // Multiple messages
);

if (payload.evmTx) {
  // SDK automatically uses executeMultiple for multiple tokenization messages
  // payload.evmTx.functionName will be 'executeMultiple'
  // payload.evmTx.to will be TOKENIZATION_PRECOMPILE_ADDRESS (0x1001)
}
```

**Note:** Multiple messages must all be tokenization messages. Mixed message types (e.g., tokenization + gamm) are not supported in a single transaction.

## Transaction Context for EVM

When creating a transaction context for EVM transactions:

```typescript
const txContext: TxContext = {
  testnet: false,
  sender: {
    address: 'bb1...', // BitBadges address (required)
    sequence: 0, // Required
    accountNumber: 1, // Required
    publicKey: '', // Not needed for EVM (can be empty string)
  },
  fee: {
    amount: '0', // Fee amount in ubadge
    denom: 'ubadge',
    gas: '200000', // Gas limit
  },
  memo: '',
  evmAddress: '0x1234...', // ✅ EVM address for precompile conversion
};
```

**Important Notes:**

- `sender.address` must be a BitBadges address (bb-prefixed)
- `evmAddress` should be the Ethereum address (0x-prefixed) of the same account
- `publicKey` is not required for EVM transactions (can be empty string)
- The SDK handles address conversion between formats automatically

## Error Handling

If a message type is not supported for EVM conversion, `payload.evmTx` will be `undefined`. You should check for this:

```typescript
const payload = createTransactionPayload(txContext, messages);

if (!payload.evmTx) {
  // Messages are not supported for EVM transactions
  // Fall back to Cosmos signing or show error
  throw new Error('Messages are not supported for EVM transactions');
}
```

## Complete Example

```typescript
import {
  createTransactionPayload,
  createTxBroadcastBody,
  TxContext,
  MsgTransferTokens,
  TransactionPayload
} from 'bitbadgesjs-sdk';
import { useSendTransaction } from '@privy-io/react-auth';

function useEthereumSigning() {
  const { sendTransaction } = useSendTransaction();

  const signAndBroadcast = async (
    evmAddress: string,
    bitbadgesAddress: string,
    messages: any[]
  ) => {
    // 1. Get account info
    const account = await BitBadgesApi.getAccount({ address: bitbadgesAddress });
    
    // 2. Create transaction context with evmAddress
    const txContext: TxContext = {
      testnet: false,
      sender: {
        address: bitbadgesAddress,
        sequence: account.sequence,
        accountNumber: account.accountNumber,
        publicKey: '', // Not needed for EVM
      },
      fee: {
        amount: '0',
        denom: 'ubadge',
        gas: '200000',
      },
      memo: '',
      evmAddress: evmAddress, // ✅ Enable EVM conversion
    };

    // 3. Create payload (SDK converts to EVM format automatically)
    const payload = createTransactionPayload(txContext, messages);

    // 4. Check if EVM conversion succeeded
    if (!payload.evmTx) {
      throw new Error('Messages not supported for EVM transactions');
    }

    // 5. Send transaction
    const result = await sendTransaction(
      {
        to: payload.evmTx.to,
        data: payload.evmTx.data,
        value: 0n
      },
      { address: evmAddress }
    );

    return {
      txHash: result.hash,
      precompileAddress: payload.evmTx.to,
      functionName: payload.evmTx.functionName
    };
  };

  return { signAndBroadcast };
}
```

## Differences from Cosmos Signing

| Aspect | Cosmos | Ethereum |
|--------|--------|----------|
| Wallet | Keplr, Leap, etc. | MetaMask, Privy, etc. |
| Address Format | `bb1...` (Bech32) | `0x...` (Hex) |
| Public Key | Required | Not required |
| Signature Format | Cosmos Direct/Amino | EVM (EIP-155) |
| Message Format | Protobuf | Precompile JSON string |
| Transaction Type | Cosmos SDK messages | EVM contract calls |

## See Also

- [Signing - Cosmos](./signing-cosmos.md) - For Cosmos wallet signing
- [Transaction Context](./transaction-context.md) - For transaction context details
- [EVM Precompiles Developer Guide](../../../evm/evm-precompiles/developer-guide.md) - For detailed precompile information

