# Universal Approach - Claim Codes

In the following pages, we will explain how to further customize claims beyond what is natively available. However, we want to highlight that claim codes are a universal approach that can be used with any application / criteria. For example,

* Give codes to finishers of a race
* Give codes to attendees of an event
* Give codes to those who sign in to your website
* Distribute codes via email, SMS, etc
* And so on. You distribute according to your needs!

## **Codes Plugin**

Use the Codes plugin to set the codes for your claim. We recommend auto-generating them for sufficient randomness, but you may also custom create them.

**Custom Metadata**

Consider also setting custom metadata to let users know what the codes are for, how to get them, etc.

**Configuration Tools**

If you are building a tool / service, consider creating a [configuration tool](plugins/configuration-tools.md) to auto-configure the plugin for the claim creator. Or, have the user copy / paste them to your service.

An example configuration input would be:

```
{"pluginId":"codes", "publicParams": {"numCodes": 10}, "privateParams": {"seedCode": "abc123", "codes": []}}
```

<figure><img src="../../.gitbook/assets/image (9) (1).png" alt=""><figcaption></figcaption></figure>

## **Obtaining Codes**

Codes can be obtained by the claim creator by clicking on the Distribute or Codes button which will bring up a distribution modal with all the codes.

If you have a large number of codes, consider using the Copy Seed Code button under the Batch tab (only applicable to automatically generated codes) instead of copying all N codes and calculate them dynamically. See snippet below.

Using a configuration tool would take this step out for the claim creator.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Generate Codes from Seed Snippet**

Auto-generated codes are calculated from a seed code, rather than needing to store all N codes. Note indexes are zero-based (code #1 = idx 0).

```typescript
import CryptoJS from 'crypto-js';
const { SHA256 } = CryptoJS;
export const generateCodesFromSeed = (seedCode: string, numCodes: number): string[] => {
  let currCode = seedCode;
  const codes = [];
  for (let i = 0; i < numCodes; i++) {
    currCode = SHA256(`${seedCode}-${i}`).toString();
    codes.push(`${currCode}-${i}`);
  }
  return codes;
};
```

**Generate Codes from Seed API Endpoint**

Or, outsource the generation to our API route:

POST https://api.bitbadges.io/api/generate-code

The request body should be a JSON object with the following properties:

* `seedCode` (string): The seed used to generate the code.
* `idx` (number): A non-negative integer index.

Response: { "code": "generatedCode" }

## **Claim Links**

To make it even easier for users, you can redirect them to the claim page with the code in the URL parameters and have it auto-filled for the user in the form.

See more on the prior page for configuring claim links.

https://bitbadges.io/CLAIM\_PATH?claimId=YOUR\_CLAIM\_ID\&code=YOUR\_CODE

## **Save for Later Links**

You may also consider using a save for later link. See example below.

[https://bitbadges.io/saveforlater?value=38d3fe58dffea3f7a675b587bc9239e0360047b7482d245f6770deb589aef869](https://bitbadges.io/saveforlater?value=38d3fe58dffea3f7a675b587bc9239e0360047b7482d245f6770deb589aef869)
