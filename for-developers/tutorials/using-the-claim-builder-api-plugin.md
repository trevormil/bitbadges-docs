# Creating HTTP Request Plugin

The claim builder supports custom API calls via the Custom HTTP Request plugin. We will call the specified URI via a HTTP request with the configured variables. The name and description are to be displayed to the user. You can customize the API call and even allow users to enter parameters too.

To pass the validation check, a successful response must be received (e.g. status code 200). **We do nothing else with the response besides check success or not.**

For POST, PUT, and DELETE requests, we pass the values over the body. For GET, we pass them over the GET params.

Couple notes:

* **You should not depend on a successful response from your endpoint meaning a successful claim.**&#x20;
  * **BitBadges does not maintain state for custom plugins.** If your plugin requires state, you have two options:
    * Utilize the BitBadges core plugins to ensure correct behavior. For example, if you want to implement a query of Discord users (one claim per user) who attended an event, the one claim per user must be set and tracked on the BitBadges end.
    * Maintain state yourself but account for potential failures and race conditions. Be mindful that there are two different backends (ours and yours). Just because your plugin returned a success response DOES NOT mean the entire claim was successful. For example, another plugin might have failed.
* **You are responsible for making sure the endpoint is accessible (e.g. no CORS errors, etc.). Make sure it is the desired type as well (i.e. GET vs POST vs DELETE vs PUT).**
  * See more below in the authentication tutorial.
* **If you need private variables, you should take the necessary measures.**&#x20;
  * You can set up a proxy server. For example, BitBadges does not deal with API keys. If you need an API key, you can set up a proxy that knows the API key before making the official request.
* **Make sure that no malicious third-party can learn private information.** For example, don't allow a malicious party to call your API with arbitrary data and learn private information (even the success / failure response may leak private information).
  * If there is potential private information, it is critical that you ensure the request is from the BitBadges backend.&#x20;

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Ensuring BitBadges as Origin

Ensuring BitBadges as the origin may be critical to maintaining privacy for your use case. BitBadges does not handle any API keys or private variables, so to check, you have a couple options:

Firstly, you can check CORS origin in the header is from the correct api.bitbadges.io

Or, we pass a \_\_key variable in the body as well which is a unique key generated for each call.

<pre class="language-typescript"><code class="lang-typescript"><strong>await handleIntegrationQuery({
</strong>    ...body,
    __type: apiCall.uri.split('/').pop(),
    __key: randomKey
});
</code></pre>

You can check the authenticity of the key on your end by calling&#x20;

```typescript
const resData = await BitBadgesApi.getExternalCallKey({ uri: 'YOUR_URI', key: '' })
```

It will return a success response along with a timestamp of the key issuance if it is a valid key issued from us. This is to prevent man in the middle attacks. It is good practice to also check the issuance timestamp to prevent replay attacks.

```javascript
if (!resData || !resData.key || !resData.timestamp) {
    return res.send('Key not valid');
}

if (Date.now() > resData.timestamp + 60000) {
    return res.send('Key expired');
}
```

## Custom Upload via JSON

On the BitBadges site, there is a Upload JSON button in the custom plugin form. If you have a preconfigured plugin but do not want to officially publish it to the BitBadges site, you can create a JSON file such as below and have others upload the preconfigured JSON plugin details to the form.

```typescriptreact
{
    name: '',
    method: '',
    uri: '',
    description: '',
    passAddress: true,
    passEmail: false,
    passDiscord: false,
    passTwitter: false,
    passGithub: false,
    passGoogle: false,
    bodyParams: {}, //hardcode JSON body
    userInputsSchema: [{ key: 'minBalance', label: 'Minimum Balance', type: 'number' }]
  }
```

## Natively Handled vs Self-Hosted

You have a couple options for how the endpoint will be set up.&#x20;

1. You can self-host it yourself. This can also be set up as a proxy to handle private information.&#x20;
   1. Ex: BitBadges API -> Your Endpoint -> Other API
2. Create a pull request in the bitbadges-indexer/src/integrations/integration-query-handlers, and this avoids you having to host your own endpoint,  and everythign is done internally. Couple catches:
   1. We expect you to maintain this if there are any errors.
   2. No private params are supported.
