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

<figure><img src="../../../.gitbook/assets/image (58).png" alt=""><figcaption></figcaption></figure>

It will generate a URL for you. Copy and paste this into your browser and select your server to invite it to.

IMPORTANT: You must make sure that the bot's role is above the role you are trying to assign in the hierarchy. You can do this by manually dragging it in the Roles menu.

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

You may also have to enable these.

<figure><img src="../../../.gitbook/assets/image (59).png" alt=""><figcaption></figcaption></figure>

### Step 5: Set Up Discord Callback Handler + Role Assignment

Setup the quickstart repository. Specify the fields in the .env obtained from prior steps. ROLE\_ID will be the name of the role you want to assign. We refer you to pages/api/discordVerify.ts in the quickstart or the Discrord API for further implementation on assigning roles / authenticating users from the Discord side.

**Frontend Redirecting**

From your frontend, you will have users redirect to the Discord auth URI. Upon success, they will be redirected to the callback.

```typescript
const discord = {
  clientId: '...',
  redirectUri: '...'
}

const state = { ... }

const discordDetails = discord ? JSON.parse(discord as string) : undefined;
if (discordDetails?.clientId && discordDetails.redirectUri) {
  window.open(
    `https://discord.com/api/oauth2/authorize?client_id=${discordDetails.clientId}&redirect_uri=${discordDetails.redirectUri}&response_type=code&scope=identify&state=${JSON.stringify({ state })}`,
    '_blank'
  );
}
```

**Callback Handler**

{% @github-files/github-code-block url="https://github.com/BitBadges/bitbadges-quickstart/blob/main/src/pages/api/integrations/discordVerify.ts" %}

### Step 6: Establish Link + Sign In with BitBadges

Once you have your Discord side setup, you need to handle Sign In with BitBadges. We leave this step up to you.  This is also already implemented int he quickstart and documented via the page linked below. We refer you there. Within this request, you will verify badge ownership.

{% content-ref url="../" %}
[..](../)
{% endcontent-ref %}

Remember, there are three things that need to happen:

1. Authenticated with Discord
2. Authenticated with BitBadges
3. Link established between address and Discord ID somehow

Step 3 is especially important and can be a little tricky. You have to tie incoming requests to the callback to the correct address. Consider to make use of the state variables passed to the redirect URI or implement sessions where you verify the user is authenticated on both BitBadges and Discord before assigning.

### Step 7: Production + Add-Ons

In production, you will need to change the REDIRECT\_URI from localhost to something else.&#x20;

You can also customize everything further if you would like. We leave any other custom logic up to you like periodic retries, revoking, preventing replay attacks, flash ownership attacks, and so on. Much of this is application / badge specific to your requirements.&#x20;
