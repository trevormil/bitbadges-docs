# Using the Claim Builder API Plugin

The claim builder supports custom API calls via the API plugin. We will call the specified URI via a POST HTTP request with the configured body. The name and description are to be displayed to the user. You can customize the body of the API call and even allow users to enter parameters too.

To pass the validation check, a successful response must be received (e.g. status code 200). We do nothing else with the response besides check success or not.

Couple notes:

* **You should not depend on a successful response from your endpoint meaning a successful claim.**&#x20;
  * Just because your endpoint was a success does not mean the entire claim was successful. This can cause data race conditions since there are two different backends (ours and yours), especially if another plugin fails.&#x20;
  * It is important that you set the necessary criteria on the BitBadges end to ensure correct behavior. For example, if you want to implement a query of X users (one claim per user) who attended a space, the one claim per user must be set and tracked on the BitBadges end.
* **You are responsible for making sure the endpoint is accessible (e.g. no CORS errors, etc.). Make sure it is a POST request as well.**
  * See more below in the authentication tutorial.
* **If you need private variables, you should take the necessary measures.**&#x20;
  * You can set up a proxy server. For example, BitBadges does not deal with API keys. If you need an API key, you can set up a proxy that knows the API key before making the official request.
* **Make sure that no malicious third-party can learn private information.** For example, don't allow a malicious party to call your API with arbitrary data and learn private information (even the success / failure response may leak private information).
  * If there is potential private information, it is critical that you ensure the request is from the BitBadges backend.&#x20;

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Ensuring BitBadges as Origin

Ensuring BitBadges as the origin is critical to maintaining privacy. BitBadges does not handle any API keys or priivate variables, so to check, you have a couple options:

Firstly, you can check CORS origin in the header is from the correct api.bitbadges.io

Or, we pass a \_\_key variable in the body as well which is a unique key generated for each call.

<pre class="language-typescript"><code class="lang-typescript"><strong>await handleIntegrationQuery({
</strong>    ...body,
    __type: apiCall.uri.split('/').pop(),
    __key: randomKey
});
</code></pre>

You can check the authenticity of the key on your end by calling&#x20;

```
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

## Natively Handled vs Self-Hosted

You have a couple options for how the endpoint will be set up.&#x20;

1. You can self-host it yourself. This can also be set up as a proxy to handle private information.&#x20;
   1. Ex: BitBadges API -> Your Endpoint -> Other API
2. Create a pull request in the bitbadges-indexer/src/integrations/integration-query-handlers, and this avoids you having to host your own endpoint. Couple catches:
   1. We expect you to maintain this if there are any errors.
   2. No private params are supported.

## Custom Upload via JSON

On the BitBadges site, there is a Upload JSON button in the custom plugin form. If you have a preconfigured plugin but do not want to officially publish it to the BitBadges site, you can create a JSON file such as below and have others upload the preconfigured JSON plugin details to the form.

```typescriptreact
{
    name: '',
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

## Publishing a Plugin

Plugins can also be published and preconfigured to be displayed to users in the template section. To publish, create a pull request with your ApiCallPlugin in src/integrations/api.tsx of the bitbadges-frontend repository. The pull request should specify everything below. Also, give a brief summary of everything about the plugin (who created it?, risks?, what data it uses?).&#x20;

For non-indexed compatible queries, you must support calls with **only** the cosmosAddress and any hardcoded inputs. No claimId, Discord details, X details, or custom user inputs are supported.&#x20;

The creator schema is what is to be configured by the claim creator, not the end user. The user schema is to be configured by the end user (the one claiming).

<figure><img src="../../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

```typescript
export const ApiCallPlugins: ApiCallPlugin[] = [
  {
    id: 'test',
    metadata: {
      name: 'Test API',
      description: 'Test API',
      image: 'https://avatars.githubusercontent.com/u/86890740',
      createdBy: 'BitBadges',
      nonIndexedCompatible: true
    },
    apiCallInfo: {
      uri: 'https://api.bitbadges.io/test',
      passDiscord: true,
      passTwitter: true,
      userInputsSchema: [{ key: 'username', label: 'Username', type: 'string' }],
      creatorInputsSchema: [
        { key: 'username', label: 'Username', type: 'string' },
        { key: 'ss', label: 'Test', type: 'number' },
        { key: 'date', label: 'Date', type: 'date' },
        { key: 'd', label: 'Test', type: 'number' }
      ],
      hardcodedInputs: [{ key: 'fdsgsdfg', label: 'Test', value: 'test' }]
    }
  }
];

export interface ApiCallPlugin {
  id: string;
  metadata: {
    name: string;
    description: string;
    image: string;
    createdBy: string;
    nonIndexedCompatible?: boolean;
  };
  apiCallInfo: {
    uri: string;
    passDiscord: boolean;
    passTwitter: boolean;
    userInputsSchema: Array<JsonBodyInputSchema>;
    creatorInputsSchema: Array<JsonBodyInputSchema>;
    hardcodedInputs: Array<JsonBodyInputWithValue>;
  };
}

```
