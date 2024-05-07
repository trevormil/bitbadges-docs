# Server-Side Verification

```typescript
const popupParams = {
    ...,
    verifyOptions: { ... },
    skipVerify: false,
    expectVerifySuccess: true
}
```



If implementing the callback approach, we by default attempt to verify the request and return the response to you using the provided **verifyOptions**. This can be turned off by **skipVerify** = true. This would make sense if you want to verify on your end (saves resources and is quicker) or are not expecting a verification result.

{% content-ref url="../verification.md" %}
[verification.md](../verification.md)
{% endcontent-ref %}



For a better user experience, we can also display if certain details are not valid if we know that the challenge is expected to pass at sign time. **expectVerifySuccess** lets us know whether or not the verification (according to verifyOptions) is expected to return success. If so, we are able to check and catch certain errors and warn the user before they sign rather than after. Some use cases, however, may not be expected to pass at sign time, such as pre-generating a QR code for a later time. This option defaults to false.
