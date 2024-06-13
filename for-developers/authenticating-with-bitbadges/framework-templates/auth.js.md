# Auth.js

**Next Auth (Auth.js) Template**

```typescript
import NextAuth from 'next-auth';
import BitBadges from "next-auth/core/providers/bitbadges"

export const { handlers, signIn, signOut, auth } = NextAuth({
    providers: [BitBadges],
});

// Or for custom configurations
export const { handlers, signIn, signOut, auth } = NextAuth({
    providers: [BitBadges({ expectAttestationsPresentations: true, ownershipRequirements: { ... })]
});
```

This is a provider template integrated into Next Auth JS to help you get started.&#x20;

Starter Repository: [https://github.com/BitBadges/bitbadges-authjs-example](https://github.com/BitBadges/bitbadges-authjs-example)

You will need to configure your environment variables as such

```properties
BITBADGES_CLIENT_ID="a3e6ad11d9a971b2707d9147c80721919a1ba826c1087a294201ffcb03f62002"
BITBADGES_CLIENT_SECRET="020906c8eb7a96b5c8595208d08d8de21c22d5902a33635254cbb5ba12dfcb0a"
```

And, the redirect URI should be configured to match your callback handler.

```typescript
https://example.com/api/auth/callback/bitbadges
```

For the rest of the setup we refer you to the setup documentation.
