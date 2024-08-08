# Alternatives

### Do you actually need a custom plugin?

Before going through the entire plugin  creation process, consider whether you actually need a plugin for your use case. For many use cases, you can develop them with workarounds using existing plugins. Get creative!

For example,

* Use the Custom Validate plugin as a more streamlined alternative.
* Use the Codes plugin and give out claim codes from your side to those who meet the criteria
* Give out a secret password to those who satisfy a criteria
* Many apps / services use emails to identify users. If you have a list of emails, consider copy / pasting them into the Email plugin rather than needing to implement everything.
* And so on.

Tip: If you redirect the user to the claim page with code=abc or password=123 in the URL, it will auto-populate the fields for them.

If you can implement everything you need while using existing plugins, consider creating  a configuration tool rather than a plugin.

{% content-ref url="../../configuration-tools.md" %}
[configuration-tools.md](../../configuration-tools.md)
{% endcontent-ref %}

### Custom Validation URL Plugin: Streamlined Alternative

If your plugin can function with at most this information and returning a 200 OK success code, consider using the already preconfigured Custom Validation URL plugin.

This is a streamlined alternative that can be used for many use cases with nos etup required. If you need additional features like state management or additional inputs, you may need to custom create one.

```typescript
{
  cosmosAddress: string;
  claimId: string;
  _isSimulation: boolean;
  lastUpdated: number;
  createdAt: number;
  claimAttemptId: string;
  assignMethod: string | undefined;
  isClaimNumberAssigner: boolean;
  maxUses: number;
  currUses: number;
  
  pluginId: string
  instanceId: string
  pluginSecret: string; //This is the validation secret entered (used to check origin)
}
```

<figure><img src="../../../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>
