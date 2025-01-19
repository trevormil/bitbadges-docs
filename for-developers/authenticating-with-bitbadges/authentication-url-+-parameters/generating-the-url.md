# Generating the URL

The base URL is [https://bitbadges.io/siwbb/authorize](https://bitbadges.io/siwbb/authorize), with parameters appended to it.&#x20;

For instance:

```vbnet
https://bitbadges.io/siwbb/authorize?name=Event&description=...
```

This URL structure adheres to the following interface:

* **Base URL**: [https://bitbadges.io/siwbb/authorize](https://bitbadges.io/siwbb/authorize)
* **Parameters**: Custom parameters specific to your implementation.

You can use [https://bitbadges.io/auth/linkgen](https://bitbadges.io/auth/linkgen) or the code below to generate the URL or click Create SIWBB URL directly from your app in the developer portal (recommended).

<figure><img src="../../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>



The URL is to be distributed to your users via any communication method or directly in your frontend. The generated URL can be quite long, so you may consider using a URL shortener.

**Snippets**

<pre class="language-typescript"><code class="lang-typescript"><strong>import { generateBitBadgesAuthUrl, CodeGenQueryParams } from 'bitbadgesjs-sdk';
</strong>
const popupParams: CodeGenQueryParams {
    ...
} // See Authentication URL page

<strong>const authUrl = generateBitBadgesAuthUrl(popupParams);
</strong></code></pre>

```typescript
export const generateBitBadgesAuthUrl = (params: CodeGenQueryParams) => {
    let url = `https://bitbadges.io/siwbb/authorize?`;
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
