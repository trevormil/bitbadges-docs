# Auto-Complete Claims w/ BitBadges API

You can use the BitBadges SDK to auto-complete claims for users.&#x20;

```typescript
const res = await BitBadgesApi.completeClaim(claimId, address, { ...body });
console.log(res);
```

An alternative to using the SDK is to automate the process with Zapier. We recommend reading this for more insights into the behind the scenes even if using the SDK.

{% content-ref url="automate-w-zapier/automatic-claim-tutorial.md" %}
[automatic-claim-tutorial.md](automate-w-zapier/automatic-claim-tutorial.md)
{% endcontent-ref %}

Couple notes with auto-completing claims:

-   Claims have different "actions". For on-chain badges, a successful claim will reserve the ability to claim in the future. For off-chain badges and address lists, there is no reserve step, the badge transfers / list append is instant.
-   If your claim is setup to require proof of address, proof of other socials sign ins (Sign In with Discord, etc), you must have the proper session authentication handled.
-   Otherwise, you can setup your claim to be open to anyone but restricted by non-session criteria. For example, do not require proof of address but all claimees must present a valid password (potentially only known by you or the one who is expected to claim).
-   Consider sending a BitBadges claim alert after a claim if extra input is required from the user (e.g. code is reserved but needs an on-chain transaction to receive badges). If badges are automatically sent or addresses are automatically added, this will show up in their activity automatically.&#x20;

**Custom Body**

Only two core plugins currently require a custom body input (the "codes" and "password" plugin). Custom plugins may also require custom body inputs from the user. If you are using a custom plugin not created by you, refer to that plugin's documentation or cocntact the creator for more input on the custom body schema.

The custom body (if needed) should be in the following format

```typescript
{
    prevCodesOnly: false, //If you want to check for reserved codes

    [pluginId: string]: { ...pluginBody }
}
```

For example,&#x20;

```typescript
{
    [`abc123`]: { //password plugin ID = "abc123"
        password: "abc123"
    },
    [`codes1234`]:: { //codes plugin ID = "codes1234"
        code: "supersecretcode"
    },
    customPluginId: {
        customInput: "xyz",
    }
}
```
