# Authentication

BitBadges uses [Blockin](https://app.gitbook.com/o/7VSYQvtb1QtdWFsEGoUn/s/AwjdYgEsUkK9cCca5DiU/) for authenticating users for private and authenticated functionality. The Blockin execution flow is simple:

**1) Fetch Challenge**

Request a challenge from the **POST /api/v0/auth/getChallenge** route.&#x20;

```typescript
const res = await BitBadgesApi.getSignInChallenge({
    chain: "Ethereum",
    address: "0x.....",
})

/*
    res = {
        nonce: "...",
        params: {
            address: "0x....",
            expirationDate: "...",
            ...
        }, 
        "blockinMessage": "https://bitbadges.io wants you to sign in with your Ethereum account...."
    }
*/
```

**2) Sign Challenge**

The user should then sign the **blockinMessage** string with a personal message signature from their wallet. See [https://github.com/BitBadges/bitbadges-frontend/tree/main/src/bitbadges-api/contexts/chains](https://github.com/BitBadges/bitbadges-frontend/tree/main/src/bitbadges-api/contexts/chains) for complete code snippets.

Ethereum:

```typescript
const signChallenge = async (message: string) => {
    const sign = await signMessage({
      message: message,
    })

    const msgHash = ethers.utils.hashMessage(message)
    const msgHashBytes = ethers.utils.arrayify(msgHash)
    const pubKey = ethers.utils.recoverPublicKey(msgHashBytes, sign)

    const pubKeyHex = pubKey.substring(2)
    const compressedPublicKey = Secp256k1.compressPubkey(
      new Uint8Array(Buffer.from(pubKeyHex, "hex"))
    )
    const base64PubKey = Buffer.from(compressedPublicKey).toString("base64")
    setPublicKey(cosmosAddress, base64PubKey)
    setCookies("pub_key", `${cosmosAddress}-${base64PubKey}`, { path: "/" })

    return {
      message,
      signature: sign,
    }
  }
```

Cosmos (Keplr):

```typescript
const signChallenge = async (message: string) => {
    let sig = await window.keplr?.signArbitrary("bitbadges_1-2", cosmosAddress, message);

    if (!sig) sig = { signature: '', pub_key: { type: '', value: '' } };

    const signatureBuffer = Buffer.from(sig.signature, 'base64');
    const uint8Signature = new Uint8Array(signatureBuffer); // Convert the buffer to an Uint8Array
    const pubKeyValueBuffer = Buffer.from(sig.pub_key.value, 'base64'); // Decode the base64 encoded value
    const pubKeyUint8Array = new Uint8Array(pubKeyValueBuffer); // Convert the buffer to an Uint8Array

    const isRecovered = verifyADR36Amino('cosmos', cosmosAddress, message, pubKeyUint8Array, uint8Signature, 'secp256k1');
    if (!isRecovered) {
      throw new Error('Signature verification failed');
    }

    return {
      message: message,
      signature: sig.pub_key.value + ':' + sig.signature,
    }
  }
```

Solana (Phantom Wallet):

```typescript
const getProvider = () => {
  if ('phantom' in window) {
    const phantomWindow = window as any;
    const provider = phantomWindow.phantom?.solana;
    setSolanaProvider(provider);
    if (provider?.isPhantom) {
      return provider;
    }

    window.open('https://phantom.app/', '_blank');
  }
};

const signChallenge = async (message: string) => {
  const encodedMessage = new TextEncoder().encode(message);
  const provider = solanaProvider;
  const signedMessage = await provider.request({
    method: "signMessage",
    params: {
      message: encodedMessage,
      display: "utf8",
    },
  });
  
  return { message: message, signature: signedMessage.signature };
}
```

Bitcoin (Phantom Wallet):

```typescript
const getProvider = () => {
  if ('phantom' in window) {
    const phantomWindow = window as any;
    const provider = phantomWindow.phantom?.bitcoin;
    if (provider?.isPhantom) {
      return provider;
    }

    window.open('https://phantom.app/', '_blank');
  }
};

function bytesToBase64(bytes: Uint8Array) {
  const binString = String.fromCodePoint(...bytes);
  return btoa(binString);
}

const signChallenge = async (message: string) => {
  const encodedMessage = new TextEncoder().encode(message);
  const provider = getProvider();
  const { signature } = await provider.signMessage(address, encodedMessage);

  return { message: message, signature: bytesToBase64(signature) };
};
```



**3) Send and Verify Signed Challenge**

Send the signed message via **POST /api/v0/auth/verify**. This will grant a Express.js session cookie which is valid for whatever amount of hours you specify in the request. The params should match match what is returned from Step 2.

<pre class="language-typescript"><code class="lang-typescript"><strong>const res = await BitBadgesApi.verifySignIn({
</strong>    chain: "Ethereum",
    message: "https://bitbadges.io wants you to sign in with your....",
    signature: "...."
})

//console.log(res.success) 
</code></pre>

**4) Check health of sign in**

At any time, you can check the health of the signin by **POST /api/v0/auth/status**.

```typescript
const res = await BitBadgesApi.checkIfSignedIn(requestBody)
//console.log(res.signedIn)
```
