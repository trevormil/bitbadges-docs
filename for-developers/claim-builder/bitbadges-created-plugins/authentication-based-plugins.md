# Socials / Email Plugins

**Plugin IDs: `twitter, github, discord, google, email, twitch, and other socials`**

These plugins restrict the number of times specific users from different social platforms can use the claim.&#x20;

**Usernames vs IDs**

We differentiate by account IDs (should be constant) and usernames for display purposes / ease of use.  For the email plugin, we use { id: bob@gmail.com, username: id@gmail.com }

```typescript
export const IdsVsUsernamesExplanationMap: Record<string, string> = {
  bluesky: 'Username: Handle format (e.g. alice.bsky.social)\nID: Decentralized identifier (e.g. did:plc:abcdef123...)',

  discord: 'Username: Current format is just the username (e.g. discord_user)\nID: Snowflake format - 17-20 digit number (e.g. 123456789012345678)',

  twitter: 'Username: @ handle without the @ symbol (e.g. elonmusk)\nID: Numerical string, typically 7-20 digits (e.g. 44196397)',

  github: 'Username: Case-insensitive alphanumeric with hyphens (e.g. octocat)\nID: Numerical identifier (e.g. 583231)',

  google: 'Username: Email address (e.g. user@gmail.com)\nID: Sub identifier from OAuth - alphanumeric string (e.g. 110248495415936123456)',

  facebook: 'Username: Custom username in URL (e.g. zuck)\nID: Numerical string, typically 15+ digits (e.g. 4)',

  reddit: 'Username: u/ prefix without the u/ (e.g. spez)\nID: Base36 string (e.g. 13h2kf)',
  
  twitch: 'Username: Lowercase alphanumeric (e.g. ninja)\nID: Numerical user ID (e.g. 19571641)',

  telegram: 'Username: Optional @ handle without @ (e.g. durov)\nID: Numerical user ID, can be negative for groups (e.g. 12345)',

  farcaster: 'Username: Case-insensitive handle (e.g. dwr)\nID: Numerical FID (e.g. 2)',

  slack: 'Username: Lowercase with underscores (e.g. slack_user)\nID: Uppercase string starting with U (e.g. U0G9QF9C6)'
};
```

**Custom Claim Numbers**

We also support assigning specific claim numbers if assignMethod === instance ID. We will use the zero-based index in the list of \[...usernames, ...ids]. Note we take first match so duplicates here are not supported.

**Private Lists**

The user list is only allowed to be private. You may consider hosting the list on your end, so users know if they can claim or not.

### Public Parameters

* **maxUsesPerUser**: The maximum number of uses allowed per user.
* **blacklist:** Whether this should deny users on the IDs / usernames or not

### Private Parameters

If both are left blank, we do not check any whitelist restrictions.&#x20;

* **usernames**: An optional list of whitelisted usernames who are allowed to claim.
* **ids:** An optional list of whitelisted user IDs who are allowed to claim.

### State

State is maintained by tracking the number of claims each user makes. It keeps a record of user IDs and usernames.

Note that in order to maintain correctness, we replace all "." values with "\[dot]" to ensure the keysa re a single string. If micro-managing state through the API, you will need to do this.

```json
{
  "ids": {
    "user_id": 1
  },
  "usernames": {
    "username": "user_id"
  }
}
```

#### Default State

The default state of the plugin is defined as follows:

```json
{
  "ids": {},
  "usernames": {}
}
```

#### Public State

State is not made public to users.
