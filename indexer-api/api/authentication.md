# Authentication

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
