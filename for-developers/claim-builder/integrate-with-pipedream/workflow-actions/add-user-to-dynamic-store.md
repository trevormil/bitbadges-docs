# Add User to Dynamic Store

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
      await axios($, {
        method: 'POST',
        url: `https://api.bitbadges.io/api/v0/bin-actions/add/${steps.trigger.event.dynamicDataId}/${steps.trigger.event.dataSecret}`,
        headers: {
          'Content-Type': 'application/json',
          'Accept': 'application/json',
          'x-api-key': this.bitbadges.$auth.api_key
        },
        data: {
          "address": steps.trigger.event.address,
          "id": steps.trigger.event.id,
          "email": steps.trigger.event.email,
          "username": steps.trigger.event.username
        }
      })

      return { success: true }
    } catch (error) {
      $.export('error', error)
      throw error
    }
  },
})
```
