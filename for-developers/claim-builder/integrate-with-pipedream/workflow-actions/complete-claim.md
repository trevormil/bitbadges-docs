# Complete Claim

To complete a claim, enter this code in your workflow with [the BitBadges integration](https://pipedream.com/apps/bitbadges) and adapt as needed. &#x20;

The **claimInfo** is in the format: ${claimId}-${passwordPluginInstanceId}-${password}. You can setup the password plugin manually or select the Automation Workflow completion method -> Copy.

This is the approach we use for automation workflows. We remove any sign in requirements, but anyone claim must specify the secret password (only known to Pipedream / Zapier).

```
62f59244fc1003e331f183c4b3907f87-ecdb0ed0513716af3124899f6e7b5da70eb85c4399dafe2bf970236b34940da9-30892aa1bb0f84b19ecf244958c9cd4676400e13a24791e7512bc88efa217c89
```

<figure><img src="../../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```typescript
import { axios } from "@pipedream/platform";

//TODO: Edit where you get the props from. Static manually entered props are in via
//      the props field. Or you can configure to use steps.trigger.event.propName
//      for dynamic props from the trigger or other steps

export default defineComponent({
  props: {
    bitbadges: {
      type: "app",
      app: "bitbadges",
    },
    claimInfo: {
      type: "string", // Claim details passed as a string in the format "claimId-passwordPluginId-password"
    },
    address: {
      type: "string", // Address of the user
    },
    isSimulation: {
      type: "boolean", // Boolean to determine if this is a simulated run
      default: false,
    },
  },
  async run({ steps, $ }) {
    const details = this.claimInfo.split("-");
    if (details.length !== 3) {
      throw new Error("Invalid claim details parsed");
    }

    const claimId = details[0];
    const passwordPluginId = details[1];
    const password = details[2];

    const endpoint = `https://api.bitbadges.io/api/v0/claims/${
      this.isSimulation ? "simulate" : "complete"
    }/${claimId}/${this.address}`;

    const data = {
      _expectedVersion: -1, 
      [`${passwordPluginId}`]: {
        password: password,
      },
    };

    try {
      const response = await axios($, {
        method: "post",
        url: endpoint,
        headers: {
          "Content-Type": "application/json",
          Accept: "application/json",
          "x-api-key": `${this.bitbadges.$auth.api_key}`,
        },
        data,
      });

      const result = response;

      // Note: This means a successful trigger (add to queue), not a claim completion
      // You can use the claimAttemptId to poll
      return {
        success: true,
        claimAttemptId: result.claimAttemptId || "",
        currentTimestamp: Date.now()
      };
    } catch (error) {
      throw new Error(`Failed to complete claim: ${error.message}`);
    }
  },
});
```
