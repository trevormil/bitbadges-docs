# Role Assigner Plugin

## Setting Up BitBadges Bot for Your Discord Server

Follow these steps to set up the BitBadges Bot and configure it for your Discord server.

### 1. Create Actions App in Developer Portal

1. Go to the [BitBadges developer portal](https://bitbadges.io/dev).
2. Create a new actions app.
3. Save your client secret securely; you'll need it later.

### 2. Invite the BitBadges Bot to Your Server

1. Visit the [BitBadges Bot invitation page](https://bitbadges.io/bot).
2. Select your server from the dropdown menu.
3. Review and grant the necessary permissions.
4. Click "Authorize" to add the bot to your server.

### 3. Ensure Proper Hierarchy of Roles

1. View Server Roles:
   * Right-click on your server's name and select "Server Settings".
   * Click on "Roles" in the left sidebar.
   * You'll see a list of all roles in the server.
2. Ensure Higher Priority:
   * Locate the role assigned to the BitBadges Bot.
   * Drag the bot's role above the roles you want it to manage.
   * Remember: Bots can only assign roles lower in the hierarchy than their own role.

### 4. Configure Claim Plugin

1. Set up a BitBadges claim. Add the Discord Role Assigner plugin and configure the parameters.

Remember to keep your client secret secure and never share it publicly. If you encounter any issues or need further assistance, consult the [BitBadges documentation](https://bitbadges.io/docs) or reach out to their support team.

<figure><img src="../../.gitbook/assets/image (133).png" alt=""><figcaption></figcaption></figure>
