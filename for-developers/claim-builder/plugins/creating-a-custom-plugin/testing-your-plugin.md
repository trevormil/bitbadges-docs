# Testing Your Plugin

There are two approaches to testing your plugin.

**Local Development**

Use your favorite tool to simulate requests to your handler such as the one below. Replace with your corresponding details.

```bash
curl -X POST https://bitbadges.io  -H 'Content-Type: application/json' -d '{
  "pluginSecret": "068145b0058668ac5b880e23ca2556e4207efe8066227f4eb3466a6b0d16daa4",
  "_isSimulation": false,
  "claimId": "abcxyz123",
  "claimAttemptId": "...",
  "currUses": 0,
  "maxUses": 10,
  "lastUpdated": 1800000000000,
  "createdAt": 1800000000000,
  "version": "0"
}'
```

**Claim Tester**

The easiest way to test your plugin integration with BitBadges is with the Claim Tester tab in the developer portal.

This is the only place you will be able to test unfinalized versions of your plugin.

<figure><img src="../../../../.gitbook/assets/image (8) (1).png" alt=""><figcaption></figcaption></figure>

After your plugin has been created (does not have to be published):

**Step 1:** Create a test claim which is configured with your plugin. The claim is not linked with any collection, list, etc, and will be deleted after you are done.

**Step 2:** Experiment with the claim and ensure your plugin is working correctly. Attempt to claim, try with different accounts, etc. Test it thoroughly. Try your configuration tools, user inputs, and more!

Use the console, developer tools, and error messages to debug. Also, do not hesitate to reach out for help.
