# Core vs Custom Plugins

**Core Plugins**

Core plugins are maintained by BitBadges and are used when we need to keep track of state (e.g. who has claimed? how many times? etc). These are all executed and maintained internally. If you want to add a core plugin, contact us.

**Custom Plugins - HTTP Requests**

We also support custom API plugins that can be created by anyone. In simple terms, these are just a configured HTTP request where the criteria is met if we receive a success status code (e.g. 200) and fail if we receive an error one.

For custom plugins, it is important to note that these must be stateless, or if you maintain state, you should not rely on a success response meaning a successful claim (other plugins could fail).

You can optionally choose to request to receive certain information about the claiming user.

<figure><img src="../../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

Additionally, you can configure plugins to have custom user inputs.

<figure><img src="../../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

{% content-ref url="using-the-claim-builder-api-plugin.md" %}
[using-the-claim-builder-api-plugin.md](using-the-claim-builder-api-plugin.md)
{% endcontent-ref %}

{% content-ref url="publishing-a-plugin.md" %}
[publishing-a-plugin.md](publishing-a-plugin.md)
{% endcontent-ref %}

