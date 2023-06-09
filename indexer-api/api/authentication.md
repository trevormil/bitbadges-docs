# Authentication

BitBadges uses [Blockin](http://localhost:5000/o/7VSYQvtb1QtdWFsEGoUn/s/AwjdYgEsUkK9cCca5DiU/) for authenticating users.

The Blockin execution flow is simple:

1. Request a challenge from the **POST /api/v0/auth/getChallenge** route. The return value of blockinMessage is the message to be signed.
2. User signs the challenge message locally

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

3. Send the signed message via **POST /api/v0/auth/verify**. This will grant a HTTP only Express.js session cookie which is valid for 24 hours. The originalBytes and signatureBytes to provide in the request body are what is returned from the signChallenge() examples.



### POST /api/v0/auth/getChallenge

Returns a challenge string from Blockin to be signed.

#### Request

* Body:&#x20;
* ```
  {
      chain: string
      address: string
  }
  ```

address should be the requesting user's address. chain should be either 'Cosmos' or 'Ethereum'

#### Response

* Status Code: 200
* Body:
* ```json
  { 
          nonce: string,
          params: Object, //challenge params
          blockinMessage: string //message to sign
  }
  ```

### POST /api/v0/auth/verify

Verify the challenge and grants HTTP only session token.

#### Request

* Body:&#x20;
* ```
  {
      chain: string
      originalBytes: Uint8Array
      signatureBytes: Uint8Array
  }
  ```

#### Response

* Status Code: 200
* Body:
* ```json
  { 
          verified: true, 
          message: verificationResponse.message
  }
  ```

### POST /api/v0/auth/logout

Logs out the user and removes the session cookie.

#### Request

* Body: None

#### Response

* Status Code: 200
* Body:
* ```json
  { 
      message: 'Successfully removed session cookie!' 
  }
  ```
