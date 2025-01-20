# QR Codes

If you do not specify a redirect URL, we will generate a QR code from the authorization code and prompt the user to store it in their BitBadges account under the "Authentication Codes" tab or export it to a more portable format like email, Apple Wallet, etc.

The QR code flow is used for delayed authentication scenarios, most often in-person events.

BitBadges offers various options for obtaining and presenting the code to you, such as scanning a QR code, emailing to self, or copying as text. There is also a pre-fetch all codes option for offline scenarios. We leave the implementation details to you. Once you receive the code (32 byte hex), this will be what you use in the verification process.

Provide your users instructions on how you expect them to present the code. If they are not expected to have their wallets handy at authentication time, it is best practice to not expect them to have access to their BitBadges account.

**Generating URL**

The most important part of this step is to not include a **redirectUri** in the parameters. This lets us know that you want to store the details in the users' account, rather than being transmitted to the redirect URI. See the [Generating the URL](../authentication-url-+-parameters/generating-the-url.md) section for more details.

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="539"><figcaption></figcaption></figure>
