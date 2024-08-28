# Badge-Gating Discord Servers

You may want to gate your Discord server or specific channels with badges. Below, we walk you through the process of doing so.

### Set Up a Sign In with BitBadges Callback Handler

{% content-ref url="../" %}
[..](../)
{% endcontent-ref %}

Use the Sign in with BitBadges flow to get the authentication details for your users. Make sure to add the **otherSignInMethods** to include Discord sign ins.&#x20;

```
otherSignIns: ['discord']
```

You can also customize everything further if you would like. We leave any other custom logic up to you like periodic retries, revoking, preventing replay attacks, flash ownership attacks, and so on. Much of this is application / badge specific to your requirements.&#x20;

### Assigning Roles

Once you get the details in your handler, you can verify and assign roles to successful authentication requests. This can be using any method you wish such as Discord.js, setting up a Zapier zap, or manually.

```typescript
import Discord from 'discord.js';

const client = new Discord.Client({
  intents: ['Guilds', 'GuildMembers']
});

//See signIn.ts for self-verification example (not API)

//Initiate Discord client
const CLIENT_ID = process.env.CLIENT_ID;
const CLIENT_SECRET = process.env.CLIENT_SECRET;
const REDIRECT_URI = process.env.REDIRECT_URI;
const BOT_TOKEN = process.env.BOT_TOKEN;
client.login(BOT_TOKEN);

...

const discordUserId = userResponse.data.id;
const discordUsername = userResponse.data.username;

const userId = discordUserId;
const guildId = process.env.GUILD_ID;
const guild = await client.guilds.fetch(guildId ?? '');
if (!guild) {
  return res.status(400).json({ success: false, errorMessage: 'Guild not found' });
}

const member = await guild.members.fetch(userId);
const role = guild.roles.cache.find((role) => role.name === process.env.ROLE_ID);
if (role && member) {
  await member.roles.add(role).then(() => {
    console.log(`User has been whitelisted and assigned the ${role.name} role.`);
  });
} else {
  throw new Error('Role or member not found');
}

```
