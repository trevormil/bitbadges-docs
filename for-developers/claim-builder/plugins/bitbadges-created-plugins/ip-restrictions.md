# IP Restrictions

### Plugin ID: `ip`

This plugin rezstricts how often an IP address can claim.

### Public Parameters

* **maxUsesPerIp:** Max times a specific IP can claim

### Private Parameters

* **None**

### State

State is maintained by incrementing the uses by 1 **every** claim.

```
{
  ipsUsed: {
    "127.0.0.1": 10 // claimed 10 times
  }
}
```

#### Default State

The default state of the plugin is defined as follows:

```
{
  ipsUsed: {}
}
```

**Public State**

None
