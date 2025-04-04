# Testing Your Plugin

There are two approaches to testing your plugin.

**Local Development**

Use your favorite tool to simulate requests to your handler such as the one below. Replace with your corresponding details.

```bash
curl -X POST https://yourhandler.com  -H 'Content-Type: application/json' -d '{
  "pluginSecret": "068145b0058668ac5b880e23ca2556e4207efe8066227f4eb3466a6b0d16daa4",
  "claimId": "abcxyz123",
  "claimAttemptId": "...",
  "lastUpdated": 1800000000000,
  "createdAt": 1800000000000,
  "version": "0",
  "_attemptStatus": "executing",
  ...USER_ID_DETAILS, //whatever is configured
  ...YOUR_INPUTS
}'
```

**Test Requests**

In the creation / update interface, there will be a Send Test Request button which will send a formatted request from your browser. This uses mocked data, and should only be used for testing purposes.

<figure><img src="../../../../.gitbook/assets/image (147).png" alt=""><figcaption></figcaption></figure>

**Claim Tester**

The easiest way to test your plugin integration with BitBadges is with the Claim Tester tab in the developer portal. This is the only place you will be able to test unfinalized versions of your plugin.

<figure><img src="../../../../.gitbook/assets/image (8) (1) (1).png" alt=""><figcaption></figcaption></figure>