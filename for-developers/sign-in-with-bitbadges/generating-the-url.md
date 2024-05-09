# Generating the URL

The base URL is [https://bitbadges.io/auth/codegen](https://bitbadges.io/auth/codegen), with parameters appended to it. For instance:

```vbnet
https://bitbadges.io/auth/codegen?name=Event&description=...
```

This URL structure adheres to the following interface:

* **Base URL**: [https://bitbadges.io/auth/codegen](https://bitbadges.io/auth/codegen)
* **Parameters**: Custom parameters specific to your implementation.

You can use [https://bitbadges.io/auth/linkgen](https://bitbadges.io/auth/linkgen) or the code below to generate the URL. The URL is to be distributed to your users via any communication method or directly in your frontend.

<pre class="language-typescript"><code class="lang-typescript"><strong>import { generateBitBadgesAuthUrl, CodeGenQueryParams } from 'bitbadgesjs-sdk';
</strong>
const popupParams: CodeGenQueryParams {
    ...
} // See Authentication URL page

<strong>const authUrl = generateBitBadgesAuthUrl(popupParams);
</strong></code></pre>

```typescript
export const generateBitBadgesAuthUrl = (params: CodeGenQueryParams) => {
  let url = `https://bitbadges.io/auth/codegen?`;
  for (const [key, value] of Object.entries(params)) {
    if (value) {
      if (typeof value === 'object') {
        const valueString = JSON.stringify(value);
        const encodedValue = encodeURIComponent(valueString);
        url = url.concat(`${key}=${encodedValue}&`);
      } else {
        url = url.concat(`${key}=${value}&`);
      }
    }
  }
  return url;
};

```

