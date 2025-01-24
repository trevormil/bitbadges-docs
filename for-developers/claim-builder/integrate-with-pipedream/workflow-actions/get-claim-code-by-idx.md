# Get Claim Code by Idx

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
      const codeIdx = this.sub_by_one 
        ? steps.trigger.event.code_idx - 1 
        : steps.trigger.event.code_idx;

      const response = await axios($, {
        method: 'POST',
        url: 'https://api.bitbadges.io/api/v0/codes',
        headers: {
          'Content-Type': 'application/json',
          'Accept': 'application/json',
          'x-api-key': this.bitbadges.$auth.api_key
        },
        data: {
          'seedCode': steps.trigger.event.seed_code,
          'idx': codeIdx
        }
      })

      return response;
    } catch (error) {
      $.export('error', error)
      throw error
    }
  },
})
```
