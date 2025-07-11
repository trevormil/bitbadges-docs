# Auth0

### **Social Connection Integration**

When adding a Social Connection, you will see BitBadges as a preconfigured option. This has everything setup for you. You will simply need to specify your client ID and client secret. You will also have to configure your redirect URI to match your Auth0 application's domain. This typically ends with /callback (for example, [https://dev-pgv803tz4ztg35oi.us.auth0.com/login/callback](https://dev-pgv803tz4ztg35oi.us.auth0.com/login/callback)).

### [https://marketplace.auth0.com/integrations/bitbadges](https://marketplace.auth0.com/integrations/bitbadges)

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (126).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Custom

You can also customize the connection further by creating a custom connection. This allows you to add parameters beyond the standard flow. You callback will be the same as above.

<figure><img src="../../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Authorization URL:** https://bitbadges.io/siwbb/authorize

**Token URL:** https://api.bitbadges.io/api/v0/siwbb/token

**Scopes:** None (All you typically need is the user crypto address) - Although, you can add as needed

**Fields:** Get your API key, client ID, and client secret at https://bitbadgesio/developer.

**Fetch Profile Script**:

```javascript
function fetchUserProfile(accessToken, ctx, cb) {
    request.post(
        {
            url: 'https://api.bitbadges.io/api/v0/auth/status',
            headers: {
                'Content-Type': 'application/json',
                // TODO: Replace
                'x-api-key': 'ENTER_API_KEY_HERE',
                Authorization: 'Bearer ' + accessToken,
            },
        },
        (err, resp, body) => {
            if (err) {
                return cb(err);
            }
            if (resp.statusCode !== 200) {
                return cb(new Error(body));
            }
            let bodyParsed;
            try {
                bodyParsed = JSON.parse(body);
            } catch (jsonError) {
                return cb(
                    new Error('Failed JSON parsing for user profile response.')
                );
            }

            const account = bodyParsed;

            const profile = {
                address: account.address,
                chain: account.chain,
                id: account.bitbadgesAddress,
                name: account.address,
            };
            return cb(null, profile);
        }
    );
}
```

**Custom Headers:**&#x20;

Make sure you enter your API key in the custom headers an d replace it in the fetch script.

```
{
    "x-api-key": "YOUR_API_KEY"
}
```
