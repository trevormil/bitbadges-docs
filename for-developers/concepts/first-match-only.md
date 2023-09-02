# 🥇 First-Match Only

## First-Match Design Pattern and Data Management

Across the interface, we consistently adopt a first-match only design pattern to enhance efficiency and effectiveness. This pattern prioritizes the initial match when encountering multiple duplicate options.&#x20;

For instance, consider the scenario of badge metadata management. Within an array, we store comprehensive metadata associated for each badge. The distinguishing factor here is the badge IDs, which serve as the criteria for establishing matches. In the process of searching for the appropriate Uniform Resource Identifier (URI) linked to a particular badge ID, we locate the array element where the corresponding badge ID is specified. Subsequently, we retrieve the URI associated with that badge ID. To illustrate this further:

```json
[
  { uri: uri1, badgeIds: [{ start: 1, end: 10 }] },
  { uri: uri2, badgeIds: [{ start: 11, end: 100 }] }
]
```

In this example, badge IDs 1 to 10 correspond to `uri1`, while badge IDs 11 to 100 correspond to `uri2`.

The essence of the "first-match only" principle lies in the resolution process when dealing with duplicates. If duplicates exist, we employ a linear scan of the array, starting from the first element. The focus is on capturing the first instance where the criteria match occurs. Subsequent matches are disregarded. As an illustration:

Suppose we have the following data:

```json
[
  { uri: uri1, badgeIds: [{ start: 1, end: 100 }] },
  { uri: uri2, badgeIds: [{ start: 11, end: 100 }] }
]
```

In this case, for badge IDs ranging from 1 to 100, `uri1` is the first-match, and `uri2` is consistently ignored.

In essence, our choice of the first-match only design pattern streamlines data processing and minimizes redundancy. This approach ensures efficient retrieval of pertinent information while eliminating unnecessary duplication.





