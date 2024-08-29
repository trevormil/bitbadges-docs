# Auth0

### **Social Connection Integration**

When adding a Social Connection, you will see BitBadges as a preconfigured option. This has everything setup for you. You will simply need to specify your client ID and client secret. You will also have to configure your redirect URI to match your Auth0 application's domain. This typically ends with /callback (for example, [https://dev-pgv803tz4ztg35oi.us.auth0.com/login/callback](https://dev-pgv803tz4ztg35oi.us.auth0.com/login/callback)).

### [https://marketplace.auth0.com/integrations/bitbadges](https://marketplace.auth0.com/integrations/bitbadges)



<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (126).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Custom

You can also customize the connection further by creating a custom connection. This allows you to add parameters beyond the standard flow.

**Authorization URL:** https://bitbadges.io/siwbb/authorize

**Token URL:** https://api.bitbadges.io/api/v0/siwbb/token

**Scopes:** None (All you typically need is the user crypto address)

**Fields:** Get your client ID and client secret at https://bitbadgesio/developer.

**Fetch Profile Script**:&#x20;

```javascript
function(accessToken, ctx, cb) {
    const address = accessToken;
  	
   	request.post(
    {
      url: 'https://api.bitbadges.io/api/v0/users',
     	headers: {
				'Content-Type': 'application/json',
			},
      body: JSON.stringify({
       	accountsToFetch: [{ address: accessToken }] 
      })
    }, (err, resp, body) => {
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
        return cb(new Error(body));
      }
      
      const account = bodyParsed.accounts[0];

      const profile = {
        address: account.address,
        chain: account.chain,
        id: account.cosmosAddress,
        name: account.address,
      };
      cb(null, profile);
    });
}
```

**Custom Headers:** None

<figure><img src="../../../.gitbook/assets/image (3) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (118).png" alt=""><figcaption></figcaption></figure>
