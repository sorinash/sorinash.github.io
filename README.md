<h1>Metal Community Visualization Dashboard</h1>

[This project](https://sorinash.github.io/) provides a means of visualizing the traits and geographic distributions of communities within a network of heavy metal bands as scraped from the Metal Archives. 

<h2>Background</h2>

The [Metal Archives](https://www.metal-archives.com/) is an actively-curated encyclopedia of heavy metal bands and artists, detailing their musical output, reviews of said musical output, and (most importantly for this project), information regarding their lineups, countries of origin, and chosen subgenres of metal. It was founded in 2002, and as of May 2026, catalogues information pertaining to over 190 thousand bands and over 900 thousand individual artists. Given that this information is so extensive, thorough, and (generally) consistently formatted, I elected to use it as a database for this project. 

<h2>Pipeline</h2>

The Metal Archives was scraped using the Selenium library in Python during December of 2025. An initial list of URLs to scrape was assembled by traversing the Archives' "Browse Bands - Alphabetically" menu, after which, each individual page was scraped directly. In order to comply with the Archives' rate limits, I only scraped 1 page every 3 seconds, necessitating approximately 1 uninterrupted week (or approximately 2 real-time weeks) of scraping. Due to ethical and budgetary concerns, I elected not to use IP rotation to speed up the process.

Upon completion of scraping, relevant information about individual bands, such as their subgenres, their lineups, and their nations of origin, were extracted using BeautifulSoup. 

<h2>Network Assembly, Community Detection, and Analysis</h2>

The NetworkX Python library was used to construct a graph of bands. Bands are used for nodes and shared artists are used for edges in this graph. If two bands share at least one artist, then an edge exists between those two bands' nodes. For instance, [Ronnie James Dio](https://www.metal-archives.com/artists/Ronnie_James_Dio/4282) performed both in [Rainbow](https://www.metal-archives.com/bands/Rainbow/108) and [Black Sabbath](https://www.metal-archives.com/bands/Black_Sabbath/99); as such, an edge connects the nodes for those two bands. 

I isolated the largest connected component (LCC) from this graph for the purposes of community detection. There are approximately 126 thousand bands in this graph, meaning that a little under two-thirds of all bands in the Metal Archives are part of a single network.

Community detection on the LCC was performed using the [Louvain Algorithm](https://en.wikipedia.org/wiki/Louvain_method) as provided by the NetworkX library. I elected to use this method due to its [relative speed and consistency of output](https://onlinelibrary.wiley.com/doi/10.1002/9781118601181.ch13). 

It is important to note that deterministic community detection is an [NP-hard problem](https://pmc.ncbi.nlm.nih.gov/articles/PMC10897298/) and that, as such, the Louvain Algorithm is stochastic in nature. To illustrate consistency (and lack thereof) of detected communities across runs of this algorithm, I performed community detection using 10 separate seeds, recorded community information across all runs, and assembled band-level, community-level, and global-level information in .csv files using Pandas. Visualizations were created using d3, predominately to create the dynamic content for the webpage.

For each community (as well as for the LCC as a whole), a graph detailing the subgenre makeup of the bands within is provided. Subgenres are broken down into 16 categories as provided by the Metal Archives, and are tallied by the detection of keywords within the bands' reported genre. Metal bands can often overlap between two or more genres (for instance, Finnish band [Nightwish](https://www.metal-archives.com/bands/Nightwish/39) is categorized as both symphonic and power metal), meaning that there will be more tallied genres in a community's graph than there are bands in said community. This decision was deliberately made in order to more thoroughly measure the subgenre diversity of bands in detected communities and the LCC as a whole. 

In addition to the subgenre tally of bands in a community, a choropleth map detailing the number of bands in each country and territory recorded in the LCC is provided. Territories of other countries that were recorded in the Metal Archives are displayed as separate from said countries; for instance, Gibraltar is shown as being distinct from England. This map was assembled using a combination of GeoPy (to gather the geoJSONs for this project) and Shapely (to extract territories from their original countries) in Python. Exact counts of bands in a nation within a given community can be read by hovering over the country. A list of bands in said nation can be accessed by clicking on it. 

Finally, for each community, the following information is detailed: 

<ol>
  <li>The community's size</li>
  <li>The band with the highest degree (ie, number of connections) in the community</li>
  <li>The degree of said band</li>
  <li>The average degree for bands within the community</li>
  <li>The number of end branches (ie, bands that are only connected to 1 other band) in the community</li>
  <li>The assortativity of the community. This scale runs from -1 to 1.</li>
  <li>The clustering coefficient of the community.</li>
  <li>The density of the community (ie, what proportion of the possible connections are actually present).</li></li>
</ol>

URLs to all relevant bands are provided for those who are curious. 

<h2>Findings</h2>

The primary finding of this dashboard is that, under most circumstances, communities within the global metal scene are defined by geographic boundaries. Most communities are concentrated in one or two countries. In the latter case, these communities are frequently concentrated within neighboring countries, such as Austria and Germany. Additionally, the Louvain algorithm has a [resolution limit](https://dharvi02mittal.medium.com/the-louvain-algorithm-a-powerful-tool-for-community-detection-in-large-networks-de4ac2091bc3), meaning that particularly small communities may be "bundled" into larger ones. Genre distributions appear to be more defined by national community, rather than the other way around. For instance, Norway has a rather [notorious black metal scene](https://en.wikipedia.org/wiki/Music_of_Norway#Black_metal), which is reflected in the data; Norwegian communities tend to skew more towards black metal as compared to other communities. The tendency of metal bands to associate by geography makes intuitive sense; after all, if one is looking to start a band, it's generally easier to rehearse with bandmates that one doesn't need to board a plane in order to see.  

Nations with a particularly large number of bands, such as the United States, may have multiple communities within them. What defines splits between these communities is less apparent than communities across national boundaries. In certain situations, these splits seem to be defined by genre distribution (for instance, community 28 in seed 1 shows a larger skew towards doom metal than other American communities). However, this is by no means consistent, and merits further investigation.

While communities are typically defined by geography in the global metal scene, there is a particularly fascinating exception to this rule. Across the bulk of runs of the Louvain Algorithm on this network (and all provided runs here), there is a single community that includes large numbers of bands from multiple, non-proximal nations (typically including the United States, Germany, and the United Kingdom). This international community contains some of the most successful bands in metal's history, including Iron Maiden and Metallica, suggesting that it may be defined less by geographic boundaries and more by the "superstar" bands present within it. 

Finally, of particular note is the insularity of certain nations' scenes. For instance, most seeds' representation of the Finnish metal scene show that it encompasses the vast majority of bands within it, with vanishingly few bands appearing outside of this nation's single community. Even highly successful Finnish bands such as Nightwish do not appear in the aforementioned "superstar" community. This particular observation suggests that the Finnish metal scene is not only [highly prolific on a per-capita basis](https://nordicperspective.com/facts/metal-bands-per-capita-world-map), but also that it is densely interconnected. 

<h2>Future Directions</h2>

This dashboard has provided the foundation for future research into the global metal scene. In particular, I hope to extract sub-national geographic data to observe if metal communities in a nation are concentrated within specific regions. This may also shine a further light on why larger nations have multiple communities within them. It would be interesting to see, for instance, if [Bay Area thrash metal](https://en.wikipedia.org/wiki/Bay_Area_thrash_metal) and the [Tampa Bay death metal](https://en.wikipedia.org/wiki/Florida_death_metal) scenes can be algorithmically detected. 

Running additional seeds and performing analysis across them would also provide some potentially useful insights. In particular, it would be interesting to see if national distributions are consistent across communities that have been detected by various seeds. 

<h2>Subject Matter Disclaimer</h2>

Given the transgressive subject matter of heavy metal, offensive terms may be present in the band names shown by the dashboard, as well as bands with offensive political or lyrical themes. This project is not an endorsement of the lyrics or music created by these bands, or the actions taken by certain individual artists.

<h2>AI Disclaimer</h2>

Generative AI was not used for any part of the scraping or analysis processes. Claude Code was used to debug the CSS for the dashboard webpage. 

In accordance with the requirements set out by the [Metal Archives' robots.txt](https://www.metal-archives.com/robots.txt), no data was used for the training or fine-tuning of any generative AI model. To best comply with their wishes, only data directly necessary for this dashboard is provided on this Github page. 
