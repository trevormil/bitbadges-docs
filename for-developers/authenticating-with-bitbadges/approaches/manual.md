# Manual

Manual redirection is suitable for scenarios where delayed authentication is implemented, and BitBadges caches details for you. This approach does require internet/API access during verification. Users directly navigate to the link provided and complete the authentication process by signing the challenge message.

BitBadges will cache the authentication details identified by a generated authorization code (32 byte hex string) in users' accounts under the "Authentication Codes" tab. At verification time, users present their code to you, typically via a QR code, and you can fetch the authentication details from the BitBadges API.

BitBadges offers various options for obtaining and presenting the code, such as QR codes, email, or copying as text. Consider providing users instructions on where to store the code. If they are not expected to have their crypto wallets handy at authentication time, do not expect them to have access to their BitBadges account. Options on the site include QR codes, emailing to self, copying as text, and more. For example, scanning the QR code will provide you with their authorization code.

**Generating URL**

The most important part of this step is to not include a **redirectUri** in the parameters. This lets us know that you want to store the details in the users' account, rather than being transmitted to the redirect URI. We will store the code in the user's BitBadges account via the Authentication Codes page.

{% content-ref url="../../sign-in-with-bitbadges/generating-the-url.md" %}
[generating-the-url.md](../../sign-in-with-bitbadges/generating-the-url.md)
{% endcontent-ref %}

\[generating-the-url.md]\(../../sign-in-with-bitbadges/generating-the-url.md

**Obtaining Codes**

The code will be stored in the users' BitBadges account under the Authentication Codes tab. That page will have different options for sharing and presenting like QR, exporting to email, Apple Wallet, etc.

Like we mentioned, obtaining the code is up to you. The code will be a 32 byte hex string that you need to get. Typically, this can be via you scanning the users' QR code (whose contents are the code itself).

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="539"><figcaption></figcaption></figure>
