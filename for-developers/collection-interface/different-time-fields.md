# 🕒 Different Time Fields

There are different time fields within the interface with different purposes which could get confusing:

* **permittedTimes**: The times that a permission will **always** be able to be executed successfully
* **forbiddenTimes**: The times that a permission will **always** be forbidden from being executed
* **timelineTimes**: The times that some field is scheduled to be a specific value for a timeline-based field
  * Ex: X from timelineTimes 1 - 100 but changes to value Y from timelineTimes 100-200
* **transferTimes**: The times that a transfer transaction can occur
* **ownershipTimes**: The times that a user owns a badge from

Times are defined via [UNIX time](https://developer.mozilla.org/en-US/docs/Glossary/Unix\_time) or the number of milliseconds elapsed since the [epoch](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global\_Objects/Date#the\_epoch\_timestamps\_and\_invalid\_date), which is defined as the midnight at the beginning of January 1, 1970, UTC.

**Examples**

To provide a concrete understanding of the times, let's delve into a couple of illustrative scenarios.

#### Example 1: Presidential Election Badges

Imagine a scenario where users participate in a US presidential election by casting their votes through badge transfers. The timeline spans from the conclusion of voting, marked as T1, to the tenure of the elected president from T2 to T3. Here's how it might be structured:

* The president badge can be transferred after the voting conclusion period (T1 to T2) indicated by `transferTimes`.
* The individual who holds the president badge (the president) during the term (T2 to T3) is designated by `ownershipTimes`.

#### Example 2: Managing Collection Archival

Let's say we have a collection that can be optionally archived by the manager from T1 to T2 (permittedTimes) but is non-archivable at all other times (forbiddenTimes). From T1 to T2, they are permitted to update the archival status for any time.

Let's look at the before and after of the manager executing this permission for all times.

IMPORTANT: The permission corresponds to the **updatability** of the timeline. The timeline corresponds to the actual values. So, timelineTimes in the permission is which timeline times can be updated whereas timelineTimes in the actual timeline are the actual times for the values.

**Before:**

Permission:&#x20;

```
permittedTimes -> [{ start: T1, end: T2 }]
forbiddenTimes -> [ everything but T1 to T2}]
timelineTimes -> [{ start: 1, end: MAX_TIME }]
```

Archived Timeline

```
isArchived -> false for [{ start: 1, end: MAX_TIME }] (timelineTimes)
```

**After**

Now after they archive the collection for all times, the permission won't change but the archived timeline will.

Permission:&#x20;

```
permittedTimes -> [{ start: T1, end: T2 }]
forbiddenTimes -> [ everything but T1 to T2}]
timelineTimes -> [{ start: 1, end: MAX_TIME }]
```

Archived Timeline

```
isArchived -> true for [{ start: 1, end: MAX_TIME }] (timelineTimes)
```

**Example 3:**&#x20;

Now, let's continue the example from example 2. let's say they want to permanently lock the permission. This means the timeline is now forbidden to be updated for all timeline times and will remain forever as currently set.

Permission:&#x20;

```
permittedTimes -> []
forbiddenTimes -> [{ start: 1, end: MAX_TIME }]
timelineTimes -> [{ start: 1, end: MAX_TIME }]
```

Archived Timeline

```
isArchived -> true for [{ start: 1, end: MAX_TIME }] (timelineTimes)
```
