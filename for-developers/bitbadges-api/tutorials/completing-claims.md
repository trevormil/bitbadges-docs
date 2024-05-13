# Completing Claims

You can use the BitBadges SDK to auto-complete claims for users.&#x20;

```typescript
const res = await BitBadgesApi.checkAndCompleteClaim(claimId, address, { ...body });
console.log(res);
```

An alternative to using the SDK is to automate the process with Zapier. We recommend reading this for more insights into the behind the scenes even if using the SDK.

{% content-ref url="../../automate-w-zapier/automatic-claim-tutorial.md" %}
[automatic-claim-tutorial.md](../../automate-w-zapier/automatic-claim-tutorial.md)
{% endcontent-ref %}

Couple notes with auto-completing claims:

* Claims have different "actions". For on-chain badges, a successful claim will reserve the ability to claim in the future. For off-chain badges and address lists, there is no reserve step, the badge transfers / list append is instant.
* If your claim is setup to require proof of address, proof of other socials sign ins (Sign In with Discord, etc), you must have the proper session authentication handled.
* Otherwise, you can setup your claim to be open to anyone but restricted by non-session criteria. For example, do not require proof of address but all claimees must present a valid password (potentially only known by you or the one who is expected to claim).

**Custom Body**

Only two core plugins currently require a custom body input (the "codes" and "password" plugin). Custom plugins may also require custom body inputs from the user.

The custom body (if needed) should be in the following format

```typescript
{
    [pluginId: string]: { ...pluginBody }
}
```

For example,&#x20;

```typescript
{
    password: { //password plugin ID = "password"
        password: "abc123"
    },
    codes: { //codes plugin ID = "codes"
        code: "supersecretcode"
    },
    customPluginId: {
        customInput: "xyz",
    }
}
```

