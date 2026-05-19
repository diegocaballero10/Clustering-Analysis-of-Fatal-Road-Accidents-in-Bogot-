Clustering Analysis of Fatal Road Accidents in Bogotá
Second project for the Machine Learning Techniques course at Pontificia Universidad Javeriana (Bogotá, 2024-3). The project applies unsupervised learning techniques to historical road accident records in Bogotá in order to identify zones of the city with similar accident patterns, focusing on fatal incidents.
Table of contents

Overview
Research question
Dataset
Methodology
Models
Results
Technologies
Installation and usage
Repository structure
Conclusions
Authors

Overview
Road safety is a critical public concern in large cities. By analyzing historical traffic accident data, we can detect spatial and behavioral patterns that help authorities design more effective preventive measures. This project applies three clustering approaches to characterize zones of Bogotá that share similar fatal-accident patterns.
Research question

Which zones of the city show similar accident patterns in terms of fatalities caused by road accidents?

Dataset
Source: historico_siniestros_bogota_d.c_-.csv — historical record of traffic incidents in Bogotá D.C.
After filtering for incidents classified as CON MUERTOS (fatal accidents), the working dataset contains:

3,239 records
16 columns (mostly numerical)

Missing values detected during exploration:
ColumnNull valuesLOCALIDAD46CIV1,701PK_CALZADA37,974
Methodology
Preprocessing

Filter records where GRAVEDAD == 'CON MUERTOS'.
Select the most relevant features: LATITUD, LONGITUD, CLASE_ACC, LOCALIDAD.
One-hot encode the categorical variables CLASE_ACC and LOCALIDAD using pd.get_dummies.
Standardize numerical variables (LATITUD, LONGITUD) with StandardScaler to prevent features with larger ranges from dominating distance-based algorithms.

Models
1. K-Means

Optimal number of clusters selected via the elbow method: K = 3.
Final centroids visualized in a 2D PCA projection.

Evaluation metrics:
MetricValueInterpretationInertia (WCSS)77,471.64Reasonably compact clustersSilhouette score0.114Acceptable but not strong separationCalinski-Harabasz208.46K = 3 outperformed K = 2 and K = 4
2. Hierarchical Clustering

Linkage method: Ward (minimizes within-cluster variance, chosen after comparison with other linkage methods).
Distance metric: Euclidean.
Dendrogram analysis and elbow plot on linkage distances led to a cut-off distance of 85, producing 5 clusters.
Most points show positive silhouette coefficients, indicating overall reasonable cluster assignments, although some clusters are denser than others.

3. Hybrid model: PCA + DBSCAN with t-SNE / UMAP visualization
A pipeline that integrates dimensionality reduction, density-based clustering, and non-linear visualizations:

PCA for dimensionality reduction. Variance thresholds analyzed:

85% variance → 19 components
90% variance → 21 components
95% variance → 22 components


DBSCAN with parameter optimization over min_samples and eps (using the k-distance method).
t-SNE and UMAP for visualizing the resulting clusters in 2D.
Cluster quality measured with the silhouette score.

Best DBSCAN configuration:

min_samples = 3
62 clusters identified
28 noise points
Silhouette score: 0.8859 (strong cohesion and separation)

Results
ModelClustersSilhouetteNotesK-Means30.114Cluster 1 dominated by Usaquén; pedestrian collisions concentrated in cluster 2; vehicle crashes in cluster 3Hierarchical (Ward)5~0.15 (avg)More granular partitioning; some clusters compact, others dispersedPCA + DBSCAN620.886Strongest separation, captures fine-grained local patterns
Key qualitative findings:

K-Means cluster 0 is composed almost exclusively of Usaquén, with a mix of pedestrian collisions, vehicle crashes, and rollovers — suggesting unique accident dynamics in that locality.
K-Means cluster 1 is led by Kennedy, Engativá, Ciudad Bolívar, Suba, and Puente Aranda. Pedestrian collisions (ATROPELLO) dominate this group.
K-Means cluster 2 also concentrates in Kennedy, Engativá, Suba, Fontibón, Bosa, and Ciudad Bolívar but is dominated by vehicle crashes (CHOQUE), suggesting different infrastructural or behavioral factors.
The hybrid PCA + DBSCAN model uncovered 62 micro-zones with very similar accident profiles, which is more useful for targeted, location-specific interventions than the broader K-Means partition.

Technologies

Python 3
pandas, NumPy
scikit-learn (KMeans, DBSCAN, PCA, StandardScaler, silhouette, Calinski-Harabasz)
SciPy (linkage, dendrogram, fcluster for hierarchical clustering)
umap-learn (UMAP projection)
Matplotlib, Seaborn (visualizations)
Jupyter Notebook


Conclusions
The three clustering approaches yielded complementary insights into the structure of fatal road accidents in Bogotá:

K-Means provided a high-level partition of the city into three broad accident regimes, useful for general policy decisions.
Hierarchical clustering revealed a more nuanced structure with five groups, supported by clear dendrogram cut-offs.
The hybrid PCA + DBSCAN model produced the most granular and best-separated grouping (62 clusters, silhouette = 0.886), suitable for identifying micro-zones where targeted preventive measures could have the most impact.

Latitude, longitude, accident type, and locality were sufficient to expose meaningful patterns, but incorporating additional variables — such as road infrastructure, time of day, weather, or driver behavior — could substantially improve the characterization of high-risk zones in future work.
This project illustrates how unsupervised learning, combined with appropriate dimensionality reduction and visualization techniques, can support data-driven decisions for urban road safety.
Authors

Diego Caballero Sarmiento
Liseth Lozano
Aura Atuesta
