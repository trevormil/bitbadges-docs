# WordPress

Gate your WordPress site with BitBadges! Authenticate with SIgn In with BitBadges and check any claim criteria, allowing you to create multi-chain websites gated for anything.

{% @github-files/github-code-block url="https://github.com/BitBadges/bitbadges-wp-plugin" %}

<figure><img src="../../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

This plugin allows users to gate their WordPress site with BitBadges – claims, payments, NFT badges, and more!

== Description ==

This plugin allows users to gate their site with BitBadges - claims, payments, NFT badges, and more!

**Important Note:** This plugin ONLY handles authentication with BitBadges - it does not restrict access to your site by itself. For restricting access, we recommend using this plugin in conjunction with:

-   [Force Login](https://wordpress.org/plugins/wp-force-login/) - Forces users to log in to view your site
-   [Restrict Content](https://wordpress.org/plugins/restrict-content/) - Restrict content to logged-in users
-   [Members](https://wordpress.org/plugins/members/) - Advanced user roles and permissions
-   Or other plugins that restrict access to your site

== Installation ==

1. Download the plugin files and upload them to your `/wp-content/plugins/` directory
2. Activate the plugin through the 'Plugins' menu in WordPress
3. Go to Settings > BitBadges SIWBB to configure the plugin

== Configuration ==

1. Create a new application (OAuth App) in the [BitBadges Developer Portal](https://bitbadges.io/developer)
2. Set your redirect URI to: `https://your-wordpress-site.com/wp-login.php?action=bitbadges-callback`
3. Copy your Client ID and Client Secret
4. Go to your WordPress admin panel > Settings > BitBadges SIWBB
5. Enter your Client ID and Client Secret
6. (Optional) Configure a claim ID to gate access and other additional settings
7. Save the settings

== Features ==

-   Adds a "Sign in with BitBadges" button to the WordPress login form
-   Creates WordPress users automatically when they first sign in with BitBadges
-   Secure OAuth 2.0 implementation with state verification
-   Simple admin interface for configuration
-   Optional exclusive BitBadges authentication mode (disable normal WordPress login)
-   Claim-gated access support
-   Emergency admin access URL for exclusive mode
-   Customizable claim visibility on authorization page

== Security ==

-   CSRF protection using state parameter
-   Secure storage of client credentials
-   WordPress nonce verification
-   Input sanitization
-   Proper error handling
-   Secure admin access fallback
-   Claim verification

== User Experience ==

-   Clean, centered login button design
-   Shortened wallet addresses for better readability
-   Clear separation between traditional and BitBadges login
-   Visible claim requirements before authentication
-   Seamless first-time user setup

== Requirements ==

-   WordPress 5.0 or higher
-   PHP 7.0 or higher
-   HTTPS enabled on your site (required for secure OAuth)

== Support ==

For support or feature requests, please visit the [BitBadges website](https://bitbadges.io) or create an issue in the GitHub repository.

== License ==

This plugin is licensed under the GPL v2 or later.  
License URI: https://www.gnu.org/licenses/gpl-2.0.html

== Changelog ==

= 1.0.0 =

-   Initial release with basic OAuth functionality
-   Added exclusive authentication mode
-   Added claim-gating support
-   Added emergency admin access
-   Improved user display names
-   Enhanced UI/UX for login button
-   Added claim visibility options
