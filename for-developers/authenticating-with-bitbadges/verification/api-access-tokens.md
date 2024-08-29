# API Access Tokens

If you do not have access tokens, you can skip this section. Most apps only need to identify the user address.

With access tokens, you can start sending requests to authenticated endpoints with your access token specified in the Authorization header as "Bearer YOUR\_ACCESS\_TOKEN".

<figure><img src="../../../.gitbook/assets/image (96).png" alt=""><figcaption></figcaption></figure>

If you are using the SDK, you can instead do this which handles the header setting:

```typescript
BitBadgesApi.setAccessToken(token);
BitBadgesApi.unsetAccessToken();
```

Access tokens by default expire in 1 day, and refresh tokens expire in 60 days. Note that they may also become invalid as the user revokes access to them as well.&#x20;

**Refreshing**

```typescript
const res = await BitBadgesApi.exchangeSIWBBAuthorizationCode({ 
    refresh_token
    grant_type: 'refresh_token',
    client_secret: '...',
    client_id: '...',
    redirect_uri: '...' //only needed if redirected
});

const { access_token, access_token_expires_at, refresh_token, refresh_token_expires_at } = res;
```

Using the refresh token obtained previously, you can exchange for a new access token and refresh token (with expiration reset) on a rolling basis. This step can be repeated indefinitely.&#x20;

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Revoking Access**

Once you are done with the access token, you should revoke your access to it via the following. This can also be done by the user via the Connections -> Authorizations tab in-site. This can be done by either the user or the app.

```typescript
// POST https://api.bitbadges.io/api/v0/siwbb/token/revoke
await BitBadgesApi.revokeOauthAuthorization({ token });
```
