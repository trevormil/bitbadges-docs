# Universal Approaches

BitBadges is designed to be as generic and universal to allow you to implement your use case with as little code as possible. Before going through the entire process, consider whether you can implement your use case with a plugin-only approach. Get creative!&#x20;

Use plugins like Codes, Password, or Email which are generic and can be used with pretty much any app. For example, give out claim codes to those who meet the criteria on your end. Then, they can claim in the BitBadges site, and you do not need to implement the claiming logic.

#### Codes / Passwords: Example

1. Create a claim with the Codes or Password plugin.
2. Check everything you need on your end. If the user is eligible, give them the corresponding information (e.g. unique claim code).
3. Direct them to BitBadges to claim all in-site. You can auto-complete the parameters in the form for them by specifying the code / password in the route URL.

```typescriptreact
https://bitbadges.io/collections/ADD_COLLECTION_ID_HERE?claimId=CLAIM_ID&code=${code}
```

For codes, you may find the following snippet helpful. Obtain the seed code from the codes plugin modal on the BitBadges site.

```typescript
import CryptoJS from 'crypto-js';

const { SHA256 } = CryptoJS;
export const generateCodesFromSeed = (seedCode: string, numCodes: number): string[] => {
  let currCode = seedCode;
  const codes = [];
  for (let i = 0; i < numCodes; i++) {
    currCode = SHA256(currCode + seedCode).toString();
    codes.push(currCode);
  }
  return codes;
};
```

See the Claim Distribution tab on the quickstarter for a reference.
