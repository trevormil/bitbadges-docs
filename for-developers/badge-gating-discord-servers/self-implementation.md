# Self-Implementation

### **Obtaining (Username / ID, Address) Pairs**

**Option 1: Sign In with BitBadges (Recommended)**

{% content-ref url="../authenticating-with-bitbadges/" %}
[authenticating-with-bitbadges](../authenticating-with-bitbadges/)
{% endcontent-ref %}

Use the Sign in with BitBadges flow to authenticate your users. Use the **otherSignInMethods** to include Discord sign ins (in addition to crypto authentication).

```
otherSignIns: ['discord']
```

Then, in your redirect handler, you can implement the role assignment logic.

Sign In with BitBadges is easier to implement because it is OAuth2 compatible, you do not have to go through the custom plugin creation process, and you can also attach a claim with additional functionality (if needed).

**Option 2: BitBadges Claims**

You can also create a custom plugin with the **passDiscord** option enabled and implement the role assignment logic in your plugin handler logic. If you select this option, you can handle everything within a single BitBadges claim (e.g. as soon as a user claims, assign them a role).

Note though that a successful plugin response may not mean a successful claim (all plugins need to pass). &#x20;

{% content-ref url="../../overview/claim-builder/" %}
[claim-builder](../../overview/claim-builder/)
{% endcontent-ref %}

**Option 3: Public Lookups**

There may already be services that map addresses to Discord usernames or vice versa. If there are, you can optionally use those instead. All that you would need to do is lookup the (address, Discord ID) pair and assign the roles.

## Role Assignment Implementation

Below, we explore using Discord.js (below), but this can be any method for managing roles such as setting up a Zapier zap, or manually.

### Creating a Discord Bot

* Go to the [Discord Developer Portal](https://discord.com/developers/applications).
* Click on "New Application" and give your application a name.
* After creating the application, go to the "Bot" tab and click "Add Bot".
* Customize your bot's name and avatar if desired.
* Under the "Token" section, click "Copy" to copy your bot token. Keep this token secret! This is your BOT\_TOKEN int the code snippet below.

### Invite the Discord Bot to Your Server

* Go back to the Discord Developer Portal and select your application.
* Go to the "OAuth2" tab and then "URL Generator".
* Under "Scopes", select "bot".
* Under "Bot Permissions", select the permissions your bot needs (at minimum: "Send Messages", "Manage Roles").
* Copy the generated URL and open it in a new browser tab.
* Select the server you want to add the bot to and authorize it.

### Ensure Proper Hierarchy

* **View Server Roles:**
  * Right-click on your server's name and select "Server Settings".
  * Click on "Roles" in the left sidebar.
  * You'll see a list of all roles in the server.
* **Ensure Higher Priority:**&#x20;
  * Ensure the added bot role has a higher priority than the roles you wish to add. Bots can only assign roles lower in the hierarchy than them.

### Assigning Roles

In your handler, you are now ready to assign roles to users.&#x20;

```typescript
import Discord from 'discord.js';

const client = new Discord.Client({
  intents: ['Guilds', 'GuildMembers']
});

//Initiate Discord bot
const BOT_TOKEN = process.env.BOT_TOKEN;
client.login(BOT_TOKEN);

//TODO: Replace res with the Discord details passed from BitBadges
//      For claims, this will be payload.discord
//      For SIWBB, this will be res.otherSignIns.discord
const { id: discordUserId, username: discordUsername } = res;

const userId = discordUserId;
const guildId = process.env.GUILD_ID; //TODO: Configure with your server ID
const guild = await client.guilds.fetch(guildId ?? '');
if (!guild) {
  return res.status(400).json({ success: false, errorMessage: 'Guild not found' });
}

const member = await guild.members.fetch(userId);

//TODO: Configure with your role IDs
//      You can also use role.name for role names instead of role IDs
const role = guild.roles.cache.find((role) => role.id === process.env.ROLE_ID);
if (role && member) {
  await member.roles.add(role).then(() => {
    console.log(`User has been assigned the ${role.name} role.`);
  });
} else {
  throw new Error('Role or member not found');
}
```

You can customize everything further if you would like. We leave any other custom logic up to you like periodic retries, revoking, preventing replay attacks, flash ownership attacks, and so on. Much of this is application / badge specific to your requirements.&#x20;
