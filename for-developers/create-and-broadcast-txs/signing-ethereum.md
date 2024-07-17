# Signing - Ethereum

Pre-Requisite: You have generated the transaction and have it in the **txn** variable (see prior pages).

You have two options (EIP-712 and JSON), but EIP-712 is limited in terms of message size.

## JSON

You can simply sign the JSON, similar to the signatures for other chains.

**Wallet Connect / WAGMI**

```typescript
import {
    TransactionPayload,
    TxContext,
    createTxBroadcastBody,
} from 'bitbadgesjs-sdk';
import { signMessage } from '@wagmi/core';

const signTxn = async (
    context: TxContext,
    payload: TransactionPayload,
    simulate: boolean
) => {
    if (!account) throw new Error('Account not found.');

    let sig = '';
    if (!simulate) {
        const message = payload.jsonToSign;
        sig = await signMessage({
            message: message,
        });
    }

    const txBody = createTxBroadcastBody(context, payload, sig);
    return txBody;
};
```

**Standard**&#x20;

[**https://docs.metamask.io/wallet/reference/personal_sign/**](https://docs.metamask.io/wallet/reference/personal_sign/)

```typescript
const sig = await window.ethereum.request({
    method: 'personal_sign', //May be eth_sign on some wallets
    params: [address, payload.jsonToSign],
});
```

## EIP-712

Important: EIP-712 is limited in message size. Due to its poor performance, it greatly slows down the blockchain, so we only allow it to be used for small messages.

```
payload.jsonToSign.length > 1000
```

**Signing with Wallet Connect / WAGMI**

```typescript
import {
    TransactionPayload,
    TxContext,
    createTxBroadcastBodyEthereum,
} from 'bitbadgesjs-sdk';
import { signTypedData } from '@wagmi/core';

const signTxn = async (
    context: TxContext,
    payload: TransactionPayload,
    simulate: boolean
) => {
    if (!account) throw new Error('Account not found.');

    let sig = '';
    if (!simulate) {
        sig = await signTypedData({
            message: payload.eipToSign.message as any,
            types: payload.eipToSign.types as any,
            domain: payload.eipToSign.domain,
            primaryType: payload.eipToSign.primaryType,
        });
    }

    const txBody = createTxBroadcastBodyEthereum(context, payload, sig);
    return txBody;
};
```

#### Signing with Metamask

To sign with Metamask, replace the signature logic in the snipper above with the code below.

```typescript
//Add necessary imports
import { _TypedDataEncoder } from 'ethers/lib/utils.js';

// Init Metamask
await window.ethereum.enable();

const ethAddress = '...';

//Option 1: window.ethereum
const eip = _TypedDataEncoder.getPayload(
    payload.eipToSign.domain,
    types_,
    payload.eipToSign.message
);
const sig = await window.ethereum.request({
    method: 'eth_signTypedData_v4',
    params: [ethAddress, JSON.stringify(eip)],
});

//Option 2: Use the signer._signTypedData from ethers directly (https://docs.ethers.org/v5/api/signer/#Signer-signTypedData)
//It will give an error with an unused EIP712Domain type, so you have to remove that before calling it as seen below.
//It adds this type automatically.

//From https://github.com/wagmi-dev/wagmi/blob/main/packages/core/src/actions/accounts/signTypedData.ts#L41
const types_ = Object.entries(payload.eipToSign.types)
    .filter(([key]) => key !== 'EIP712Domain')
    .reduce((types, [key, attributes]: [string, TypedDataField[]]) => {
        types[key] = attributes.filter((attr) => attr.type !== 'EIP712Domain');
        return types;
    }, {} as Record<string, TypedDataField[]>);

// Method name may be changed in the future, see https://docs.ethers.io/v5/api/signer/#Signer-signTypedData
return await signer._signTypedData(
    payload.eipToSign.domain,
    types_,
    payload.eipToSign.message
);
```

## Output

This will leave you with a variable which is to be submitted to a running blockchain node. See [Broadcast to a Node.](broadcast-to-a-node.md)

### Full Snippet

{% @github-files/github-code-block url="https://github.com/BitBadges/bitbadges-quickstart/blob/main/src/chains/chain_contexts/insite/EthereumContext.tsx" %}
