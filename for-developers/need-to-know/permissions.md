# ☑ Permissions

Each badge collection will define the privileges that the manager has. There are currently six permissions that can be set. Once disabled (i.e. set to false), the privilege is permanently locked.

* CanDelete - Can the manager delete the collection?
* CanUpdateBytes - Can the manager update the bytes field?
* CanManagerBeTransferred - Can the role of the manager be transferred?
* CanUpdateUris - Can the manager update the metadata URIs?
* CanCreateMoreBadges - Can the manager add badges to the collection?
* CanUpdateDisallowed - Can the manager update the transferability?

Permissions are packed within a struct as a uint on the blockchain in the order they appear above (CanUpdateDisallowed is the rightmost bit). Leading zeroes must be applied. So for example, to set all six permissions to true, you will set it to 0011 1111 in binary which is 63.

To convert between number format and a more human-readable struct format, use the permissions functions from the [BitBadges SDK](broken-reference).

```typescript
import { GetPermissions, GetPermissionNumberValue } from 'bitbadgesjs-utils';
```
