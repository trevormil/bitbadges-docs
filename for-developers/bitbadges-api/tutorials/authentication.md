# API Authorization

**Typically, you will not need authentication because these are features that are pretty specific to the BitBadges website. However, in some cases, you may.**

This follows a typical OAuth authorization flow. Note that this is different than Sign In with BitBadges. Authorizing allows you to do stuff on the BitBadges API on behalf of the user. SIWBB is for authenticating users in your own application. If you are looking for that, see the SIWBB tutorial.

**Step 1: Create App**

Create your app at [https://bitbadges.io/developer](https://bitbadges.io/developer). You will get a client ID and client secret which will be used later. Your redirect URI will be the callback where we send the authorization codes after successful authorization.

IMPORTANT: The client secret should always be kept secret.

**Step 2: Create Your Authorize URL**

The authorization URL for your application will be

```
https://bitbadges.io/oauth?client_id=YOUR_CLIENT_ID&redirect_uri=YOUR_REDIRECT_URI
```

You can also specify a **state** parameter for passing arbitrary data (like a nonce). You can also specify **scope** to be predefined scopes that you would like access to. By default, we allow the user to select, but if you specify what you want, we will hardcode the scopes to those and disallow selecting others.

```
....&state=something&scopes=[{ "scopeName": "Complete Claims" }, ... ] //in URL encoded format
```

Visit https://bitbadges.io/oauth to see the full list of approved scopes. You only should specify the labels (scope names). Let us know if you would like us to add other scopes.

**Step 3: Setup Your Callback Handler**

At the redirect URI, you will setup a callback handler on your backend. This will be passed the **code** (authorization code) and **state** (if originally passed in).

```typescript
const callback = async (req: NextApiRequest, res: NextApiResponse) => {
  //Parse the code and state from the query parameters
  const code = req.query.code as string;
  const state = req.query.state as string;
  ...
```

You then need to exchange the code for an access token.

<pre class="language-typescript"><code class="lang-typescript"><strong>// POST https://api.bitbadges.io/api/v0/siwbb/token
</strong><strong>const res = await BitBadgesApi.getOauthAccessToken({
</strong>    client_id,
    client_secret,
    grant_type: 'authorization_code',
    redirect_uri,
    code, //the received authorization code
})
</code></pre>

Your response will look something like this. The access token will expire in 1 day. The refresh token expires in 24 hours.

<figure><img src="../../../.gitbook/assets/image (95).png" alt=""><figcaption></figcaption></figure>

Finally, you can start sending requests to authenticated endpoints with your access token specified in the Authorization header as "Bearer YOUR\_ACCESS\_TOKEN".

<figure><img src="../../../.gitbook/assets/image (96).png" alt=""><figcaption></figcaption></figure>

If you are using the SDK, you can instead do this which handles the header setting:

```typescript
BitBadgesApi.setAccessToken(token);
BitBadgesApi.unsetAccessToken();
```

**Step 4: Refresh**

Using the refresh token obtained from step 3, you can exchange for a new access token and refresh token (with expiration reset) on a rolling basis. This step can be repeated indefinitely. You will receive the same response type as Step 3.

<pre class="language-typescript"><code class="lang-typescript"><strong>const res = await BitBadgesApi.getOauthAccessToken({
</strong>    client_id,
    client_secret,
    grant_type: 'refresh_token',
    redirect_uri,
    refresh_token
})
</code></pre>

**Step 5: Revoke**

Once you are done with the access token, you should revoke your access to it via the following. This can also be done by the user via the Connections tab in-site.

```typescript
// POST https://api.bitbadges.io/api/v0/siwbb/token/revoke
await BitBadgesApi.revokeOauthAuthorization({ token });
```

{% @github-files/github-code-block url="https://github.com/BitBadges/bitbadges-quickstart/blob/main/src/pages/api/apiauth.ts" %}
