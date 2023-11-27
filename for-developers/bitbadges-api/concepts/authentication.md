# Authentication

BitBadges uses [Blockin](http://127.0.0.1:5000/o/7VSYQvtb1QtdWFsEGoUn/s/AwjdYgEsUkK9cCca5DiU/) for authenticating users. The Blockin execution flow is simple:

**1) Fetch Challenge**

Request a challenge from the **POST /api/v0/auth/getChallenge** route.&#x20;

```typescript
await BitBadgesApi.getSignInChallenge(requestBody)
```

```typescript
export interface GetSignInChallengeRouteRequestBody {
    chain: SupportedChain; //"Ethereum", "Solana", "Cosmso"
    address: string;
    hours?: NumberType; //hours to be signed in for
}
```

<pre class="language-typescript"><code class="lang-typescript"><strong>export interface GetSignInChallengeRouteSuccessResponse&#x3C;T extends NumberType> {
</strong>    nonce: string;
    params: ChallengeParams&#x3C;T>;
    blockinMessage: string;
}
</code></pre>

**2) Sign Challenge**

The user should then sign the **blockinMessage** string with a personal message signature from their wallet.

Ethereum:

```typescript
const signChallenge = async (message: string) => {
    let accounts = await window.ethereum.request({ method: 'eth_accounts' })

    const from = accounts[0];
    const msg = `0x${Buffer.from(message, 'utf8').toString('hex')}`;
    const sign = await window.ethereum.request({
        method: 'personal_sign',
        params: [msg, from],
    });

    return { originalBytes: new Uint8Array(Buffer.from(msg, 'utf8')), signatureBytes: new Uint8Array(Buffer.from(sign, 'utf8')), message: 'Success' }
}
```

Cosmos (Keplr):

```typescript
const signChallenge = async (message: string) => {
    let sig = await window.keplr?.signArbitrary("bitbadges_1-1", cosmosAddress, message);
    
    if (!sig) sig = { signature: '', pub_key: { type: '', value: '' } };
    
    const signatureBuffer = Buffer.from(sig.signature, 'base64');
    const uint8Signature = new Uint8Array(signatureBuffer); // Convert the buffer to an Uint8Array
    const pubKeyValueBuffer = Buffer.from(sig.pub_key.value, 'base64'); // Decode the base64 encoded value
    const pubKeyUint8Array = new Uint8Array(pubKeyValueBuffer); // Convert the buffer to an Uint8Array
    
    const isRecovered = verifyADR36Amino('cosmos', cosmosAddress, message, pubKeyUint8Array, uint8Signature, 'ethsecp256k1');
    if (!isRecovered) {
        throw new Error('Signature verification failed');
    }
    const concat = Buffer.concat([pubKeyUint8Array, uint8Signature]);
    
    return { originalBytes: new Uint8Array(Buffer.from(`0x${Buffer.from(message, 'utf8').toString('hex')}`, 'utf8')), signatureBytes: new Uint8Array(concat), message: 'Success' }
}
```

Solana (Phantom Wallet):

```typescript
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
  const originalBytes = new Uint8Array(Buffer.from(message, 'utf8'));
  const signatureBytes = new Uint8Array(Buffer.from(signedMessage.signature, 'hex'));

  return { originalBytes: originalBytes, signatureBytes: signatureBytes, message: 'Success!' };
}
```

**3) Send Signed Challenge**

Send the signed message via **POST /api/v0/auth/verify**.&#x20;

This will grant a Express.js session cookie which is valid for 24 hours (or whatever amount of hours you specify in the request).&#x20;

The originalBytes and signatureBytes to provide in the request body should match what is returned from Step 2.

```typescript
await BitBadgesApi.verifySignIn(requestBody)
```

```typescript
export interface VerifySignInRouteRequestBody {
    chain: SupportedChain;
    originalBytes: any;
    signatureBytes: any;
}

export interface VerifySignInRouteSuccessResponse<T extends NumberType> {
    success: boolean;
    successMessage: string;
}
```

3. At any time, you can check the health of the signin by **POST /api/v0/auth/status**.

```typescript
await BitBadgesApi.checkIfSignedIn(requestBody)
```
