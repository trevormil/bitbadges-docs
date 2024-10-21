# Success Logic

Certain claims may make use of the **satsifyMethod** property to implement dynamic logic. This option is currently only available behind the scenes. Frontend defaults to all are required (satisfyMethod = undefined).

This works in the following ways:

* By default, if  **satisfyMethod** is falsy (undefined), ALL plugins are required in order for the overall claim to succeed.
* The "numUses" plugin or those critical to assigning claim numbers are required and cannot be optional
* Claimees are allowed to select which plugins they want to execute / be applied. Behind the scene this uses the **\_specificInstanceIds** property on the simulate / complete API claim request.
* For stateful plugins, we ONLY update the state for the plugins in the success path (we will short circuit where necessary).
  * For example, if there is a requirement group with 2 out of 8 plugins needing success and the first two pass, we will not even check or apply state for the other six.

```typescript
export interface iSatisfyMethod {
  type: 'AND' | 'OR' | 'NOT';
  /** Conditions can either be the instance ID of the plugin to check success for or another satisfyMethod object. */
  conditions: Array<string | iSatisfyMethod>;
  options?: {
    /** Only applicable to OR logic. Implements M of N logic. */
    minNumSatisfied?: number;
  };
}
```



<figure><img src="../../.gitbook/assets/image (152).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (151).png" alt=""><figcaption></figcaption></figure>

