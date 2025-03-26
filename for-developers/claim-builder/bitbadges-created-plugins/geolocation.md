# Geolocation

### Plugin ID:  geolocation

This plugin checks the location of a specific IP address. This should be treated as a "best effort".

### Public Parameters

```typescriptreact
https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2
```

* **allowedCountryCodes:** String array of country codes allowed. If blank, all are allowed by default.
* **disallowedCountryCodes**: String array of country codes allowed. If blank, none are disallowed by default.
* **pindrop:** The latitude, longitude, and radius of the pindrop location. Radius is in coordinates, so please convert first from miles or km or whatever unit you are using.

```typescript
const { latitude, longitude, radius } = pindrop;
```

### Private Parameters

* **None**

### State

State is not maintained for this plugin.
