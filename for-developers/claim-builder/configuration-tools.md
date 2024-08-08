# Configuration Tools

Configuration tools help automate the plugin configuration process for the creator. Instead of creating a tutorial for the creator to follow, you can create a configuration tool that automatically passes back all input configurations for the plugin automatically.

Tools will be permissionless such that the user can enter any tool URL and get configuration parameters; however, a specific plugin can define a featured tool to be highlighted.

<figure><img src="../../.gitbook/assets/image (128).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (129).png" alt=""><figcaption></figcaption></figure>

**Implementation**

We will redirect the user to your tool URL and expect a window.postMessage back. Please ensure the target origin is set to https://bitbadges.io for security concerns.

For the time being, we only allow the **pluginId, publicParams, privateParams,** and **metadata** to be set during the initial creation process.

```tsx
import { Layout } from 'antd';
import { ClaimIntegrationPrivateParamsType, ClaimIntegrationPublicParamsType } from 'bitbadgesjs-sdk';
import { InformationDisplayCard } from '../components/display/InformationDisplayCard';
import { FRONTEND_URL } from '../constants';

const { Content } = Layout;

function PluginTestScreen() {
  return (
    <Content className="full-area" style={{ minHeight: '100vh', padding: 8 }}>
      <div className="flex-center">
        <InformationDisplayCard title="Inputs" md={12} xs={24} sm={24} style={{ marginTop: '10px' }}>
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
        </InformationDisplayCard>
      </div>
    </Content>
  );
}

export default PluginTestScreen;

```

**Publishing**

Create a pull request to this repository with your tool and its information. If you want us to feature it directly in-site, let us know!

{% @github-files/github-code-block url="https://github.com/BitBadges/bitbadges-tutorials/tree/main" %}
