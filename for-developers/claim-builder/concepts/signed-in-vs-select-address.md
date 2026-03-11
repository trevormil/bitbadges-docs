# Signed In vs Select Address

To require the user to be signed in, select the Signed In to BitBadges plugin. If not selected, we allow any address to be manually entered without address verification or sign in requirements. However, disabling "Signed In with BitBadges" allows any user to claim on another's behalf. Make sure this is intended and all other criteria properly gates the claim.

By not including a sign-in requirement, this makes the user experience better (no signatures). This is also helpful on mobile or other places where users may not have access to their wallets. However, this can be mitigated with approved sign-ins or other approaches like embedded wallets, but those require prior setup.

**Auto-Completing Claims**

If you are planning to auto complete claims behind the scenes via the API, disable the sign-in requirement and gate the claim in another way (e.g. a password that only your backend knows).

<figure><img src="../../../.gitbook/assets/image (371).png" alt=""><figcaption></figcaption></figure>
