# Signed In vs Select Address

All the accounting for claim attempts is address-based, but we do NOT require sign-ins by default. To require the user to be signed in, select the Signed In to BitBadges plugin. If not selected, we allow any address to be manually entered without address verification or sign in requirements. However, disabling "Signed In with BitBadges" allows any user to claim on another's behalf. Make sure this is intended and all other criteria properly gates the claim.

By not including a sign-in requirement, this makes the user experience better (no signatures). This is also helpful on mobile or other places where users may not have access to their wallets. However, this can be mitigated with approved sign-ins or other approaches like embedded wallets, but those require prior setup.

**Auto-Completing Claims**&#x20;

If you are planning to auto complete claims behind the scenes via the API or via Zapier, note that you have two approaches.

1. OAuth Sign In with BitBadges - The Signed In requirement will pass if you have "Complete Claims" scope
2. Disable + Gate In Another Way - For example, our Zapier flow does not check user sign in but gates with a password that only Zapier knows.

<figure><img src="../../../.gitbook/assets/image (198).png" alt=""><figcaption></figcaption></figure>
