# Badge-Gating Discord Servers

You may want to gate your Discord server or specific channels with badges. Below, we walk you through the process of doing so.

### Step 1: Prep

Prep your server by creating the roles you want to assign and gating certain channels to only be accessible if you have that role. This can all be done in the Discord app.

Next, you will want to get your GUILD\_ID aka server ID by:

"To get the server ID for the first parameter, open Discord, go to Settings → Advanced and enable developer mode. Then, right-click on the server title and select "Copy ID" to get the guild ID."

### Step 2: Create a Bot

Go to [https://discord.com/developers/applications](https://discord.com/developers/applications) and create a new application.

Go to bot -> Token and get your BOT\_TOKEN.

Go to OAuth2 -> General to get your CLIENT\_ID and CLIENT\_SECRET.

### Step 3: Add a Redirect URI

Under OAuth2 -> General, add a redirect URI. In this tutorial, we assume you are using the quickstart repo, so put the following. Adjust as necessary for your callback handler (see following steps).

```shellscript
http://localhost:3000/api/integrations/discordVerify
```

### Step 4: Invite Bot to Server

You will invite your bot to your server via Oauth2 -> URL generator. Select the following options in the image and enter your redirect URI from Step 3. You can play around with the options, but it must be able to identify members and assign roles.

<figure><img src="../../.gitbook/assets/image (58).png" alt=""><figcaption></figcaption></figure>

It will generate a URL for you. Copy and paste this into your browser and select your server to invite it to.

IMPORTANT: You must make sure that the bot's role is above the role you are trying to assign in the hierarchy. You can do this by manually dragging it in the Roles menu.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

You may also have to enable these.

<figure><img src="../../.gitbook/assets/image (59).png" alt=""><figcaption></figcaption></figure>

### Step 5: Callback Handler

Setup the quickstart repository. Specify the fields in the .env obtained from prior steps. ROLE\_ID will be the name of the role you want to assign. We refer you to pages/api/discordVerify.ts in the quickstart for further implementation.&#x20;

The callback handler needs to check and authenticate two separate things: 1) authenticate address ownership / badge ownership and 2) authenticate Discord ownership.

```typescript
const code = req.query.code;
const state = req.query.state as string;
const stateData = JSON.parse(state ?? '{}');
const { message, signature, publicKey, options } = stateData; //You can also check the verificationResponse.success field to see if the message was verified on the clientside but this value should not be trusted.
const verifyOption = options ? JSON.parse(options) : undefined;
```

### Step 6: Generate URL

{% content-ref url="authentication-url-+-parameters/" %}
[authentication-url-+-parameters](authentication-url-+-parameters/)
{% endcontent-ref %}

Make sure to check the Discord option and enter your CLIENT\_ID and REDIRECT\_URI.

This is the URL users will navigate to in order to obtain the role. They will be walked through the process of authenticating both with their Web3 address and with Discord. Upon the success of both, they will be redirected to the REDIRECT\_URI (aka your callback handler).&#x20;

The callback handler will verify everything and assign the role (if criteria is met). Test it out to make sure everything works.

### Step 7: Production + Add-Ons

In production, you will need to change the REDIRECT\_URI from localhost to something else.&#x20;

You can also customize everything further if you would like. We leave any other custom logic up to you like periodic retries, revoking, preventing replay attacks, flash ownership attacks, and so on. Much of this is application / badge specific to your requirements.&#x20;





{% @github-files/github-code-block url="https://github.com/BitBadges/bitbadges-quickstart/blob/main/src/pages/api/discordVerify.ts" %}
