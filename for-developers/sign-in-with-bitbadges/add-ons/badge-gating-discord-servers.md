# Badge-Gating Discord Servers

You may want to gate your Discord server or specific channels with badges. Below, we walk you through the process of doing so.

### Set Up a Sign In with BitBadges Callback Handler

{% content-ref url="../../authenticating-with-bitbadges/" %}
[authenticating-with-bitbadges](../../authenticating-with-bitbadges/)
{% endcontent-ref %}

Use the Sign in with BitBadges flow to get the authentication details for your users. Make sure to add the **otherSignInMethods** to include Discord sign ins.&#x20;

```
otherSignIns: ['discord']
```

You can also customize everything further if you would like. We leave any other custom logic up to you like periodic retries, revoking, preventing replay attacks, flash ownership attacks, and so on. Much of this is application / badge specific to your requirements.&#x20;

### Assigning Roles

Once you get the details, you can verify and assign roles to successful authentication requests. This can be using any method you wish such as Discord.js, setting up a Zapier zap, or manually.
