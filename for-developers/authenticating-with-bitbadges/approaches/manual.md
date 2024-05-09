# Manual

Manual redirection is suitable for scenarios where delayed authentication is implemented, and BitBadges caches details for you. This approach does not necessitate a custom frontend but does require internet/API access during verification. Users directly navigate to the link provided and complete the authentication process by signing the challenge message.

BitBadges stores authentication details identified by a generated authentication code (32 byte hex string) in users' accounts under the "Authentication Codes" tab. At verification time, users present their code to you, typically via a QR code. BitBadges offers various options for obtaining and presenting the code, such as QR codes, email, or copying as text.

Consider providing users instructions on where to store the code. If they are not expected to have their crypto wallets handy at authentication time, do not expect them to have access to their BitBadges account. Options on the site include QR codes, emailing to self, copying as text, and more. For example, scanning the QR code will provide you with their authentication code.

<figure><img src="../../../.gitbook/assets/image (1).png" alt="" width="539"><figcaption></figcaption></figure>

**Generating URL**

You can use [https://bitbadges.io/auth/linkgen](https://bitbadges.io/auth/linkgen) or the code below to generate the URL. The URL is then distributed to your users. via any communication method.

The most important part of this step is to set **storeInAccount** = true within the parameters. Otherwise, BItBadges will not cache the details, and it will return 404 when attempting to fetch at verification time. **storeInAccount** set to true means we will store the code in the user's BitBadges account via the Authentication Codes page.&#x20;

<pre class="language-typescript"><code class="lang-typescript">import { generateBitBadgesAuthUrl } from 'bitbadgesjs-sdk';

const popupParams = { 
    ...
    
    storeInAccount: true
} // See Authentication URL page

<strong>const authUrl = generateBitBadgesAuthUrl(popupParams);
</strong></code></pre>

Behind the scenes, this uses the BitBadgesApi.createAuthCode() route.

**Obtaining Code**

Like we mentioned, obtaining the code is up to you. The code will be a 32 byte hex string that you need to get. Typically, this can be via you scanning the users' QR code (whose contents are the code itself).&#x20;

The code will be stored in teh users' BitBadges account under the Authentication Codes tab. That page will have different options for sharing and presenting like QR, exporting to email,  Apple Wallet, etc.

**Fetching Code and Verifying**

You can then fetch the code and verify it using our API all with the getAuthCode route. The **id** is the code.&#x20;

Note that the response will already include a **verificationResponse** field with the verification using the provided **options**. This is to make the process more convenient (one call instead of two). When on the Verification page, you can then skip the step where you are getting the verificationResponse.

<pre class="language-typescript"><code class="lang-typescript"><strong>const options: VerifyChallengeOptions = { ... }
</strong><strong>const res = await BitBadgesApi.getAuthCode({ id, options }); //id is their code
</strong><strong>const blockinChallenge = res.blockin;
</strong><strong>const verificationResponse = blockinChallenge.verificationResponse
</strong><strong>
</strong>// Alternative syntax: 
// const blockinChallenge = await BlockinChallenge.FromAuthCodeId(api, { id, options });
<strong>// const verificationResposne = blockinChallenge.verificationResponse
</strong><strong>// ...
</strong><strong>
</strong><strong>//Handle your other checks
</strong></code></pre>
