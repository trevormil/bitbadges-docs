# Redirect Callback

The callback approach is used for immediate authentication. To enable callbacks, the parameters must have a redirect URI set that matches your app's configured one. With the callback, the user will never even see the code. Everything is handled behind the scenes.

**How do callbacks work?**

A high-level overview is:

1. Users navigate to the custom SIWBB URL for your authentication request.
2. The user will be walked through the authentication process. Upon completion, an authorization code is transmitted to the redirect URI via the query parameters `code` and `state`.
3. The redirect URI can then fetch the details from the API using the transmitted code with knowledge of the configured app's client secret.

<figure><img src="../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Generating the URL**

The important part here is to correctly specify a valid **redirectUri** letting us know that you expect to receive the details immeditately.

{% content-ref url="../../authenticating-with-bitbadges/authentication-url-+-parameters/generating-the-url.md" %}
[generating-the-url.md](../../authenticating-with-bitbadges/authentication-url-+-parameters/generating-the-url.md)
{% endcontent-ref %}

**Implementation - Backend**

```typescript
// GET /api/callback?code=...&state=...
const callbackHandler = async (req: NextApiRequest, res: NextApiResponse) => {
    //Parse the code and state from the query parameters
    const code = req.query.code;
    const state = req.query.state as string;

    //TODO: Fetch authentication details (see Verification page)
    //      const res = await BitBadgesApi.exchangeSIWBBAuthorizationCode({ code, ... });
    //TODO: Any application specific requirements
};
```

At your redirect URI, you will need to set up a handler to handle the code / state that is passed. The code and state will be passed in via the HTTP query parameters. This follows typical OAuth2 flow. We refer you to OAuth tutorials for more details.

To ensure the security of the data exchange process, consider the following practices:

* Validate the `state` parameter according to your requirements (if applicable)
* Use HTTPS to protect the data in transit, ensuring that all communications between your server and the client are encrypted.
