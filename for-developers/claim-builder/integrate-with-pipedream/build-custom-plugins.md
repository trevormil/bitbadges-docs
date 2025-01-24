# Build Custom Plugins

Building custom plugins with Pipedream could streamline a lot of the development process for you. This is a unique feature that Zapier doesn't offer because you can execute custom code from their SDK rather than just automated workflows that are setup.

Below, we provide an overview in how to do so. For further implementation guides, see the official Pipedream docs and the Custom Plugins docs. All principles of creating and setting up your custom BitBadges plugin apply. This will just streamline the authentication and logic handling on your end.&#x20;

{% content-ref url="../plugins/" %}
[plugins](../plugins/)
{% endcontent-ref %}

Note when building custom plugins, it is typically not recommended to trust full on automation workflows since these are asynchronous and may not handle the claim plugin logic quick enough. Please use direct API calls or code executed directly, or if you do trigger a full automation workflow, return a quick 200 OK and expect logic to be processed asynchronously (e.g. post-claim rewards).

[https://pipedream.com/docs](https://pipedream.com/docs)

**Pipedream SDK**

If you only need your own authentication and not end users, you can simply set this up with the standard SDK code.&#x20;

```javascript
import { axios } from "@pipedream/platform"
export default defineComponent({
  props: {
    slack: {
      type: "app",
      app: "slack",
    }
  },
  async run({steps, $}) {
    return await axios($, {
      url: `https://slack.com/api/users.profile.get`,
      headers: {
        Authorization: `Bearer ${this.slack.$auth.oauth_access_token}`,
      },
    })
  },
})
```

**Pipedream Connect**

Pipedream Connect allows you to actually obtain the access tokens for your users and directly execute API requests on their behalf all programmatically. This takes all of the authentication setup out for you. This is great for seamlessly building third-party custom plugins. Simply setup Pipedream Connect in your app, and then, you have SDK access to 1000s of API integrations at your fingertips. We refer you to their docs for setting this up.

Note that there are two flows for authenticating users:

* Manually triggered by your frontend - If you already have a frontend, you may consider just handling it all there.
* [Connect links](https://pipedream.com/docs/connect/connect-link) - You redirect the user to a Pipedream connect URL and Pipedream handles it for you. This could be useful if you want to make a headless plugin without a custom frontend.

Your frontend URL or connect URL can be configured in the tutorial / user input redirect URI when building your custom plugin. Then, once you have the authentication, you can handle your logic in the plugin handler as you see fit.&#x20;

Or, alternatives including adding custom instructions or developing a configuration tool.

<figure><img src="../../../.gitbook/assets/image (211).png" alt=""><figcaption></figcaption></figure>
