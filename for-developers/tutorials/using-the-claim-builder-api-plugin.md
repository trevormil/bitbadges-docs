# Using the Claim Builder API Plugin

The claim builder supports custom API calls via the API plugin. We will call the specified URI via a POST HTTP request with the following body (Discord and Twitter are only passed if passDiscord and passTwitter is true in the configs). The name and description are to be displayed to the user. You can customize the body of the API call and even allow users to enter parameters too.

To pass the validation check, a successful response must be received (e.g. status code 200). We do nothing else with the response besides check success or not.

Couple notes:

* You should not depend on any per-claim state. This can cause data race conditions since there are two different backends (ours and yours), especially if another plugin fails. These are supposed to be stateless queries. If you need to maintain state or implement more custom claim ideas, you can either implement your own claims or reach out to us to see if it can get integrated natively into the site.
* You are responsible for making sure the endpoint is accessible (e.g. no CORS errors, etc.). Make sure it is a POST request as well.
* **All parameters + body should be considered public.** If you need private variables, consider setting up a proxy server that knows the private variables and redirects to the correct URI.

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

## Publishing a Plugin

Plugins can also be published and preconfigured to be displayed to users in the template section. To publish, create a pull request with your ApiCallPlugin in src/integrations/api.tsx of the bitbadges-frontend repository. The pull request should specify everything below. Also, give a brief summary of everything about the plugin (who created it?, risks?, what data it uses?).&#x20;

For non-indexed compatible queries, you must support calls with **only** the cosmosAddress and any hardcoded inputs. No claimId, Discord details, X details, or custom user inputs are supported.&#x20;

The creator schema is what is to be configured by the claim creator, not the end user. The user schema is to be configured by the end user (the one claiming).

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

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
