# Configuration Tools

Configuration tools are a helper layer abstracted over the core plugin implementations. Instead of creating new, individual plugins for each use case, you can often reuse existing ones. Configuration tools help the user configure the parameters of existing plugins.

For example,&#x20;

* Google Calendar can be implemented by configuring the Email plugin with the attendee emails
* Auto-configure approved user addresses with the Address Restrictions plugin
* Or any application can be implemented by issuing claim codes with the Codes plugin.

```
Note: This is a more advanced option and is not a great user experience. 
This should only be used in select cases.

Please create custom plugins for better UX.
```

Prompt the user to add the copy / paste the stringified JSON to the Configuration Tools tab on the claim builder.

<figure><img src="../../../.gitbook/assets/image (214).png" alt=""><figcaption></figcaption></figure>

```json
{"pluginId":"codes","version": "0", "publicParams":{"numCodes":1},"privateParams":{"codes":["code123"]}}
```
