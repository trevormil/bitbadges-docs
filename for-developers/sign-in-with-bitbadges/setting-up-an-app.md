# Setting Up an App

App registration is done at [https://btibadges.io/developer](https://btibadges.io/developer). All authentication requests must be for a specific app.

**Client IDs**

The client ID is a unique identifier assigned to your application when you register it with BitBadges. This identifier allows BitBadges to recognize your application and process authentication requests accordingly.

**Client Secrets**

The client secret is a cryptographic key provided by BitBadges upon registering your application. It is used alongside the client ID to authenticate requests made from your application to the BitBadges API.&#x20;

You must keep your client secret confidential and never expose it in client-side code or any place where it might be accessed by unauthorized users. Treat your client secret as securely as you would treat a password to ensure the security of your application's interactions with BitBadges.

In order to fetch the authentication details for any user of your application, you must prove that you know the client secret.

**Redirect URIs**&#x20;

Redirect URIs are specified endpoints in your application where users will be redirected after they have authenticated with BitBadges. These URIs must be pre-registered in your BitBadges application settings. It is critical to ensure these URIs are secure and exactly match the ones listed in your application settings to prevent redirect attacks.&#x20;

Always use HTTPS to protect the data integrity and confidentiality of the sensitive information exchanged during the redirect process.

For delayed / QR code authentication, redirect URIs are not used.
