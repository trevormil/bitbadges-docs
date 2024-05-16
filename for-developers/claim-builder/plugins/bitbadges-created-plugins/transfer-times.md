# Transfer Times

This plugin restricts claims based on specified transfer times, allowing claims only within certain time ranges.

### Public Parameters

* **transferTimes**: An array of time ranges during which transfers are allowed, defined using the `UintRangeArray` type.

### Private Parameters

* **None**

### State

State is not maintained for this plugin as it relies solely on the provided transfer times for validation.
