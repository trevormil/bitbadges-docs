# Publishing a Plugin

**Pre-Requisite**

See the prior page for the details about custom plugins and how to configure yours.

**Publishing a Plugin**

Plugins can also be published and preconfigured to be displayed to users in the directory / directly on-site. To publish, create a pull request with your ApiCallPlugin in src/integrations/api.tsx of the bitbadges-frontend repository. The pull request should specify everything below. Also, give a brief summary of everything about the plugin (who created it?, risks?, what data it uses?).&#x20;

For non-indexed compatible queries, you must support calls with **only** the cosmosAddress and any hardcoded inputs. No claimId, Discord details, X details, or custom user inputs are supported.&#x20;

The creator schema is what is to be configured by the claim creator, not the end user. The user schema is to be configured by the end user (the one claiming).

```typescript
export const ApiCallPlugins: ApiCallPlugin[] = [
  ...
  {
    id: 'github-contributions',
    parentPluginId: 'github',
    metadata: {
      name: 'Github Contributions',
      description: "Check a user's Github contributions.",
      image: <Avatar size={40} src={<GithubOutlined style={{ fontSize: 30 }} />} />,
      createdBy: 'BitBadges',
      nonIndexedCompatible: false,
      duplicatesAllowed: true,
      zapierCompatible: false
    },
    apiCallInfo: {
      method: 'POST',
      uri: 'https://api.bitbadges.io/api/v0/integrations/query/github-contributions',
      passDiscord: false,
      passEmail: false,
      passTwitter: false,
      passAddress: false,
      passGoogle: false,
      passGithub: true,
      userInputsSchema: [],
      creatorInputsSchema: [{ key: 'repository', label: 'Repository', type: 'string', helper: 'Ex: bitbadges/bitbadges-frontend' }],
      hardcodedInputs: []
    },
    detailsString: (apiCall) => {
      return (
        <div>
          <div className="">
            <b>Repository:</b> {apiCall.bodyParams?.repository}
          </div>
        </div>
      );
    }
  },
  ...
]

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
