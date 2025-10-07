# Supabase

{% @github-files/github-code-block url="https://github.com/BitBadges/bitbadges-supabase-demo" %}

This repository is a basic example for getting started with Supabase and Sign in with BitBadges. It is meant to be a starting point and is not production ready.

This example:

* Uses the Next.js x Supabase template
* Uses traditional Supabase authentication and once signed in, you can additionally Sign In with BitBadges. This effectively creates a username <-> address mapping that you can use.

<figure><img src="../../../../.gitbook/assets/image (226).png" alt=""><figcaption></figcaption></figure>

Anything else is left as an exercise for you.

* Gating the entire site rather than using traditional authentication
* Gating specific pages / implementing your utility
* Linking claims

Note: Supabase is flexible. You may choose to implement it via Express, Auth0, or another way as well. This is just a reference to help you get started.

**Setup**

1. Setup Supabase as explained via their documentation
2. Add your BitBadges API key, client ID, and client secret to your .env
3. Execute the SQL in supabase/migrations folder to setup the tables. This can be done via the SQL Editor in the dashboard.
