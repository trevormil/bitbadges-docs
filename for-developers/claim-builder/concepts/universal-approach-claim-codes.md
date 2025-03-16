# Universal Approach - Claim Codes

We want to highlight that claim codes are a universal approach that can be used with any application / criteria. For example,

* Give codes to finishers of a race
* Give codes to attendees of an event
* Give codes to those who sign in to your website
* Distribute codes via email, SMS, etc
* And so on. You distribute according to your needs!

No need for a custom integration or to identify the user by an identifier. Simply identify them with a code.

Use the Codes plugin to set the codes for your claim. We recommend auto-generating them for sufficient randomness, but you may also custom create them.

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

## **Save for Later Links**

You may also consider using a save for later link. See example below.

[https://bitbadges.io/saveforlater?value=38d3fe58dffea3f7a675b587bc9239e0360047b7482d245f6770deb589aef869](https://bitbadges.io/saveforlater?value=38d3fe58dffea3f7a675b587bc9239e0360047b7482d245f6770deb589aef869)

## **Zapier**

The get code via idx from seedCode route is also available in Zapier opening up some cool possibilities like auto-distribution.

{% content-ref url="../automate-w-zapier/distribute-claim-information-tutorial/" %}
[distribute-claim-information-tutorial](../automate-w-zapier/distribute-claim-information-tutorial/)
{% endcontent-ref %}

