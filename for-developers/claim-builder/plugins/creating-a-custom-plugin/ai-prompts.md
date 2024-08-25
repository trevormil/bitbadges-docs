# AI Prompts

Fill in as needed. Note this is experimental.

In the developer portal, use the JSON feature to copy / paste the resulting configuration JSON.

```
Using the following interface, could you build a BitBadges plugin 
JSON, handler function, and potentially the frontend helper redirects
for ENTER_YOUR_IDEA_HERE. 

My pluginId is ENTER_HERE and the version is ENTER_HERE (0 for new ones).

/** Other custom instructions (intended frameworks, languages, etc) **/

BitBadges accepts a JSON configuration (which should be configured in-site with the JSON
feature), and the plugin (you) should have an HTTP API handler that implements the custom logic.

Depending on the configuration, you may also have a user input redirect frontend page and / or
a tutorial for the claim creator to configure parameters correctly.

You can additionally provide a tutorial for parameter configuration for the claim creator. Do not use toolUri. Use tutorialUri only. You may request authentication here.
Leave blank if not needed.

Some additional information to note:
- Reusing for non-indexed balances should only be true if it can function with no user inputs and just a crypto address.
- postProcessingJs is not to be used. This is an internal tool only.
- requiresSessions is false sisnce this is custom created.

The state function preset should be "Stateless" or "ClaimToken". If claimToken,
the handler should return a 200 OK with a unique one time use only "claimToken": "..." 
in the body. For preventing double claiming, ensure the same claim token is returned
for duplicate requests / criteria as necessary. If stateless, a 200 OK is all that is needed. Note that a successful 
response does not always mean the claim is successful (other plugins may fail).

Certain identifiers may be passed automatically (passAddress (crypto address), passGithub, pass email). These
do not pass any sensitive information (only username / ID / address). If not needed, leave as false.

The handler may optionally choose to maintain state as necessary on the plugin end.

export interface iPluginVersionConfig<T extends NumberType> {

  /** Version of the plugin */

  version: T;

  /** True if the version is finalized */

  finalized: boolean;

  /** The time the version was created */

  createdAt: UNIXMilliTimestamp<T>;

  /** The time the version was last updated */

  lastUpdated: UNIXMilliTimestamp<T>;

  /** Reuse for nonindexed balances? Only applicable if is stateless, requires no user inputs, and requires no sessions. */

  reuseForNonIndexed: boolean;

  /** Reuse for address list claims? Address list claims are similar to standard badge claims and supports all features, except order of claims (claim numbers) do not matter. */

  reuseForLists: boolean;

  /** Preset type for how the plugin state is to be maintained. */

  stateFunctionPreset: PluginPresetType;

  /** Whether it makes sense for multiple of this plugin to be allowed */

  duplicatesAllowed: boolean;

  /** This means that the plugin can be used w/o any session cookies or authentication. */

  requiresSessions: boolean;

  /** This is a flag for being compatible with auto-triggered claims, meaning no user interaction is needed. */

  requiresUserInputs: boolean;

  userInputsSchema: Array<JsonBodyInputSchema>;

  publicParamsSchema: Array<JsonBodyInputSchema | { key: string; label: string; type: 'ownershipRequirements'; headerField?: boolean }>;

  privateParamsSchema: Array<JsonBodyInputSchema | { key: string; label: string; type: 'ownershipRequirements'; headerField?: boolean }>;

  userInputRedirect?: {

    baseUri: string;

  };

  claimCreatorRedirect?: {

    toolUri?: string;

    tutorialUri?: string;

  };

  /** The verification URL */

  verificationCall?: {

    uri: string;

    method: 'POST' | 'GET' | 'PUT' | 'DELETE';

    hardcodedInputs: Array<JsonBodyInputWithValue>;

    passAddress?: boolean;

    passDiscord?: boolean;

    passEmail?: boolean;

    passTwitter?: boolean;

    passGoogle?: boolean;

    passGithub?: boolean;

    passTwitch?: boolean;

    passStrava?: boolean;

    postProcessingJs: string;

  };

}

/**

 * @category Claims

 */

export type JsonBodyInputWithValue = {

  key: string;

  label: string;

  type?: 'date' | 'url';

  value: string | number | boolean;

  headerField?: boolean;

};

/**

 * @category Claims

 */

export type JsonBodyInputSchema = {

  key: string;

  label: string;

  type: 'date' | 'url' | 'string' | 'number' | 'boolean';

  helper?: string;

  headerField?: boolean;

  required?: boolean;

  defaultValue?: string | number | boolean;

  options?: {

    label: string;

    value: string | number | boolean;

  }[];

  arrayField?: boolean;

};

For the handler, here is a template. Everything will be provided in the body as 
const payload = {
    ...customBody,//if applicable
    ...allConfiguredParams, //if applicable
        
    // Context info
    
    email: { id: 'bob@abc.com' }, //If pass email is configured
    discord: { id: '...', username: '...', discriminator: '...' }, //If configured
    twitch: { id: '...', username: '...' }, //If configured
    twitter: { id: '...', username: '...' }, //If configured
    github: { id: '...', username: '...' }, //If configured
    google: { id: '...', username: '...' }, //If configured
    priorState: { }, //If using state transition preset function (see below)
    pluginSecret: pluginDoc.pluginSecret,
    claimId: context.claimId,
    claimAttemptId: context.claimAttemptId,
    cosmosAddress: context.cosmosAddress, //If pass address is configured
    _isSimulation: context._isSimulation,
    lastUpdated: context.lastUpdated,
    createdAt: context.createdAt,
    maxUses: context.maxUses,
    currUses: context.currUses,
    version: context.version
};

//TODO: Fill in missing information
const handlePlugin = async (req: NextApiRequest, res: NextApiResponse) => {
  try {
    //Step 1: Handle the request payload from the plugin
    const body = req.body; //We assume the plugin sends the payload in the body of the request (change this for GET)
    const { priorState, claimId, pluginSecret, cosmosAddress, _isSimulation, lastUpdated, createdAt } = body;
    const { ...otherCustomProvidedInputs } = body;

    //Step 2: Verify BitBadges as origin by checking plugin secret is correct
    const YOUR_PLUGIN_SECRET = '';
    if (pluginSecret !== YOUR_PLUGIN_SECRET) {
      return res.status(401).json({ message: 'Invalid plugin secret' });
    }

    //Step 3: Implement your custom logic here. Consider checking the plugin's creation / last updated time to implement version control.
    //TODO: 

    //Step 4: Return the response to the plugin based on your configured state function preset
    // const claimTokenRes = { claimToken: '...'  }
    // const stateTransitionRes = { ...newState }
    // const statelessRes = {};
    return res.status(200).json({});
  } catch (err) {
    console.log(err);
    return res.status(401).json({ message: `${err}` });
  }
};

export default handlePlugin;



If you need authentication from the user at claim time, use the redirect URI and pass the custom body back
via another custom frontend handler such as below. The userInputsSchema should match
the returned inputs schema.

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

  const expectedPluginId = '....';

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
                if (claimContext.pluginId !== expectedPluginId) {
                  throw new Error("Invalid plugin ID")
                }
              
                if (window.opener) {              
                  const res = {
                     inputs: customBody,
                     pluginId: expectedPluginId,
                     instanceId: claimContext.instanceId,
                     version: claimContext.version //TODO: Handle version control as necessary
                  };
                  
                  
                  console.log('Sending message to opener', res, FRONTEND_URL);
                  window.opener.postMessage(res, FRONTEND_URL);
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
