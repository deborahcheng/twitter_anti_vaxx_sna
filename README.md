# Social Network Analysis (SNA) on Twitter Anti-Vaxx Tweets and User Engagement

## Introduction

### The Project

This is a project I did with my teammates in our Social Network Analysis course as part of the Master in Big Data program in IE School of Human Sciences and Technology. As it was an open topic project, we decided to explore the issue of anti-vaxx conversations on Twitter after I happened upon a podcast titled <a href="https://open.spotify.com/episode/2M1OghvpwPhTEzVtKEGUZL?si=Z0QTC_XLT1yQQ6To6wenfA">The Spread of Misinformation Online by Data Skeptic</a> on Spotify.

### The Topic and Objective

Anti-vaccination has been identified as one of the top 10 global health threats of 2019 and W.H.O. even had the first infodemiology conference in 2020 to address the overabundance of misinformation. 

The problem with not getting vaccinated is that it affects people around you. This is because there are people in our community who have health problems such as auto-immune diseases, and therefore cannot be vaccinated themselves, but rather rely on the herd immunity created by others being vaccinated.

Therefore, we believe that it is an important topic to discuss right now since the new COVID-19 vaccines are becoming available. The objective of this project was to identify the network of anti-vaxx conversation on Twitter between users and try to see if there are opportunities to limit the spread of misinformation and effectively push more accurate information to the Anti-Vax community.

## Technicals

This project is written in Python. The tweets and their user information were scraped using Twitter API v2. SNA metrics were calculated using the NetworkX library, the diffusion model was simulated using the ndlib library, machine learning with NLP and k-means were done using the NLTK and scikit-learn library, and the graphs were visualized with <a href="https://www.polinode.com/">Polinode</a>.

While Twitter API v1.1 was widely used and had readily available libraries, Twitter API v2 offers the ability to scrape by Conversation ID, which includes replies, quotes and retweets associated with the conversation. This makes scraping for engagements of engagements much easier. The json file returned from API v2 was also easier to work with and I wasn't restricted to the information filtered by existing libraries. Therefore, I decided to scrape using API v2 by defining my own functions as there was no libraries using API v2 at the time.

Since it was the first time I scraped with an API, there was much to learn and one of the biggest challenge was the request window. Some of the existing Twitter scraping libraries built-in the retrieval of request window information and will wait for the next request window to open when it's maxed out. However, since I couldn't figure out how to retrieve that information, I end up just adding a sleep between requests to avoid hitting the maximum in my request windows. Unfortunately, this slowed down the process for small scrapes, but since the majority of the requests made are for anywhere between 30 - 100 converstations, the sleeps between conversation requests were sufficient to solve the issue.

## Data

The data were scraped daily between Dec 18 - 24th, 2020 with the initial queries for the following hashtags that are some of most commonly used by the anti-vaxx community on Twitter according to <a href="https://www.cogitatiopress.com/mediaandcommunication/article/view/2847/2847">this paper</a>:
<ul>
  <li>#CDCwhistleblower</li>
  <li>#cdcfraud</li>
  <li>#vaccineinjury</li>
  <li>#NOVAX</li>
  <li>#Antivax</li>
  <li>#vaxxed</li>
</ul>

Subsequent queries for the replies, quotes and retweets related to the resulted conversations were then scraped along with the users who posted them.

## Challenge

Twitter, along with many other social media websites, had been aggressively deleting and banning posts and accounts associated with misinformation. During the course of our project, one of the biggest challenge was what I called the <b>vanishing tweets and user syndrome</b>, where Twitter were basically deleting and banning accounts as we were collecting the data.

For example, one of the biggest anti-vaxx bots we found at the beginning of the project had approximately 50k followers and tweets/retweets more than a dozen times a day was banned with all its tweets deleted just prior to our official data collection period. Also, @realDonaldTrump, who was tagged in many of the anti-vaxx tweets collected was also officially banned by Twitter a mere several weeks after our data collection period.

This resulted in the tweets dataset missing important information, making the tweets SNA graph incomplete. However, the majority of the information were present for the user engagement SNA dataset.

