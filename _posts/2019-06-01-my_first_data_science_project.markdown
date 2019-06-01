---
layout: post
title:      "My First Data Science Project"
date:       2019-06-01 18:45:14 +0000
permalink:  my_first_data_science_project
---

![img](https://i.imgur.com/tz657Bi.jpg)

For most of my life, I wanted to be a doctor. From spending many hours shadowing physicians to completing my pre-med requirements at Emory University, I invested a lot of time preparing myself for a career in medicine. During my senior year, I discovered a deep passion for business and entrepreneurship and decided that my plan needed to change. 

As I began my journey to becoming a Data Scientist, I was ecstatic but admittedly intimated by the amount of things I didn’t (and still don’t!) know.  Embracing the mentality that always having something new to learn is more exciting than it is daunting has been crucial to my progress thus far. Coming from a business background, the burning question in my mind as I poured through a seemingly endless concepts in areas like Python and statistical analysis was “When will I learn to apply these concepts to a real problem?”

My first foray into such a project came in the form of an [OSEMN](https://medium.com/@randylaosat/life-of-data-data-science-is-osemn-f453e1febc10) investigation into a [dataset](https://www.kaggle.com/harlfoxem/housesalesprediction) of house sale prices and their features in King County.  The task was simple: determine how features from the dataset affect the sale price of a house in King County and use linear regression to create a model that predicts sale price of houses in King County.  While I won’t discuss the entirety of my thought process and results in this blog post, I wanted to share my thought process and how many factors can be introduced when attempting to answer a few seemingly simple questions using data.  

You can find my [project repository](https://github.com/pawoody/dsc-1-final-project-online-ds-ft-031119) here to view the project in its entirety, including steps such as evaluating multicollinearity, scaling features to normalize distribution, and the creation of the model itself.

Before writing any code, I reviewed the features of the dataset and did my best to internalize the needs of a hypothetical client that might have an interest in such a model - likely a company that renovates or buys and sells homes in King County.  With that client in mind, I set out to answer a few critical questions and build my model. A few of those questions included:   <br>

* When making an addition to a house, is it generally more cost effective to purchase additional land to increase lot size or renovate to increase living space?	
* Is there a particular geographical area that the client should prioritize?

I began the process of answering those questions and creating my model by loading and scrubbing the dataset before beginning exploration.  First, I visualized the distribution of house prices in King County:

![img](https://i.imgur.com/8VUV4DH.png)


The first things I noted based on the visualization above were the presence of a large amount of outliers for price and that the vast majority of houses in the dataset sell for under $1M. 

Next, I created a visualization to determine the correlation between house size/lot size and price in order to get a sense of how these features impact house sale price across the entirety of the ~22,000 houses in the dataset. 

![img](https://i.imgur.com/4xM2h14.png)


The visualization above suggested that the size of a house has a greater impact on sale price than lot size.  However, as seen above, our dataset contains high variance in lot sizes, with many near zero values and some tremendously large outliers.  With that in mind, I decided to segment the dataset into houses that had a lot size of at least 2000 sqft and no more than 15,246 sqft (0.35 acres) and re-visualize the correlations above using the remaining ~17,000 houses the remained in the data subset.

![img](https://i.imgur.com/x6XHX2p.png)


From this visualization, we can see that removing outliers had relatively little impact on the correlation between house size and price, while the correlation between lot size and price was reduced to ~0.  Logically, I knew that there was a correlation between lot size and sale price, but clearly there was another factor at play. This led me to seek answers to the the second critical question I posed above: does location impact the effect that lot size has on house price in each zip code?

![img](https://i.imgur.com/EQWXInI.png)


To investigate the effect of location on the correlation between lot size and house price, I first visualized the distribution in house price across each zipcode in our dataset.  What a mess!  To create a more useful visualization/analysis I proceeded to filter the dataset to show the distribution in price among the 20 zip codes with the greatest number of values in the dataset.

![img](https://i.imgur.com/EEivVra.png)


To add the greatest value for our client, I filtered the dataset to contain data from the 4 zipcodes with the greatest variance and upper range for price, as our client would likely see the greatest ROI through renovation in these areas.

Using these 4 zip codes as hues, I again visualized the correlation between house size/lot size and house sale price.

![img](https://i.imgur.com/3yabZ8p.png)


These results were exciting and gave credence to the classic real estate adage, “Location! Location! Location!”.  Reviewing our visualization above, zipcode (and thus, location) has a significant influence on the correlation between house/lot size and price. Based on these results, increasing house-size through renovation would likely have a greater influence on its sale price in zipcodes 98033 and 98006.  Using this information, our client could target additions to housing space for renovations in these zipcodes and likely see the greatest ROI.

When evaluated by latitude, longitude, and price, our “map” of King County House corroborates these findings.

![img](https://i.imgur.com/RDCwCdC.png)
 
![img](https://i.imgur.com/Lzx0Fxk.png)

Armed with this new intel, we’re able to advise our client to purchase homes in the 4 targeted zipcodes with the greatest variance and maximum in house sale price to maximize the upward mobility in price post-renovation and also whether an increase in house size or lot size (if purchasing additional land was a possibility) would result in greater ROI for our client. 

As I continue my journey into Data Science, I will continue to post new blog posts, updates, and projects here as well as to my GitHub. If you’re interested, you can find more of my work here: https://github.com/pawoody


