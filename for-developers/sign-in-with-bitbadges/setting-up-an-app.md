# Setting Up an App

### App Registration

* Register at [https://btibadges.io/developer](https://btibadges.io/developer) -> OAuth Apps
* Take note of your client ID / secret

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Key Components

#### 1. Client ID

* Unique identifier for your app
* Assigned upon registration

#### 2. Client Secret

* Cryptographic key for API authentication
* Keep confidential; never expose in client-side code. Treat like a password.
* Required to fetch user authentication details

#### 3. Redirect URIs

* Endpoints where users are redirected after authentication
* Must be pre-registered and use HTTPS
* Not needed for delayed/QR code authentication. Only for immediate digital redirects.

### Security Notes

* Treat client secret as securely as a password
* Ensure exact match between registered and used redirect URIs (if applicable)
* Use HTTPS for all redirects for security reasons