## Metrics and Analysis - User Engagement

The main analysis was done on the social network between users engagements in terms of tweets, replies, quotes and retweets. Due to the requirements of the project, we have calculated the following metrics in Python rather than using the graphing tool Polinode, although the application was fully capable of doing the same calculations:
<ul>
  <li>Diameter of Largest Connected Component - calculated with assumption of undirected graph due to the directed graph being weakly connected</li>
  <li>Average Shorted Path Length of Largest Connected Component - calculated with assumption of undirected graph due to the directed graph being weakly connected</li>
  <li>Reciprocity</li>
  <li>Degrees - including total, in-degree and out-degree</li>
  <li>Centrality - including degree centrality, eigenvector centrality, closeness centrality, and betweenness centrality</li>
  <li>Communities - calculated using multiple methods, including Louvain, Bipartition, Modularity, and Label Propagation</li>
  <li>K-Core</li>
</ul>

We found that this network was very weakly connected, with many nodes on the outer rim of the graph (shown below) being disconnected from the main component. While some of them were made up of smaller conversations, many users were also simply retweeting their own posts. This means that our objective of spreading factual information in this network would be very difficult if we were to simply rely, randomly, on a few influencers. Therefore, using the Voter Opinion Dynamics Diffusion model from the Python library ndlib, we simulated spreading information starting with approximately 10% "infected" nodes, but with different users as starting points:
<ul>
  <li>Random Nodes</li>
  <li>Users with Top Eigenvector Centrality</li>
  <li>Users with Top Betweenness Centrality</li>
  <li>Users with Top Degree Centrality</li>
  <li>Users with Top K-Core</li>
</ul>

The results differ depending on different starting points with random start performing worse. Therefore, for the objective of pushing factual information to the community, we should focus on users with high centrality values as calculated.

Please see Jupyter Notebooks and presentation pdf for analysis notes.

## Metrics and Analysis - Tweets

Since the main SNA analysis were not on the tweets themselves, but the user engagements, only some of the above metrics were calculated on the network between tweets. However, to understand what had been tweeted, we also used NLP along with k-means to determine different clusters of conversations within our dataset.

Please see Jupyter Notebooks and presentation pdf for analysis notes.

## Graphs

Below are the resulting visualization of the network after exporting the data to Polinode.

User Engagement Network - Anti-Vaxx Related Hashtags Conversations - Dec 18 - 24th, 2020
<img src="https://github.com/deborahcheng/twitter_anti_vaxx_sna/blob/main/graph/Tweets_User_v3.png?raw=true">

Tweets Network - Anti-Vaxx Related Hashtags Conversations (Incomplete) - Dec 18 - 24th, 2020
<img src="https://github.com/deborahcheng/twitter_anti_vaxx_sna/blob/main/graph/Tweets_v3.png?raw=true">

## Conclusion

We understand that there are many conversations and concerns regarding vaccines, many of which are valid concerns and arguments such as accountability and liability of pharmaceutical companies, individual rights, freedom of choice and how it affects the next generation. However, these arguments and discussions must be fueled by factual, scientific information rather than consipracy theories and/or misinformation.

With this project, we believe that understanding the social networks and how information spreads within these commnities, we can device a strategic way to limit the spread of misinformation and more effectively push and spread accurate scientifically backed factual information to the public, allowing individuals to make more sound decisions.

## Reference

<a href="https://open.spotify.com/episode/2M1OghvpwPhTEzVtKEGUZL?si=Z0QTC_XLT1yQQ6To6wenfA">The Spread of Misinformation Online by Data Skeptic</a><br>
<a href="https://www.cogitatiopress.com/mediaandcommunication/article/view/2847/2847">The Visual Vaccine Debate on Twitter: A Social Network Analysis</a><br>
<a href="https://www.who.int/teams/risk-communication/infodemic-management">W.H.O. Infodemic Management</a>

## Credits

While the codes in this project were written by myself, the presentation were a collaboration of the team (members listed in the presentation pdf), with much credit to Eun Suk (Jason) Hong.
