# 🕒 Different Time Types

There are different time fields within the interface with different purposes which could get confusing:

* **permittedTimes**: The times that a permission can be executed successfully
* **forbiddenTimes**: The times that a permission is forbidden from being executed
* **timelineTimes**: The times that some field is a specific value for a timeline
  * Ex: X from timelineTimes 1 - 100 but changes to value Y from timelineTimes 100-200
* **transferTimes**: The times that a transfer transaction can occur
* **ownedTimes**: The times that a user owns a badge from

Times are defined via [UNIX time](https://developer.mozilla.org/en-US/docs/Glossary/Unix\_time) or the number of milliseconds elapsed since the [epoch](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global\_Objects/Date#the\_epoch\_timestamps\_and\_invalid\_date), which is defined as the midnight at the beginning of January 1, 1970, UTC.

**Examples**

Example 1: Let's say we have a presidential election in the US where users can vote to transfer the president badge. Lets call the time voting concludes T1 and the time the president is in charge T2 to T3. We might say that the president badge can be transferred during the time period T1 to T2 (transferTimes) for the president to own it from T2 to T3 (ownedTimes).



Example 2: Let's say we have a collection that can be optionally archived by the manager from T1 to T2 (permittedTimes) but is non-archivable at all other times (forbiddenTimes).

If the manager does archive it from T1 to T2, the isArchivedTimeline would be true from T1 to T2 and false from T2 to T3 (timelineTimes). If they do not, it would be true from T1 to T3 (timelineTimes).

