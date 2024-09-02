# Setting Up an App

### App Registration

* Register at [https://btibadges.io/developer](https://btibadges.io/developer) -> OAuth Apps
* Authentication requests must specify the app

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
* Not used for delayed/QR code authentication

### Security Notes

* Treat client secret as securely as a password
* Ensure exact match between registered and used redirect URIs (if applicable)
* Use HTTPS for all redirects for security reasons
