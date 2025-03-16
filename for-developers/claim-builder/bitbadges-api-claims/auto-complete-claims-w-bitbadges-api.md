# Auto-Complete Claims w/ BitBadges API

Note: This is a more advanced option that is incompatible with in-site plugins. Before going through the entire process, consider whether you can implement your use case with a plugin-only approach.&#x20;

Typically, we recommend making a custom webhook / plugin over this. Get creative! Use existing plugins, Zapier, create custom plugins, etc as an alternative to needing a complete auto-complete implementation.

<figure><img src="../../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

### Auto-Completion

You can use the BitBadges SDK to auto-complete claims for users.

```typescript
const res = await BitBadgesApi.completeClaim(claimId, address, { ... });
console.log(res.claimAttemptId);

//Sleep 2 seconds to wait for it to be processed in the queue

const res = await BitBadgesApi.getClaimAttemptStatus(res.claimAttemptId);
console.log(res); // { success: true }
```

When creating on the BitBadges site, go to the API Code tab, and you should see code snippets customized to your claim.

<figure><img src="../../../.gitbook/assets/image (119).png" alt=""><figcaption></figcaption></figure>

Couple notes with auto-completing claims:

* If your claim is setup to require proof of sign in, proof of other socials sign ins (Sign In with Discord, etc), you must have the proper session authentication handled.
* Otherwise, you can setup your claim to be open to anyone but restricted by non-session criteria. For example, do not require proof of address but all claimees must present a valid password (potentially only known by you or the code which is expected to claim).

**Simulating**

You can also simulate the claim (which is instant and not put into the queue). There are also options within this request to simulate specific plugins only for further fine-grained testing (\_specificInstanceIds). The complete claim route automatically simulates and returns instantly if simulation fails. If simulation passes, it is put into the queue.

The body is the same as the completeClaim route. See below.

```typescript
const res = await BitBadgesApi.simulateClaim(claimId, address, { ...body });
```

**Custom Body**

Custom plugins may also require custom body inputs from the user. If you are using a custom plugin not created by you, refer to that plugin's documentation or contact the creator for more input on the custom body schema.

You will need to pass \_expectedVersion. This is the version number of the claim that you expect to complete. If there is a version mismatch at claim time, the claim will fail. This is to avoid instances where the claim creator maliciously changes criteria / actions without you knowing. You can specify -1 for don't check, but this is not recommended.

The custom body (if needed) should be in the following format

```typescript
{
    _expectedVersion: '0', //version of the claim (obtained from fetching the claim)
    [instanceId: string]: { ...pluginBody }
}
```

For example,

```typescript
{
    _expectedVersion: '0', //version of the claim 
    [`abc123`]: { //password plugin w/ instance ID = "abc123"
        password: "abc123"
    },
    [`codes1234`]: { //codes plugin w/ instance ID = "codes1234"
        code: "supersecretcode"
    },
}
```

**Customization**

The rest is left up to you. You decide when to trigger this code. It could be when a user signs in, you can auto-trigger based on certain criteria, or anything else you want.
