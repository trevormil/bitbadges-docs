# Outsourced Verification

Pre-Readings: Overview Page



Verification of badges can also be completely outsourced to the BitBadges site and accompanying tools. Below, we will walk you through the process of using these tools.

Example Use Cases:

* In-Person QR Codes: Sometimes, you may want to verify users own a badge with a QR code, rather than having them sign a message with a crypto wallet. For example, for in person events, it may be unrealistic to expect all users to have their crypto wallets handy to sign an authentication message. Instead, you can have users sign their authentication messages prior to authentication time, generate a QR code (or NFC or other method), and present that at authentication time instead.
* Child Window: Similar to how websites outsource auth to a "Sign In with \_\_INSERT\_BIG\_TECH\_COMPANY\_\_ Here" popup implementation, the same thing can be done for Blockin / BitBadges.



Overview of the execution flow:

1. Generate a custom authentication message with [https://bitbadges.io/auth/linkgen](https://bitbadges.io/auth/linkgen). This will create a custom BitBadges URL that you give to users who wish to authenticate.
2. Send your custom URL to your users wishing to authenticate. This URL will have them sign your authentication message, generate an authentication code (i.e. their signature) which is to to be kept secret, and store it in their BitBadges account. It can also be optionally exported elsewhere such as a QR code, via copy text, or stored in the browser.&#x20;
3. To verify codes (signatures), you need to obtain the (message, signature) pair. We leave this step up to you and what works best for your users. Note that BitBadges account sign ins (needed to access the auth codes) require access to a wallet which may not be applicable for users in many use cases. The corresponding messages for each signature may already be pre-known, can be cached when the user signs the message if directed via callbacks if directed via a popup window, can be fetched from the BitBadges API, or provided manually.
   1. QR Codes: Scan a QR code in-person to get the signature
   2. Child Window Popups: Implement everything as a child window popup, similar to "Sign In With XYZ Company".
   3. Manual: Users can manually provide the signature and/or messages.
4. Once you have the (message, signature) pair, you need to verify it with Blockin. Blockin checks address ownership (signature is good) and verifies ownership of specified badges. Any additional logic should be implemented by you. You should also check the message is as expected (domain, address, nonces) and check codes with previously used codes to avoid replay attacks or unintended authentication.
   1. For simple use cases, you can perform steps 3 and 4 using [https://bitbadges.io/auth/verify](https://bitbadges.io/auth/verify) directly in your browser.

<figure><img src="../../.gitbook/assets/image (44).png" alt="" width="359"><figcaption></figcaption></figure>

As always, everything is open source. These are just helper tools. Feel free to customize and add logic as needed. See the [Blockin Quickstart repository](https://app.gitbook.com/s/AwjdYgEsUkK9cCca5DiU/developer-docs/quick-start) for a starting point / reference.

### **Step 1:** Generate the Generation URL

To generate your custom URL, visit [https://bitbadges.io/auth/linkgen](https://bitbadges.io/auth/linkgen) or follow the instructions below.&#x20;

The base of the generated helper tool URL is [https://bitbadges.io/auth/codegen](https://bitbadges.io/auth/codegen). The following params should be passed into the URL query.

```typescript
const {
    challengeParams, //Blockin ChallengeParams<string>
    name, //string
    description, //string
    image, //string 
    generateNonce, //boolean
    allowAddressSelect, //boolean
    requireCallback //boolean
} = router.query;
```

For example.&#x20;

```
https://bitbadges.io/auth/codegen?name=Event%20Verification&description=This%20will%20give%20you%20access%20to%20the%20requested%20event.&image=https://bitbadges-ipfs.infura-ipfs.io/ipfs/QmPfdaLWBUxH6ZrWmX1t7zf6zDiNdyZomafBqY5V5Lgwvj&challengeParams={"domain":"https://bitbadges.io","statement":"BitBadges%20uses%20Blockin%20to%20authenticate%20users.%20By%20signing%20in,%20you%20agree%20to%20our%20privacy%20policy%20and%20terms%20of%20service.","address":"0x0f741F4E00453c403151b5e7ca379B4Dc981D685","uri":"https://bitbadges.io","nonce":"9YClKQzMLtsXD52eT","expirationDate":"2023-12-01T16:06:32.205Z","resources":[],"assets":[]}
```

**challengeParams** are the Blockin parameters for the sign-in challenge. See here for the full type definition [https://blockin-labs.github.io/blockin/docs/interfaces/ChallengeParams.html](https://blockin-labs.github.io/blockin/docs/interfaces/ChallengeParams.html). These will make up the base message that is expected. Here, you include all details about the sign-in like expiration date, badges to be owned, etc.&#x20;

If **generateNonce** and **allowAddressSelect** are true, we will handle the nonce generation and address selection on our end, respectively. Meaning, we override challengeParams.nonce, challengeParams.address, and challengeParams.chain with a random nonce and the user's signed in address / chain, respectively. If false, we use exactly what is specified in **challengeParams**.

name, description, and image follow the base metadata format. These will be used for UI purposes and displaying everything nicely to the user. They are not included anywhere in the message.

requireCallback set to true means we will return the (message, signature) pair to the window that directed to the site (see Step 3).

### Step 2: User Signs and Generates Code

Send the custom generated URL to your users and have them navigate to it. The site will walk the user through signing and generating the code. The code is simply their signature. Once created, it will be stored in their BitBadges account's codes.&#x20;

You now have to obtain the (message, signature) pair from them somehow. This can be done by them manually presenting you with the code. Or, you can receive it via a popup window callback via **window.opener** as seen below. Consider the best option for your application's requirements.

**Manual**

If users are to provide the code manually (QR codes or another method), consider providing them instructions on where to store it (i.e. the expected format). If they are not expected to have their crypto wallets handy at authentication time, do not expect them to have access to their BitBadges account. Options on the site include QR codes, emailing to self, copying as text, storing in browser (for offline access), and more. For example, scanning the QR code will provide you with their authentication code (signature).

To verify, you need the (message, signature) pair, not just the signature. As mentioned above, there are variables (**generateNonce** and **allowAddressSelect**) that can potentially alter the message to be signed. If anything in the message can potentially be changed, note that you need to also be provided the custom message as well (or be given the info to produce the message). If nothing can be changed, you know the message will be exactly as passed in.

Manual: The message can also be copied by the user and provided. However, note that the code itself (such as the QR code or text code) only contains the signature. The message needs to be provided separately.

API: Given the signature, you can also fetch the message via the BitBadges API.

```
await BitBadgesApi.getAuthCode(...);
```

**Callbacks**

If you passed **requireCallback** = true to the URL params, we will send a callback message with the { mesage, signature } back to **window.opener** upon the message being signed by the user. This allows you (the parent window) to receive the pair without any user clicks.&#x20;

Once you receive the pair, you can proceed to directly authenticate the user, or you can cache it for later use. This depends on your use case.

The received **message** will be the challenge string that was signed.

The received **signature** will be the user's signature of the message.

```tsx
const handleChildWindowMessage = (event: MessageEvent) => {
  // Ensure the message comes from the child window
  if (event.source === window && event.origin === window.location.origin) {
    const dataFromChildWindow = event.data;

    // Check if the message contains the QR code data
    if (dataFromChildWindow.message) {

    }
    
    if (dataFromChildWindow.signature) {
      
    }
  }
};
 
const openChildWindow = () => {
  // Open the child window (popup) with the authentication site URL
  const childWindow = window.open('http://localhost:3000/auth/codes?challengeParams={%22domain%22:%22https://bitbadges.io%22,%22statement%22:%22BitBadges%20uses%20Blockin%20to%20authenticate%20users.%20By%20signing%20in,%20you%20agree%20to%20our%20privacy%20policy%20and%20terms%20of%20service.%22,%22address%22:%220x0f741F4E00453c403151b5e7ca379B4Dc981D685%22,%22uri%22:%22https://bitbadges.io%22,%22nonce%22:%229YClKQzMLtsXD52eT%22,%22expirationDate%22:%222023-12-01T16:06:32.205Z%22,%22resources%22:[],%22assets%22:[]}', '_blank', 'width=400,height=400');

  // You can further customize the child window as needed
  childWindow.focus();
};

// Add a listener to handle messages from the child window
useEffect(() => {
  window.addEventListener('message', handleChildWindowMessage);

  // Cleanup the listener when the component unmounts
  return () => {
    window.removeEventListener('message', handleChildWindowMessage);
  };
}, []);
```

```tsx
<button onClick={openChildWindow}>Sign In With BitBadges</button>
```

### Step 3: Authentication

Finally, it is authentication time, and you are ready to verify users. You should now have a (message, signature) pair that can be verified by Blockin. Blockin will verify that 1) message and signature is well-formed (which proves address ownership) and 2) any assets / badges that need to be owned are actually owned.&#x20;

**Pre-Checks**

Before going through the verification step, you should first check that the message is as expected and perform any pre-checks you might have.&#x20;

IMPORTANT NOTES

* Blockin verification checks what is defined in the message. If the message is manipulated, it will verify the manipulated message. You should always expect the worst case that the user may have manipulated the message.&#x20;
* Prevent against replay and man-in-the-middle attacks. The same message signed by the same address will ALWAYS produce the same signature. Thus, it is important for your authentication message to have some sort of randomness to produce unique signatures. We also recommend signatures being **one-time use only** because of this.&#x20;
  * For example, lets say two events use the same exact message for authentication. Bob authenticates at Event A. The provider at Event A now has Bob's signature and could theoretically authenticate as Bob at Event B.&#x20;

Common pre-checks include:

* Verify all message details are as expected (nonces, domains, statements, badge ownership requirements, etc).&#x20;
* Verify the signature has not been used yet.
* Any custom requirements like max one use per address? max one use per badge? This can all be checked now since you know the message details.
* Only want to allow specific whitelisted addresses? Check that here,

Consider using the exported functions from Blockin to help you.

```typescript
import { ChallengeParams, constructChallengeObjectFromString, constructChallengeStringFromChallengeObject } from 'blockin';
const params: ChallengeParams<bigint> = constructChallengeObjectFromString(message, BigIntify);
```

**Additional Custom Logic**

You will need to implement any other custom logic that you require. Typically, you will have custom rules for who gets access such as verifying extra details in your private databases. This custom logic may also be physical such as stamping users' hands. We leave this step up to you.

**Verification Option 1: BitBadges API**

If you fetched the message from the API as explained above, the getAuthCode route will also return **blockinSuccess** if the Blockin verify was successful.

```
await BitBadgesApi.getAuthCode(...);
```

You can also use the following route

```
await BitBadgesApi.genericVerify(...);
```

**Verification Option 2: Custom Implementation**

If you are performing offline verification or want a custom implementation, we refer you to the [Blockin documentation](https://app.gitbook.com/o/7VSYQvtb1QtdWFsEGoUn/s/AwjdYgEsUkK9cCca5DiU/) for verification of the (message, signature) pair. There are multiple options and design choices here (centralized, decentralized, roll your own, BitBadges API, offline, online, etc).&#x20;

Use the [Blockin Quickstart repository](https://app.gitbook.com/s/AwjdYgEsUkK9cCca5DiU/developer-docs/quick-start) to see an implemented verification process.

**Verification Option 3: Helper Tools**

Consider using [https://bitbadges.io/auth/verify](https://bitbadges.io/auth/verify) which is a verification helper tool for simple use cases. This tool allows you to scan, fetch, and verify the QR codes directly in your browser. It also uses local storage to keep track of sessions. Sessions are used to keep track of which codes have been used, which haven't, etc. Any additional custom logic will need to be implemented separately.

<figure><img src="../../.gitbook/assets/image (30).png" alt=""><figcaption></figcaption></figure>

