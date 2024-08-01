# Generating Codes from Seed

All codes via the Codes plugin are generated from a seed code via the following snippet. On the distribute modal in-site, you can simply copy the seed code instead of copying all N codes and calculate them dynamically.

```typescript
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
