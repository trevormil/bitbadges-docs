# WordPress

Gate your WordPress site with BitBadges! Authenticate with SIgn In with BitBadges and check any claim criteria, allowing you to create multi-chain websites gated for anything.

{% @github-files/github-code-block url="https://github.com/BitBadges/bitbadges-wp-plugin" %}

<figure><img src="../../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

### Installation

1. Download the plugin files and upload them to your `/wp-content/plugins/` directory.  We also published the plugin in the directory.
2. Activate the plugin through the 'Plugins' menu in WordPress
3. Go to Settings > BitBadges SIWBB to configure the plugin

### Configuration

1. Create a new application (OAuth App) in the [BitBadges Developer Portal](https://bitbadges.io/developer)
2. Set your redirect URI to: `https://your-wordpress-site.com/wp-login.php?action=bitbadges-callback`
3. Copy your Client ID and Client Secret
4. Go to your WordPress admin panel > Settings > BitBadges SIWBB
5. Enter your Client ID and Client Secret
6. (Optional) Configure a claim ID to gate access and other additional settings
7. Save the settings

### Features

* Adds a "Sign in with BitBadges" button to the WordPress login form
* Creates WordPress users automatically when they first sign in with BitBadges
* Secure OAuth 2.0 implementation with state verification
* Simple admin interface for configuration
* Optional exclusive BitBadges authentication mode (disable normal WordPress login)
* Claim-gated access support
* Emergency admin access URL for exclusive mode
* Customizable claim visibility on authorization page

### Security

The plugin implements the following security measures:

* CSRF protection using state parameter
* Secure storage of client credentials
* WordPress nonce verification
* Input sanitization
* Proper error handling
* Secure admin access fallback
* Claim verification

### User Experience

* Clean, centered login button design
* Shortened wallet addresses for better readability
* Clear separation between traditional and BitBadges login
* Visible claim requirements before authentication
* Seamless first-time user setup

### Requirements

* WordPress 5.0 or higher
* PHP 7.0 or higher
* HTTPS enabled on your site (required for secure OAuth)

