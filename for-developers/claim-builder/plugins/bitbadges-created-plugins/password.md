# Password

### Plugin ID: `password`

This plugin restricts access based on a password that must be provided by the claimer.

### Public Parameters

-   **None**

### Private Parameters

-   **password**: The password required to successfully make a claim.

### Custom Body Type

The custom body type for the password plugin requires a `password` string parameter in the request:

```typescript
type CustomBodyType = {
    password: string;
};
```

This password will be validated against the password defined in the private parameters. The password must match exactly (case-sensitive).

### State

State is not maintained for this plugin as it relies solely on the provided password for validation.
