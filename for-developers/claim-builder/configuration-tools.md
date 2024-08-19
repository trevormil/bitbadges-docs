# Configuration Tools

Configuration tools are a helper layer abstracted over the core plugin implementations. Instead of creating new, individual plugins for each use case, you can often reuse existing ones. Configuration tools help the user configure the parameters of existing plugins.

For example,&#x20;

* Google Calendar can be implemented by configuring the Email plugin with the attendee emails
* Auto-configure approved user addresses with the Address Restrictions plugin
* Or any application can be implemented by issuing claim codes with the Codes plugin.

These tools help automate the process of plugin  configuration for the creator automatically.

<figure><img src="../../.gitbook/assets/image (128).png" alt=""><figcaption></figcaption></figure>

Tools can be featured / highlighted, but we also leave them to be super generic. You can navigate to any tool URL for custom help.

<figure><img src="../../.gitbook/assets/image (129).png" alt=""><figcaption></figcaption></figure>

**E2E Examples - BitBadges Official Tools**

{% @github-files/github-code-block url="https://github.com/BitBadges/bitbadges-tools" %}

**Implementation**

From the site, we will redirect the user to your tool URL and expect a window.postMessage back. Please ensure the target origin is set to https://bitbadges.io for security concerns.

For the time being, we only allow the **pluginId, publicParams, privateParams,** and **metadata** to be set. We also only allow tools to be used when creating a plugin, not updating existing ones. **instanceId** will be set for you, and a default blank state will be used.

All should obey the respective schemas. Unknown fields will be ignored.

We will pass along certain contextual information such as claimId via the URL query parameters.

```tsx
import { ClaimIntegrationPrivateParamsType, ClaimIntegrationPublicParamsType } from 'bitbadgesjs-sdk';

function PluginTestScreen() {
  const router = useRouter();
  const { context } = router.query;

  let claimContext = undefined;
  try {
    claimContext = JSON.parse(context?.toString() || '');
  } catch (e) {
    console.error('Error parsing context', e);
  }
  const claimId = claimContext?.claimId
  
  ...

  return (
    <div className="flex-center">
      <button
        onClick={async () => {
          if (window.opener) {
            window.opener.postMessage(
              {
                pluginId: 'email',
                publicParams: {} as ClaimIntegrationPublicParamsType<'email'>,
                privateParams: {
                  ids: ['test@test.com']
                } as ClaimIntegrationPrivateParamsType<'email'>,
                metadata: { name: '', description: '' } //optional
              },
              'https://bitbadges.io'
            );
            window.close();
          }
        }}>
        Submit
      </button>
    </div>
  );
}

export default PluginTestScreen;

```

**Testing**

When creating a claim, you should see an input box for a custom tool URL. Enter your URL there and test it out!

Or, for more rapid development, enter the tool JSON you pass back via postMessage manually here. This is nice because you do not have to use a deployed production version of your tool every time.&#x20;

```json
{"pluginId":"codes","publicParams":{"numCodes":1},"privateParams":{"codes":["code123"]}}
```

<figure><img src="../../.gitbook/assets/image (4) (1).png" alt=""><figcaption></figcaption></figure>

**Publishing / Featuring**

Create a pull request to this repository with your tool and its information. If you want us to feature it directly in-site, let us know!

If your tool is for your custom plugin, add the URL under the claim creator section, and it will be displayed to users when they are attempting to use the plugin.

{% @github-files/github-code-block url="https://github.com/BitBadges/bitbadges-tutorials/tree/main" %}
