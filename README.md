# graph-clustering-study
# Graph-Based Clustering via Minimum Spanning Trees: Empirical Comparison with k-Means

## Project Overview

This project studies how a graph-based clustering method compares with a standard center-based clustering baseline. I implemented an MST-based clustering algorithm in Java and compared it against k-means on multiple synthetic 2D datasets. The goal was to examine how the two methods behave under different geometric structures, including non-convex shapes, concentric patterns, compact blob-like clusters, and noisy data.

## Methods

The core clustering method is based on the minimum spanning tree (MST) of the pairwise distance graph. Given a set of data points and a target number of clusters `k`, the algorithm first computes the pairwise distance matrix, then constructs a minimum spanning tree, removes the `k-1` heaviest edges, and finally identifies the remaining connected components as clusters.

As a baseline, I used `KMeans` from `scikit-learn`. While the MST-based method groups points based on graph connectivity and inter-point gaps, k-means groups points by assigning them to the nearest cluster center. This makes the comparison useful for studying the difference between structure-preserving clustering and center-based partitioning.

The MST-based method was implemented from scratch in Java. Cluster labels were exported and then used in Python for visualization and silhouette-score evaluation. Python was also used to run k-means and generate synthetic datasets.

## Datasets

I evaluated the two methods on four synthetic datasets:

- `gen_moons`: two non-convex moon-shaped clusters
- `gen_circles`: concentric inner and outer rings
- `gen_blobs`: compact blob-like clusters
- `gen_noisy`: clustered data with additional noisy peripheral points

These datasets were chosen to test how the methods behave under different geometric and noise conditions.

## Experiments and Findings

### `gen_moons`

On the moon-shaped dataset, the MST-based method consistently preserved the two non-convex moon structures. As `k` increased, it mainly separated a few loosely connected or isolated points into additional small clusters. In contrast, k-means tended to split the dataset into relatively uniform local segments, producing more and smaller center-based clusters as `k` increased.

For the representative case `k = 2`:

- MST silhouette score: `0.3238`
- KMeans silhouette score: `0.4855`

Although k-means achieved a higher silhouette score, the MST-based result was more faithful to the global non-convex geometry of the dataset. This shows that silhouette score may favor compact local partitions even when they do not match the most natural large-scale structure.

### `gen_circles`

On the concentric-circle dataset, the MST-based method naturally identified the inner and outer rings. Even as `k` increased, it tended to preserve this global structure and then further split the outer ring into smaller segments. By contrast, k-means did not recover the concentric organization and instead partitioned the points into radial or sector-like regions.

For the representative case `k = 2`:

- MST silhouette score: `0.1092`
- KMeans silhouette score: `0.3553`

Again, k-means achieved a higher silhouette score because its clusters were more compact locally. However, it failed to capture the true concentric structure of the data, while the MST-based method preserved the more meaningful global organization.

### `gen_blobs`

On compact blob-like data, the behavior reversed. The MST-based method tended to keep almost all points in one dominant cluster and then separate only a few peripheral or weakly connected points into small clusters as `k` increased. This produced highly imbalanced and often uninformative partitions.

K-means, on the other hand, generated compact and relatively balanced clusters that matched the intuitive structure of blob-like data much better.

For the representative case `k = 4`:

- MST silhouette score: `-0.0069`
- KMeans silhouette score: `0.5011`

This result shows that center-based clustering is much better suited to compact blob-like datasets, both visually and quantitatively.

### `gen_noisy`

On noisy data, performance depended more strongly on `k`. The MST-based method tended to preserve a dominant main cluster while separating loosely connected noisy points into additional small clusters. K-means behaved differently: for smaller `k`, it still mainly performed center-based spatial partitioning, while for larger `k`, it could allocate separate cluster centers to some peripheral noisy regions.

For `k = 5`:

- MST silhouette score: `0.4994`
- KMeans silhouette score: `0.4287`

For `k = 17`:

- MST silhouette score: `0.2290`
- KMeans silhouette score: `0.3495`

This suggests that on noisy data, relative performance is sensitive to the choice of `k`. At smaller `k`, separating weakly connected noise points can help the MST-based method. At larger `k`, k-means becomes more flexible because additional centers allow finer modeling of peripheral regions.

## Key Takeaways

This project shows that graph-based clustering and center-based clustering capture different notions of structure.

The MST-based method is more effective at preserving non-convex global structures such as moons and concentric circles. However, it can also produce highly imbalanced clusters, especially on blob-like or noisy datasets.

K-means performs strongly on compact center-based data and often achieves higher silhouette scores because it favors tight local clusters. However, on non-convex datasets, a higher silhouette score does not necessarily mean the clustering better reflects the true global geometry.

Overall, clustering quality should be judged using both quantitative metrics and qualitative structural interpretation, rather than relying on a single score alone.
