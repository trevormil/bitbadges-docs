# In-Person Verification

Pre-Readings: Overview (prior page)



Sometimes, you may want to verify users own a badge with a QR code, rather than having them sign a message with a crypto wallet. For example, for in person events, it may be unrealistic to expect all users to have their crypto wallets handy to sign an authentication message. Instead, you can have users sign their authentication messages prior to authentication time, generate a QR code (or NFC or other method), and present that at authentication time instead.

The BitBadges site provides tools to generate and store QR codes for users. Below, we will walk you through the process of using these tools.

Overview of the execution flow:

1. Generate a custom URL with [https://bitbadges.io/auth/linkgen](https://bitbadges.io/auth/linkgen).&#x20;
2. Send your custom URL to your users wishing to authenticate. This URL will have them sign your authentication message, generate a QR code of their signature, and store it in their BitBadges account + elsewhere.
3. To verify QR codes (signatures), you need a (message, signature) pair. When you scan the QR code, you will get the signature. To get the message, you can either fetch from the BitBadges API or cache it via a callback when the user signs the message.
4. Lastly, you verify with Blockin and implement any additional customization logic. For simple use cases, you can perform steps 3 and 4 using [https://bitbadges.io/auth/verify](https://bitbadges.io/auth/verify) directly in your browser.

As always, everything is open source. These are just helper tools. Feel free to customize and add logic as needed. See the [Blockin Quickstart repository](https://app.gitbook.com/s/AwjdYgEsUkK9cCca5DiU/developer-docs/quick-start) for a starting point / reference.

### **Step 1:** Generate the Generation URL

To generate your custom URL, visit [https://bitbadges.io/auth/linkgen](https://bitbadges.io/auth/linkgen) or follow the instructions below.

The base of the generated helper tool URL is  [https://bitbadges.io/auth/codegen](https://bitbadges.io/auth/codegen). The following params should be passed into the URL query.

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

challengeParams are the Blockin parameters for the sign-in challenge. See here for the full type definition [https://blockin-labs.github.io/blockin/docs/interfaces/ChallengeParams.html](https://blockin-labs.github.io/blockin/docs/interfaces/ChallengeParams.html). These will make up the base message that is expected. Here, you include all details about the sign-in like expiration date, badges to be owned, etc.

If generateNonce and allowAddressSelect are true, we will handle the nonce generation and address selection on our end, respectively. Meaning, we override challengeParams.nonce, challengeParams.address, and challengeParams.chain.&#x20;

name, description, and image follow the base metadata format. These will be used for UI purposes and displaying everything nicely to the user.

requireCallback set to true means we will return the (message, signature) pair to the window that directed to the site (see Step 3).

### Step 2: User Generates QR Code

Send the URL to your users and have them navigate to it. The site will walk the user through signing and generating the QR code. The QR code is simply their signature. Once created, it will be stored in their BitBadges account's QR codes.

However, it is strongly recommended that they also store it elsewhere for easier presentation at authentication time. Options include screenshotting it, saving it to their device, adding to a manager like Apple Wallet, physically printing it, and more.

This is because you should not expect them to have their crypto wallets handy (which is required for signing in to BitBadges if signed out).

### Step 3 (Optional): Caching Signatures

You may want to cache the messages and signature pairs for a better experience at authentication time. Or, it may even be a requirement such as in an offline setting.&#x20;

To help with this, we allow you to open the generated URL via a popup window (window.opener) and receive a callback message once signed. The callback message includes { message, signature }, and you can cache as needed. To get the callback message, you need to have requireCallback = true.

**Open Popup + Receive Callback**

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
<button onClick={openChildWindow}>Open Popup</button>
```

**Fully Generated vs Allowing Customization**

As mentioned above, there are variables (generateNonce and allowAddressSelect) that can potentially alter the message to be signed.&#x20;

If you do not use these variables, you know the passed in message (challengeParams) will be exactly the message that is signed (i.e. returned in the callback). In this case, if you do not want to deal with the callback / popups, you can simply brute force all the messages (since you have them already because they were passed in) and check if a specific signature matches at authentication time.&#x20;

For messages that can be customized, you will need to use the callback to get the final message.

### Step 4: Authentication

Finally, it is authentication time, and you are ready to verify users. Users will present their generated QR codes to you. Scanning them will provide you with their signatures.&#x20;

However, to verify, you need the signature AND the message that was signed. Obtaining the messages can be done in two ways.

**Option 1**

If you completed Step 3, you should already have the messages cached.

**Option 2**

If not, you can fetch them in real-time via the BitBadges API **POST /api/v0/authCode.**

**Verifying the (message, signature) pair**

You should now have a (message, signature) pair that can be verified by Blockin. Blockin will verify that 1) signature is well-formed and 2) any assets / badges that need to be owned are actually owned.&#x20;

If you selected Option 2 from above (BitBadges API **POST /api/v0/authCode**), this route also returns if the pair is valid, well-formed, and any assets are owned via **blockinSuccess**. No additional steps for Blockin verification are required.

If you are performing offline verification or want a custom implementation, we refer you to the [Blockin documentation](https://app.gitbook.com/o/7VSYQvtb1QtdWFsEGoUn/s/AwjdYgEsUkK9cCca5DiU/) for verification of the (message, signature) pair. There are multiple options and design choices here (centralized, decentralized, roll your own, BitBadges API, offline, online, etc).&#x20;

**Verification Tools / Resources**

1. Use the [Blockin Quickstart repository](https://app.gitbook.com/s/AwjdYgEsUkK9cCca5DiU/developer-docs/quick-start) to see an implemented verification process.
2. Consider using [https://bitbadges.io/auth/verify](https://bitbadges.io/auth/verify) which is a verification helper tool for simple use cases. This tool allows you to scan, fetch, and verify the QR codes directly in your browser. It also uses local storage to keep track of sessions. Sessions are used to keep track of which codes have been used, which haven't, etc. Any additional custom logic will need to be implemented separately.

<figure><img src="../../.gitbook/assets/image (30).png" alt=""><figcaption></figcaption></figure>

**Additional Custom Logic**

Blockin verification has now been completed, but you still need to implement any other custom logic that you may need. Typically, you will have custom rules for who gets access such as one use only per nonce to prevent replay attacks or one use per address or verifying extra details in your private databases. This custom logic may also be physical such as stamping users' hands. We leave this step up to you.

Consider using the exported functions from Blockin to help you.

```typescript
import { ChallengeParams, constructChallengeObjectFromString, constructChallengeStringFromChallengeObject } from 'blockin';
const params: ChallengeParams<bigint> = constructChallengeObjectFromString(message, BigIntify);
```

