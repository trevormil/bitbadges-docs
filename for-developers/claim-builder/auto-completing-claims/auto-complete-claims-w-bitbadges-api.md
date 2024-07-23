# Auto-Complete Claims w/ BitBadges API

### Do you really need to auto-complete?

Before going through the entire process, consider whether you can implement your use case with a plugin-only approach. Get creative!&#x20;

Use plugins like Codes, Password, or Email which are generic and can be used with pretty much any app. For example, give out claim codes to those who meet the criteria on your end. Then, they can claim in the BitBadges site, and you do not need to implement the claiming logic.

Or, explore if Zapier can help you implement it with no-code! You may also even consider using a wrapped approach with Zapier Webhooks. Instead of dealing with all the API logic, you use the simpler Zapier approach and trigger the webhook.

### Auto-Completion

You can use the BitBadges SDK to auto-complete claims for users.

```typescript
const res = await BitBadgesApi.completeClaim(claimId, address, { ...body });
console.log(res.claimAttemptId);

//Sleep 2 seconds to wait for it to be processed in the queue

const res = await BitBadgesApi.getClaimAttemptStatus(res.claimAttemptId);
console.log(res); // { success: true }
```

When creating on the BitBadges site, go to the API Code tab, and you should see code snippets customized to your claim.

<figure><img src="../../../.gitbook/assets/image (119).png" alt=""><figcaption></figcaption></figure>

Couple notes with auto-completing claims:

* Claims have different "actions". For on-chain badges, a successful claim will reserve the ability to claim in the future. For off-chain badges and address lists, there is no reserve step, the badge transfers / list append is instant.
* If your claim is setup to require proof of address, proof of other socials sign ins (Sign In with Discord, etc), you must have the proper session authentication handled.
* Otherwise, you can setup your claim to be open to anyone but restricted by non-session criteria. For example, do not require proof of address but all claimees must present a valid password (potentially only known by you or the one who is expected to claim).
* Consider sending a BitBadges claim alert after a claim if extra input is required from the user (e.g. code is reserved but needs an on-chain transaction to receive badges). If badges are automatically sent or addresses are automatically added, this will show up in their activity automatically.

**Simulating**

You can also simulate the claim (which is instant and not put into the queue). There are also options within this request to simulate specific plugins only for further fine-grained testing.&#x20;

The complete claim route automatically simulates and returns instantly if simulation fails. If simulation passes, it is put into the queue.

```typescript
const res = await BitBadgesApi.simulateClaim(claimId, address, { ...body });
```

**Custom Body**

Only two core plugins currently require a custom body input (the "codes" and "password" plugin). Custom plugins may also require custom body inputs from the user. If you are using a custom plugin not created by you, refer to that plugin's documentation or contact the creator for more input on the custom body schema.

The custom body (if needed) should be in the following format

```typescript
{
    [instanceId: string]: { ...pluginBody }
}
```

For example,

```typescript
{
    [`abc123`]: { //password instance ID = "abc123"
        password: "abc123"
    },
    [`codes1234`]: { //codes instance ID = "codes1234"
        code: "supersecretcode"
    },
    custom: {
        customInput: "xyz",
    }
}
```

**Customization**

The rest is left up to you. You decide when to trigger this code. It could be when a user signs in, you can auto-trigger based on certain criteria, or anything else you want.
