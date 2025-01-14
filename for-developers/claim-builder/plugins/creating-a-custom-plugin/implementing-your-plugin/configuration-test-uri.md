# Configuration Test URI

Optionally, you can configure a URI that we call before allowing the claim creator to add the plugin to their claim. This is a helper URI and is used by our frontend to catch errors early and improve creator experience.

<figure><img src="../../../../../.gitbook/assets/image (179).png" alt=""><figcaption></figcaption></figure>

The URI can be configured when creating your plugin under the Tester URI input.

<figure><img src="../../../../../.gitbook/assets/image (177).png" alt=""><figcaption></figcaption></figure>

We expect a success: true if the user is allowed to proceed (but you can also provide a message for the user to double check or take additional action). If success: false, we do not allow them to add the plugin until a test succeeds.

```
200 OK - { success: true, message: 'Success. We have found records for ...'  } 
Non-200 BAD - { success: false, message: 'Please fix formatting for this parameter' }
```

Similar to the implementation of the plugin itself, we will provide the pluginSecret to allow you to verify that requests are coming from BitBadges.  We also provide the creator inputted parameters for you directly in the body to perform validation on. Only POST is supported for now.

The rest is left up to you. Your logic can do as little as you want or as much as you want. It can range from a simple type check to fully executing API logic and checking everything you need.

```typescript
const testerResponse = await axios.post(testerUri, {
    _isTester: true,
    publicParams,
    privateParams,
    pluginId,
    version,
    pluginSecret: plugin.pluginSecret
});
```

Note: This is just a frontend helper option to improve user experience but is not a replacement for proper logic implementation and schema validation on your end.
