# Paginations

Throughout the API, we often use a bookmark technique. For the first request, you will not need to specify a bookmark, and it will fetch the first page. Within the response, it will return a **bookmark** and **hasMore**. **hasMore** defines whether there are more pages to be fetched. To fetch the next page, you will specify the returned bookmark from the previous request to the next request. This process can be repeated until all are loaded.&#x20;



This is all handled via the [PaginationInfo](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/PaginationInfo.html) type.
