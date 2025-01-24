# Get Claim Attempt Status

```typescript
import { axios } from "@pipedream/platform"

//TODO: Edit where you get the props from. Static manually entered props are in via
//      the props field. Or you can configure to use steps.trigger.event.propName
//      for dynamic props from the trigger or other steps

export default defineComponent({
  props: {
    bitbadges: {
      type: "app",
      app: "bitbadges",
    }
  },
  async run({steps, $}) {
    try {
      const response = await axios($, {
        method: 'POST',
        url: `https://api.bitbadges.io/api/v0/claims/status/${steps.trigger.event.claimAttemptId}`,
        headers: {
          'Content-Type': 'application/json',
          'Accept': 'application/json',
          'x-api-key': this.bitbadges.$auth.api_key
        },
        data: {}
      })

      return {
        success: response.success,
        error: response.error
      }
    } catch (error) {
      $.export('error', error)
      throw error
    }
  },
})
```
