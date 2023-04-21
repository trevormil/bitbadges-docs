# Codes / Passwords

One distribution method for badges is to be able to claim by entering a code or password (see [Claims](../../for-developers/need-to-know/claims.md)). To provide a better user experience on the BitBadges website, the official BitBadges indexer stores these codes / passwords in a centralized manner to eliminate storage requirements for the user.

**Note these routes will only work if the claim was created through the BitBadges website.**

For password-based claims, to avoid replay attacks, we still create N unique codes but distribute them in a centralized manner (one per address) to whoever enters the correct password. That is why we have the get code for password route (**POST /api/v0/collection/:id/password/:claimId/:password**).



### POST /api/v0/collection/:id/codes

Gets all codes and/or passwords for all claims in the collection.

**Manager-Only Authenticated Route**

Must be signed in and be the manager of the collection.

#### Request

* Body: None

#### Response

* Status Code: 200
* Body:
* ```json
  {
      codes: string[][]
      passwords: string[]
  }
  ```

Will return all codes for the claim as a string\[] and password as a string. Claim ID 1 details will be in index 0. Claim ID 2 will be index 1. And so on. Will be undefined / empty if the claim doesn't use passwords or codes.

### POST /api/v0/collection/:id/password/:claimId/:password

Since passwords aren't natively supported on the blockchain due to replay attacks. behind the scenes, we create N unique codes and distribute them in a centralized manner to whoever provides the correct password. Only one code per address.

**Authenticated Route**

Must be signed in.

#### Request

* Body: None

#### Response

* Status Code: 200
* Body:
* ```json
  {
      code: string
  }
  ```

Will return the code for the user to use when claiming, if password is entered correctly.
