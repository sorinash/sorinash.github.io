demo.html provides the webpage for the dashboard.

This dashboard looks at communities of metal bands across the world, as scraped from the Metal Archives. Each band forms a node on the graph, and 1 or more shared members creates an edge.

For instance, Ronnie James Dio was in both Black Sabbath and Rainbow, so those two bands would have an edge between them.

Communities were taken from the largest connected component of the dataset, consisting of approximately 120 thousand bands, and were detected using the Louvain algorithm. Note that non-stochastic community detection is an NP-hard problem, so the Louvain algorithm is stochastic. Given the computational intensity of running this process, I elected to run the Louvain algorithm with 10 separate seeds and provide them here.

For each seed/run of the algorithm, the detected communities are provided in order from largest to smallest, along with a global view of all bands. For the global view of all bands, and for each community, the following information is provided.

1. A graph detailing the genre makeup of that group of bands.
2. The size of the community.
3. The band with the highest degree (number of connections to other bands) within that community.
4. The degree of that band.
5. The average degree of bands in the cluster.
6. The number of end branches (ie, bands that only connect to one other band)
7. The assortativity of the community (ie, the tendency of high-degree bands to associate with other high-degree bands), ranging from -1 to 1.
8. The clustering coefficient of the community (how closely connected the bands are to one another)
9. The density of the community (what proportion of possible connections between bands actually exist; expect this to be low).
10. A choropleth map detailing the number of bands per country in the given community; hovering over each country will give the number of bands in that country. Note that the scale changes based on the number of bands in the community; I'll include a color bar in future releases.
