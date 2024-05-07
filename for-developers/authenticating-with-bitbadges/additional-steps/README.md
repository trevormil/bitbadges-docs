# Additional Steps

We leave any additional custom logic up to you. This includes preventing replay attacks, implementing sessions, authenticating with your private database values, max uses per account / badge? specific whitelisted addresses only?, etc.

```typescriptreact
Blockin handles checking the user's signature and verifying ownership of specified badges (if any). Any other custom
                      requirements need to be handled by you separately (e.g. stamping users hands, checking IDs, etc.). It is also critical that you
                      prevent replay attacks, man-in-the-middle attacks, and flash ownership attacks (if verifying with assets). We strongly recommend
                      codes, assets, and addresses being one-time use only to prevent these. Once everything is verified, you can authenticate the
                      user.
```

Copy

```typescript
// TODO: This can be replaced with other session methods, as well.
const sessionData = {
  chain: getChainForAddress(params.address),
  address: params.address,
  nonce: params.nonce,
};

// Set the session cookie
res.setHeader('Set-Cookie', cookie.serialize('session', JSON.stringify(sessionData), {
  httpOnly: true, // Make the cookie accessible only via HTTP (not JavaScript)
  expires: new Date(params.expirationDate),
  path: '/', // Set the cookie path to '/'
  sameSite: 'strict', // Specify the SameSite attribute for security
  secure: process.env.NODE_ENV === 'production', // Ensure the cookie is secure in production
}));
```
