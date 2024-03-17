# Using the Claim Builder API Plugin

The claim builder supports custom API calls via the API plugin. We will call the specified URI via a POST HTTP request with the following body (Discord and Twitter are only passed if passDiscord and passTwitter is true in the configs). The name and description are to be displayed to the user. You can customize the body of the API call and even allow users to enter parameters too.

To pass the validation check, a successful response must be received (e.g. status code 200). We do nothing else with the response besides check success or not.

Couple notes:

* You should not depend on any per-claim state. This can cause data race conditions, especially if another plugin fails. These are supposed to be stateless queries.
* You are responsible for making sure the endpoint is accessible (e.g. no CORS errors, etc.).&#x20;
* All parameters + body should be considered public. If you need private variables, consider setting up a proxy server that knows the private variables and redirects to the correct URI.
* Make sure it is a POST request.

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

## Publishing a Plugin

Plugins can also be published and preconfigured to be displayed to users in the template section. To publish, create a pull request with your ApiCallPlugin in src/integrations/api.tsx of the bitbadges-frontend repository. The pull request should specify everything below. Also, give a brief summary of everything about the plugin (who created it?, risks?, what data it uses?).&#x20;

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
      userInputs: [{ key: 'username', label: 'Username', type: 'string' }],
      creatorInputs: [
        { key: 'username', label: 'Username', type: 'string' },
        { key: 'ss', label: 'Test', type: 'number' },
        { key: 'date', label: 'Date', type: 'date' },
        { key: 'd', label: 'Test', type: 'number' }
      ],
      hardcodedInputs: [{ key: 'fdsgsdfg', label: 'Test', value: 'test' }]
    }
  }
];

```
