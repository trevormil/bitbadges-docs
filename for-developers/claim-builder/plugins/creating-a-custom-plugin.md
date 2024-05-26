# Creating a Custom Plugin

Custom plugins are, in simple terms, just a configured HTTP request that we call upon attempting to claim. The plugin (your logic) will handle if the claim attempt should be denied or successful, plus potentially tells us how to manage the plugin state. All plugins must pass for a claim attempt to be successful.

To create, publish, and maintain your plugin, go to [https://bitbadges.io/developer](https://bitbadges.io/developer) and use the Plugins tab.

Publishing involves passing a review process. Published plugins will be displayable in the directory and selectable by anyone creating a claim. You can also create your own custom private plugin and add it when creating the claim. Private plugins will not be shown in the directory.

<figure><img src="../../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

### User Inputs (Frontend)

If your plugin requires no user prompting, then you can skip this section. To be compatible with Zapier, user inputs are not allowed.

**Setup + Configuration**

If your plugin requires user inputs from the frontend side, we will direct the user to your plugin's frontend URI provided when they are submitting the claim.&#x20;

<figure><img src="../../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>



Via the query params, we will pass some contextual information as well.

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

<figure><img src="../../../.gitbook/assets/image (102).png" alt=""><figcaption></figcaption></figure>

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

### Creation Parameters

If you need to allow the claim creator to configure parameters (e.g. max 10 uses per user), this is also left up to you. You can provide a URL for how to do so when creating the plugin. The claim creator will be directed to this URL.

These parameters are left completely up to you. We do not store any of them. There is no window.postMessage or anything. You should store these on your end per claim (if needed).

If no creator URI is provided, we assume there is nothing additional the claim creator has to do.

<figure><img src="../../../.gitbook/assets/image (103).png" alt=""><figcaption></figcaption></figure>

### **Backend Request**

The outgoing request (from BitBadges to your plugin) will be made up of the custom body inputs (passed from your frontend), plus some contextual information about the claim and the claiming user.

* **Plugin Secret:** A plugin secret value that you can use to verify BitBadges as the origin of the call. This is secret only to you and can be obtained via the developer portal when creating your plugin.
* **Claiming Address:** The **cosmosAddress** of the user who is attempting to claim.
* **Simulation**: For simulated claims, we pass the \_isSimulation = true. You can use this flag, for example, to not update important state information for simulations.
* **Claim Information**: Lastly, we also pass the **claimId,** as well as the claim's **createdAt** and **lastUpdated** timestamps. These can be used, for example, to implement version control systems on your end.

For POST, PUT, and DELETE requests, we pass the values over the body. For GET, we pass them over the GET params. You are responsible for making sure the endpoint is accessible (e.g. no CORS errors, etc.). Make sure it is the desired type as well (i.e. GET vs POST vs DELETE vs PUT).

<figure><img src="../../../.gitbook/assets/image (104).png" alt=""><figcaption></figcaption></figure>

```typescript
const payload = {
    ...customBody,//if applicable
        
    // Context info
    pluginSecret: pluginDoc.pluginSecret,
    claimId: context.claimId,
    cosmosAddress: apiCall?.passAddress ? context.cosmosAddress : null,
    _isSimulation: context._isSimulation,
    lastUpdated: context.lastUpdated,
    createdAt: context.createdAt
};
```

### **Handling**

The custom logic of the plugin is left up to you. From the provided request, you can check everything you need, perform the custom logic, and more.

#### State

An important aspect to consider is how you will handle state (if applicable). The golden rule here is that a successful response from your plugin DOES NOT mean the overall claim attempt was successful. Other plugins might fail.

If your plugin depends on whether a claim is eventually successful or not (e.g. restrict to 5 claims per user), handling state on your end may not be applicable because it cannot know for certain whether a claim is successful or not. To help such plugins, you may use the preset state functions configured by the responses (see Responses), or you can consider utilizing other already implemented plugins to do such work for you. For example, if you want to implement a query of Discord users (one claim per user) who attended an event, the one claim per user must be set and tracked on the BitBadges end.

Otherwise, you can handle state on your end. An example of this might be if you are checking a user's GitHub contributions, a contribution cannot be undone, so even if the claim fails, the plugin will still work as intended eventually. If you are handling state on your end, a design decision to consider is whether to update at user input time or claim processing time.

**Plugin Secret**

The plugin secret will be given to you upon creation of the plugin, and BitBadges will pass this to every request, so you can ensure BitBadges as the origin of the call. This is important to maintain privacy.

**Private Values**

Private values are not supported by default. Private claim parameters can be configured and handled by you (see [Creation Parameters](creating-a-custom-plugin.md#creation-parameters) above). Anything else private should be managed and handled by you (e.g. API keys, etc). Consider setting up a proxy intermediary, if necessary.

The user inputs can be assumed to be passed through secure communication channels, but like explained above, add extra measures to protect against certain worst case scenarios.

**Authentication**

A typical use case for a plugin is to perform authenticated logic for your users. However, plugins do not have access to BitBadges sessions and cannot implement typical session cookies. Treat BitBadges as the middleman.

The workaround for this is to give your authenticated user a unique value (such as an authorization code) only known to them. This will be passed to the claim body inputs. Upon receiving it in your backend (at claim time), you can associate the current claim to the unique value.

This approach follows the same flow as OAuth authorization codes, except with a custom claim code. You should follow all the same best practices (expiring tokens, PKCE for preventing authorization code interception attacks, and more). Note that claims may take a couple minutes for the user to complete the process, so a 30 second expiration time, for example, may be too low.

<figure><img src="../../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

### **Response**

All responses expect a 200 success OK status code for a successful.

**Stateless Preset**

The stateless preset is simple. If we receive the 200, the plugin is successful. Nothing else is checked via the response. Everything is handled on your end (if you have state).&#x20;

<figure><img src="../../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

**Claim Token Preset**

This preset expects a { claimToken} in the response. The claim token is a one-time use only claim code. Issuing claim tokens is left up to you. We will deny a user who attempts to use a claim token a second time.

<figure><img src="../../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

### Reusing for Non-Indexed Balances

Pre-Reading: [Claim Actions](../claim-actions.md)

If your plugin does not require user inputs, you may be able to reuse it for non-indexed balance assignment. Such plugins must be stateless, require no user inputs, and can function with only the contextual information passed.

### Testing Your Plugin

Before publishing, please test your plugin thoroughly. An easy way to do this is to test it with an address list claim. The address list can be deleted after you are done testing. Or, test it out on a testnet.

When creating the claim, select the custom plugin option to add your own custom plugin details.

### Updating Your Plugin

After publishing and completing the review process, we leave updates and version control management up to you. It is your responsibility to keep claims compatible and functioning. Updating can be done at https://bitbadges.io/developer for the stuff stored on our end. On your end, you have control over what you&#x20;

If you need to implement a breaking change, consider using the createdAt or lastUpdated fields passed to implement version control.

### Deleting Your Plugin

You can delete your plugin via https://bitbadges.io/developer. Note that this is a soft delete. It removes it from the directory, but all details will still be stored to allow existing claims to still be attempted.

### **Further Customization**

In the future, we are looking to expand on the customization options to allow you to build your plugin exactly how you want. If you would like further customization (custom UI components, other presets to add, custom state functions, etc), reach out to us.
