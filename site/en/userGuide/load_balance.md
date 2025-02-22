---
id: load_balance.md
related_key: Load balance
summary: Learn how to balance query load in Milvus.
title: Balance Query Load
---

# Balance Query Load

This topic describes how to balance query load in Milvus.

Milvus supports automatic load balance by default. You can [configure](configure-docker.md) your Milvus to enable or disable [automatic load balance](configure_querycoord.md#queryCoordautoBalance). By specifying [`queryCoord.balanceIntervalSeconds`](configure_querycoord.md#queryCoordbalanceIntervalSeconds), [`queryCoord.overloadedMemoryThresholdPercentage`](configure_querycoord.md#queryCoordoverloadedMemoryThresholdPercentage), and [`queryCoord.memoryUsageMaxDifferencePercentage`](configure_querycoord.md#queryCoordmemoryUsageMaxDifferencePercentage), you can change the thresholds that trigger the automatic load balance.

If automatic load balance is disabled, you can still balance the load manually.

<div class="alert note">

When interacting with Milvus using Python code, you have the flexibility to choose between PyMilvus and MilvusClient (new). For more information, refer to <a href="https://milvus.io/api-reference/pymilvus/v2.3.x/About.md">Python SDK</a>.

</div>

## Check segment information

Get the `segmentID` of the sealed segment that you expect to transfer and the `nodeID` of the query node that you expect to transfer the segment to.

{{fragments/multiple_code.md}}

```python
from pymilvus import utility
utility.get_query_segment_info("book")
```

```go
// This function is under active development on the GO client.
```

```java
milvusClient.getQuerySegmentInfo(
  GetQuerySegmentInfoParam.newBuilder()
    .withCollectionName("book")
    .build()
);
```

```javascript
await getQuerySegmentInfo({
    collectionName: "book",
});
```

```shell
show query_segment -c book
```


<table class="language-python">
	<thead>
	<tr>
		<th>Parameter</th>
		<th>Description</th>
	</tr>
	</thead>
	<tbody>
	<tr>
		<td><code>collection_name</code></td>
		<td>Name of the collection to check the segment information.</td>
	</tr>
	</tbody>
</table>

<table class="language-javascript">
	<thead>
	<tr>
		<th>Parameter</th>
		<th>Description</th>
	</tr>
	</thead>
	<tbody>
	<tr>
		<td><code>collectionName</code></td>
		<td>Name of the collection to check the segment information.</td>
	</tr>
	</tbody>
</table>

<table class="language-java">
	<thead>
	<tr>
		<th>Parameter</th>
		<th>Description</th>
	</tr>
	</thead>
	<tbody>
	<tr>
		<td><code>CollectionName</code></td>
		<td>Name of the collection to check the segment information.</td>
	</tr>
	</tbody>
</table>

<table class="language-shell">
    <thead>
        <tr>
            <th>Option</th>
            <th>Description</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>-c</td>
            <td>Name of the collection to check the segment information.</td>
        </tr>
    </tbody>
</table>

## Transfer segment

Transfer the sealed segment(s) with the `segmentID` and the `nodeID` of the current query node and new query node(s).

{{fragments/multiple_code.md}}

```python
utility.load_balance(
  src_node_id=3, 
  dst_node_ids=[4], 
  sealed_segment_ids=[431067441441538050]
)
```

```go
// This function is under active development on the GO client.
```

```java
milvusClient.loadBalance(
  LoadBalanceParam.newBuilder()
    .withSourceNodeID(3L)
    .addDestinationNodeID(4L)
    .addSegmentID(431067441441538050L)
    .build()
);
```

```javascript
await loadBalance({
  src_nodeID: 3,
  dst_nodeIDs: [4],
  sealed_segmentIDs: [431067441441538050]
});
```

```shell
load_balance -s 3 -d 4 -ss 431067441441538050
```

<table class="language-python">
	<thead>
	<tr>
		<th>Parameter</th>
		<th>Description</th>
	</tr>
	</thead>
	<tbody>
	<tr>
		<td><code>src_node_id</code></td>
		<td>ID of the query node you want to transfer segment(s) from.</td>
	</tr>
	<tr>
		<td><code>dst_node_ids</code> (Optional)</td>
		<td>ID(s) of the query node(s) you want to transfer segment(s) to. Milvus transfers segment(s) to other query nodes automatically if this parameter is left blank.</td>
	</tr>
	<tr>
		<td><code>sealed_segment_ids</code> (Optional)</td>
		<td>ID(s) of the segment(s) you want to transfer. Milvus transfers all sealed segment(s) in the source query node to other query nodes automatically if this parameter is left blank.</td>
	</tr>
	</tbody>
</table>

<table class="language-javascript">
	<thead>
	<tr>
		<th>Parameter</th>
		<th>Description</th>
	</tr>
	</thead>
	<tbody>
	<tr>
		<td><code>src_nodeID</code></td>
		<td>ID of the query node you want to transfer segment(s) from.</td>
	</tr>
	<tr>
		<td><code>dst_nodeIDs</code> (Optional)</td>
		<td>ID(s) of the query node(s) you want to transfer segment(s) to. Milvus transfers segment(s) to other query nodes automatically if this parameter is left blank.</td>
	</tr>
	<tr>
		<td><code>sealed_segmentIDs</code> (Optional)</td>
		<td>ID(s) of the segment(s) you want to transfer. Milvus transfers all sealed segment(s) in the source query node to other query nodes automatically if this parameter is left blank.</td>
	</tr>
	</tbody>
</table>

<table class="language-java">
	<thead>
	<tr>
		<th>Parameter</th>
		<th>Description</th>
	</tr>
	</thead>
	<tbody>
	<tr>
		<td><code>SourceNodeID</code></td>
		<td>ID of the query node you want to transfer segment(s) from.</td>
	</tr>
	<tr>
		<td><code>DestinationNodeID</code> (Optional)</td>
		<td>ID(s) of the query node(s) you want to transfer segment(s) to. Milvus transfers segment(s) to other query nodes automatically if this parameter is left blank.</td>
	</tr>
	<tr>
		<td><code>SegmentID</code> (Optional)</td>
		<td>ID(s) of the segment(s) you want to transfer. Milvus transfers all sealed segment(s) in the source query node to other query nodes automatically if this parameter is left blank.</td>
	</tr>
	</tbody>
</table>

<table class="language-shell">
	<thead>
	<tr>
		<th>Option</th>
		<th>Description</th>
	</tr>
	</thead>
	<tbody>
	<tr>
		<td><code>-s</code></td>
		<td>ID of the query node you want to transfer segment(s) from.</td>
	</tr>
	<tr>
		<td><code>-d</code> (Multiple)</td>
		<td>ID(s) of the query node(s) you want to transfer segment(s) to.</td>
	</tr>
	<tr>
		<td><code>-ss</code> (Multiple)</td>
		<td>ID(s) of the segment(s) you want to transfer.</td>
	</tr>
	</tbody>
</table>

## What's next

- Learn more basic operations of Milvus:
  - [Insert data into Milvus](insert_data.md)
  - [Create a partition](create_partition.md)
  - [Build an index for vectors](build_index.md)
  - [Conduct a vector search](search.md)
  - [Conduct a hybrid search](hybridsearch.md)

