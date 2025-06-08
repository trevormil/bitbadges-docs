# QR Codes

If you do not specify a redirect URL, we will generate a QR code from the authorization code and prompt the user to store it in their BitBadges account under the "Authentication Codes" tab or export it to a more portable format like email, Apple Wallet, etc. The QR code flow is used for delayed authentication scenarios, most often in-person events. This can be exported to any portable format you wish so that users do not have to have wallets handy at authentication time. The text value of the code is the 32-byte code that you will exchange in the verification process.

**Generating URL**

The most important part of this step is to not include a **redirectUri** in the parameters. This lets us know that you want to store the details in the users' account as a QR, rather than being transmitted to the redirect URI. See the [Generating the URL](../authentication-url-+-parameters/generating-the-url.md) section for more details.

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1)   (3).png" alt="" width="539"><figcaption></figcaption></figure>
