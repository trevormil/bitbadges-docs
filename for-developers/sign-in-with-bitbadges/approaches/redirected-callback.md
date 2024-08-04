# Redirected Callback

The callback approach is useful if you want to obtain the authentication details as soon as the user generates them. For immediate authentication, the callback approach is the only option. For delayed authentication, this is useful if you want to cache the details upon the user signing them, rather than fetching from the API at verification time (e.g. the [No Callback method](../../authenticating-with-bitbadges/approaches/manual.md)).

To enable callbacks, the popupParams must have a redirect URI set. With the callback, the user should never even see the code (or resulting signature). Everything should be handled behind the scenes.

**How do callbacks work?**

A high-level overview is:

1. Users navigate to the custom URL for your authentication request.
2. The user will be waked through the authentication process. Upon completion, an authorization code is transmitted to the redirect URI.
3. The redirect URI can then fetch the authentication details from the transmitted code with knowledge of the configured app's clientSecret.

<figure><img src="../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Generating the URL**

The important part here is to correctly specify a valid **redirectUri** letting us know that you expect to receive the details immeditately.

{% content-ref url="../../authenticating-with-bitbadges/authentication-url-+-parameters/generating-the-url.md" %}
[generating-the-url.md](../../authenticating-with-bitbadges/authentication-url-+-parameters/generating-the-url.md)
{% endcontent-ref %}

**Implementation - Backend**

Set up a handler at the redirect URI to handle the code / state that is passed. The code and state will be passed in via the HTTP query parameters.

For codes created with redirect URIs, BitBadges will store them temporarily (min 30 minutes), but after some time elapses, we make no guarantees on their storage. It is up to you to store and cache them if needed beyond that timeframe.

To ensure the security of the data exchange process, consider the following practices:

* Validate the `state` parameter according to your requirements (if applicable)
* Use HTTPS to protect the data in transit, ensuring that all communications between your server and the client are encrypted.

```typescript
const callbackHandler = async (req: NextApiRequest, res: NextApiResponse) => {
  //Parse the code and state from the query parameters
  const code = req.query.code;
  const state = req.query.state as string;

  //TODO: Fetch authentication details (see Verification page)
  //      const res = await BitBadgesApi.exchangeSIWBBAuthorizationCode({ code, ... });
  //TODO: Any application specific requirements
})
```
