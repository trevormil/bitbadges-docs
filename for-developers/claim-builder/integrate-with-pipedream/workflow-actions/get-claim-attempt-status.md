# Get Claim Attempt Status

```typescript
import { axios } from "@pipedream/platform"

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
