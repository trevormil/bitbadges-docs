# Timelines

As you may have noticed, many of the collection fields are timeline-based, meaning they can be scheduled to have different values at different times. Check out the [Timeline helpers](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/functions/getCurrentValuesForCollection.html) from bitbadgesjs-utils in the SDK.

**Examples:**

<pre class="language-typescript"><code class="lang-typescript"><strong>const manager = getCurrentValuesForCollection(collection).manager;
</strong></code></pre>

<pre class="language-typescript"><code class="lang-typescript"><strong>const manager = getValuesAtTimeForCollection(collection, Date.now() + 1000 * 60).manager;
</strong></code></pre>



You can also get all possible values for timelines as such. This will set all missing times with empty or default values.

```typescript
function getFullManagerTimeline(timeline: ManagerTimeline<bigint>[]): ManagerTimeline<bigint>[]
```

```typescript
function getFullCollectionMetadataTimeline(timeline: CollectionMetadataTimeline<bigint>[]): CollectionMetadataTimeline<bigint>[]
```

And so on for all other timelines.