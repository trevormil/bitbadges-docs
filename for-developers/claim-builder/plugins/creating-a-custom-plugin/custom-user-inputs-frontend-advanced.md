# Custom User Inputs Frontend

If you want to further customize the inputs process beyond just users inputting stuff directly in-site, consider creating a custom frontend as well to pass back inputs. This can be used, for example, for more secure authenticated logic.



**Setup + Configuration**

We can direct the user to your plugin's frontend URI provided when they are submitting the claim. for you to handle via your UI.

<figure><img src="../../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



Via the query params, we will pass some contextual information as well. Note that the address is only passed if you configue to receive the address in the configuration.

```typescript
window.open(baseUri + '?context=' + JSON.stringify(context), '_blank');
```

```typescript
export interface ContextInfo {
  address: string;
  claimId: string;
  cosmosAddress: string;
  createdAt: number;
  lastUpdated: number;
}
```

<figure><img src="../../../../.gitbook/assets/image (102).png" alt=""><figcaption></figcaption></figure>

**Logic Handling**

At the configured URL, you can then perform whatever logic you need to do (i.e. authentication, prompting the user, etc). This is left up to you. Once you are ready to submit, you can pass back the inputs via a JSON back to our site via a window.opener.postMessage call. The passed data will be the user's input body parameters for this specific plugin.

IMPORTANT: Please take extra care in this step. You want to ensure that only BitBadges can read the custom body. See [https://developer.mozilla.org/en-US/docs/Web/API/Window/postMessage](https://developer.mozilla.org/en-US/docs/Web/API/Window/postMessage) and other references. Use window.opener.postMessage and specify the origin as https://bitbadges.io.&#x20;

```typescript
import { Layout } from 'antd';
import { useRouter } from 'next/router';
import { useState } from 'react';
import { DisplayCard } from '../components/display/DisplayCard';

const FRONTEND_URL = 'https://bitbadges.io';
const { Content } = Layout;

function PluginTestScreen() {
  const router = useRouter();
  const { context } = router.query;

  let claimContext = undefined;
  try {
    claimContext = JSON.parse(context?.toString() || '');
  } catch (e) {
    console.error('Error parsing context', e);
  }

  const [customBody, setCustomBody] = useState<object>({
    testData: 'testData',
  });

  return (
    <Content className="full-area" style={{ minHeight: '100vh', padding: 8 }}>
      <div className="flex-center">
        <DisplayCard title="User Inputs - Plugin Logic" md={12} xs={24} sm={24} style={{ marginTop: '10px' }}>
          {/* 
            //TODO: Add your custom logic here to prompt the user for any additional information required for the claim. 
          */}


          <div className="flex-center">
            <button
              onClick={async () => {
                if (window.opener) {
                  console.log('Sending message to opener', customBody, FRONTEND_URL);
                  window.opener.postMessage(customBody, FRONTEND_URL);
                  window.close();
                }
              }}>
              Submit
            </button>
          </div>
        </DisplayCard>
      </div>
    </Content>
  );
}
```

Note that BitBadges should just be treated as the messenger or middleman here. Although, if implemented correctly, everything will be passed via secure communication channels,  it is not recommended to pass sensitive information via the body. A workaround might be to issue claim codes instead. Consider adding extra challenges and security to your execution flows which assume that communication is intercepted or BitBadges is compromised (e.g. claim codes with quick expirations, additional challenges , etc).

**Quickstart**

See the plugin-frontend.tsx in the BitBadges quickstart repository for a starting implementation.
