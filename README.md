# League of Legends Data Mining Project


# Overview
The  final requirement to fulfill my desired certification level with EcZachly's Data Engineering Bootcamp is an engineered dataset from raw sources into a polished dataset using modern technologies. Despite that being the case of how this project started, the intent is to use this as a chance to perform data science on League of Legends. The focus of this project is to investigate the items in League of Legends, their performance, and metrics on what makes a good item and some assistance to discover interesting builds, both crowdsourced as it is today and certain unseen builds that might be worth a try.

 Indeed, [Kaggle does possess a wealth of datasets  from Riot's Match API or League of Legends (LoL) Esports matches](https://www.kaggle.com/search?q=league+of+legends+in%3Adatasets), but none have an ideal collation of matches with item statistics. Item builds have been a controversial debate since League's inception, but has picked up steam in recent years. Moreover, League has done another significant overhaul of the item system. Every week there are certain builds and "techs" discovered, from [purchasing multiple support items on the same team](https://www.reddit.com/r/summonerschool/comments/195i0v1/new_double_support_item_math/?rdt=44431) to trying out [auto attack manipulation on different champions with Titanic Hydra on ranged champions.](https://www.reddit.com/r/leagueoflegends/comments/1abowwy/new_tech_on_cait_taking_over_the_top_of_the/) 
 
 Even moreso, since certain Esports Leagues are playing on the live patch as opposed to traditionally being a week+ behind, it is even more critical to discover new strategies faster. While there are sites such as Blitz.gg that can recommend item builds, those are usually dependent on what occurred in the patch and recommends the most popular buiilds. In addition, they do not offer the data in a disaggregated manner to investigate. With the item changes in constant flux, a consistently updated item dataset would serve the community well. A goal is to obtain a Riot Developer API Key to significantly assist the community and perhaps integrate more real-time streaming capabilities.

## Target Audience
 The target audience are fellow data inquisitors interested in exploring characteristics of the game or honing their data science skills. Most of the intended audience will be familiar with the game. In the interest of not making this too large, this project will assume that the target audience has a modicum amount of knowledge in League of Legends. For those that may be new but have a background in data, [here is a resource explaining the fundamentals of LoL.](https://store.epicgames.com/en-US/news/a-beginner-s-guide-to-league-of-legends)

## Desired Insights
As mentioned prior, there is a significant amount of sites that can answer what the highest winrate builds from existing match data. At minimum, this dataset needs to be able to answer those questions. Some variations include:
- What itemsets have the highest winrate for a champion overall? How about in a given matchups?
- For each item, what are their best performing champions?
- What is an item's winrate by rank?
- Given an item, what champion class best utilizes it? How about the converse? *note: most sides do not provide this answer.*

Here are some questions that are not easily answered by many of the recommender sites:
- What are the most common items that result in an early victory? What are their highest buyers in those games?
- What items are ideal for taking neutral objectives directly compared to others? Are there scenarios where those items suffer a significant deviation in effectiveness?
- Construct a class of items based on similar characteristics and compare them with each other. Under what circumstances and breakpoints do they outperform each other? Which characteristics are invariant to champion kits?

These are some sample complex insights that are a project in its own right. However, without an easily available dataset, many of these are simply too difficult to produce. Many of these can be answered with the same dataset. Ideally this is an example of the critical utility that data enginners can provide.

 ## Datasets

 The goal is to produce a final dataset that fulfils these characteristics:
 - Is highly accurate.
 - Is in a straightforward format (i.e. schema).
 - Has the dimensions updated soon after a new patch or hotfix is pushed.
 - Requires a minimal amount of joins to ingest and visualize.
 - Provides a single platform to easily pull data from.
 - Data volume large enough to support AI processes (at least GBs).

Before we go into the datasets, some of these require access to Riot's API and their IP. To account for that, please read the following legal disclaimer:

> This LoL Datamining Project is not endorsed by Riot Games and does not reflect the views or opinions of Riot Games or anyone officially involved in producing or managing Riot Games properties. Riot Games and all associated properties are trademarks or registered trademarks of Riot Games, Inc
 
 
 To fulfil the required final dataset, we need data that can provide the basis for these. We will start with the Riot API and what it has to offer. Below is what is currently implemented and what is a future is in **(WIP)** which stands for **Work In Progress**.

 - [Account Information](https://developer.riotgames.com/apis#account-v1) **(WIP)**
 - [Summoner Information](https://developer.riotgames.com/apis#summoner-v4)

 To tie the info to certain accounts that can give information to playstyle, trends, and preferred champions, the account information is required. Since some Riot APIs deprecate summoner info, account information is used, but some still use summonerId instead of a puuid. Both are required to union an account's information. 
 - [Champion Information](https://ddragon.leagueoflegends.com/cdn/14.2.1/data/en_US/champion.json)
 - [Item Information](https://ddragon.leagueoflegends.com/cdn/14.2.1/data/en_US/item.json)

 The dimensional data required to perform basic analytics and deserialize certain entries in other datasets.

 - [Queue Type](https://static.developer.riotgames.com/docs/lol/queues.json) **(WIP)**
 - [Map ID](https://static.developer.riotgames.com/docs/lol/maps.json) **(WIP)**

Usually, items are balanced around Summoners Rift for 5v5 Ranked Solo Queue. However, the principles can still be applied to different queues and rotating game modes, especially if there are certain buffs or reduced cooldowns that could significantly change an item's power (e.g. URF Mode).

To avoid cardinality explosions at this stage, only summoner's rift 5v5 is used.

- [League Information](https://developer.riotgames.com/apis#league-v4)
- [Match Data](https://developer.riotgames.com/apis#match-v5)
- [Champion Mastery](https://developer.riotgames.com/apis#champion-mastery-v4) **(WIP)**

Finally we come to the fact data which is a large amount of what the data mining will be based off of. The Match Data has a large amount of columns, some of which are in complex forms (e.g. arrays) or enumerated fields. This project serves to unnest, dedupe, and provide a simple table to join post-shuffle.

## Tech Stack Used
For now, the tech stack is deceptively complicated for the low amount of tools:
- Python Notebooks
- Azure Synapse Analytics

Azure Synapse Analytics is Microsoft Azure's all in one shop for data integration. It differs from Data Factory in that it has more tools to support data scientists and integration with other microservices, such as Azure ML Studio. As a data scientist and data integration lead, I have used Azure Synapse for some time, but not often did I use it to handle data engineering tasks. With the skills developed at EcZachly's Data Engineering bootcamp in addition to my experience, the power of the tool is shown with these characteristics:

- Creation and running of Python Notebooks.
- Connection with external data sources or provisioned data systems such as blob or SQL DBs.
- Data Engineering flows and pipelines readily created in the workspace. 
- Various validation methods similar to dbt that can perform simple sanity checks or complex queries.
- In the event a check is too complex for the native data flow tools, synapse offers seamless integration with notebooks, SQL scripts, and external and dedicated data sources.
- Orchestration of pipelines to run, which will be essential to pull data while avoiding the API rate limit.
- Streaming data option.

The downside of Synapse is it's cost. For the convenience, it does require payment. However, Azure does provide $200 in credits used within the first month. By the end of February, the project is expected to have the dataset complete, where the costs will be assessed to either keep or slowly deprecate Synapse for other tools and flows. There were alternatives considered, such as dbt, Airflow, and other tools taught in the course, but Synapse seems too good at the moment to use. If I were to use another data tool, I am interested in seeing what [Tobiko](https://tobikodata.com/) has to offer after [meeting the Co-founders at a data social](https://www.linkedin.com/posts/hjersmanj_devprofs-sfbayarea-data-activity-7140939824257052672-91Ix?utm_source=share&utm_medium=member_desktop).

## Schema and Diagrams
Below is the current proces in obtaining the non-WIP datasets and transforming it into one dataset with supplied metrics.

![Current Diagram](readme_links/LoL_Data_Engineering_Diagram_Jan_2024.png)

You'll notice that some of the flows have the prompt to deprecate T1 SCD. While these should be T2 SCDs since it is logged by patch, the issue is that there is no simple method to ensure Riot updated the information correctly [after reading this statement from their API docs](https://developer.riotgames.com/docs/lol#data-dragon):

> Updating Data Dragon after each League of Legends patch is a manual process, so it is not always updated immediately after a patch.

Because of this, it is not ideal to rely on Data Dragon (the JSON apis used for some of the Dim data) in the future. There are some files that seek to scrape the data. However, to remain an ethical scraper, that project was delayed until it is refactored to scrape from [The LoL Wiki](https://leagueoflegends.fandom.com/wiki/Item_(League_of_Legends)). You can view the [current progress of the code here](https://github.com/HjersmanJ/LoL-Item-Datamining/blob/data_engineering/notebook/Web%20Scraping%20LoL%20items.json).

The flow begins by grabbing the relevant dimensional data on items, champions, and summoner info to convert to puuids. With the Fct Data, initially I thought that one would have to use Dim_Account for that process, but because it uses 'summonerId', we need to use the Summoner API. It is somewhat troublesome being in the middle of a tiered deprecation process where some endpoints are deprecated but others are not. This is used to convert the summoner info provided to convert to the correct key that is used in the Fct Matches dataset. Once this is correctly aggregated and partitioned by league (since an account can only be in one league at any given moment in time), the match dataset is unnested with the required items appended or joined to produce the metrics.

![Current Schema](readme_links/LoL_Data_Engineering_Schema_Jan_2024.png)

Most of the schema closely matches the sources of what Riot provides, though a focus is in the Fct_Match table, participants was a nested array of strings of puuids. Since that is not in an easily digestible state for [OLAP](https://www.ibm.com/topics/olap), it is unnested. It does cause a 10x cardinality explosion, but due to the nature of how often accounts will be used for metrics, this is a decision that must be made to ensure the data is easily usable as promised in the Datasets section.

## Data Flow and Pipelines
There are 4 data sources, each of which will at minimum have row count and null checks. Some will have more complex processes. Note to keep costs down at this stage, they will be pulled from csvs and uploaded into the Synapse Serverless DB. In the future, the data will be sourced and sinked in external databases. 

Starting relatively simple, here is the following data flow for *Dim_Champion_Details_Validation*:

![DimChampionDetailsValidation](readme_links/Dim_Champion_Details_Data_Flow.png)

All data flows must have a source and a sink. The source is of course the DimItemDetailsValidation that was pulled from the Downloading_Lol_Data Notebook accessing the Riot API. The flow in order checks for NullNames to ensure an enumerated value doesn't house a blank champion (note: there are more enums than champions because [many champions have been in development and scrapped](https://leagueoflegends.fandom.com/wiki/Category:Cancelled_champions)). Then the flow deposits the data into the serverless sink for ItemValidation to log the values for later viewing, assuming the job was successful.

That was fairly barebones, but for such small dimensional data, there often isn't more to check. One consideration is to match the names with another verified list of champions. If one doesn't match, then the pipeline should fail. Once Champion data is scraped, this would be relegated into that supportive dataset.

The *Fct_League_Info_Data_Flow* is an example of a more complex process:

![FctLeagueInfo](readme_links/Fct_League_Info_Data_Flow.png)

The flow starts by splitting the stream into two to allow concurrent runtime of the checks. The bottom branch performs the data validation check as was shown above. In the future, that check will turn into a flowlet, which can be thought of as a function in a programming language for reusability. I ascribe to the WET (Write Everything Twice) and [YAGNI principles, but not opposed to refactoring to adhere to DRY principles later](https://www.boldare.com/blog/kiss-yagni-dry-principles/#what-is-the-kiss-principle?-what-is-yagni-principle?).

The 'leaguePoints' field from the incoming data stream might be strings when they should be integers, so the top branch ensures that it they are. Furthermore, for this lower-scale version, there is a verification to ensure that those in challenger league have at least 300 points (I'm certain it's higher but couldn't find a source). These are joined back together with the primary key 'puuid'. This is broadcast joined by 'leagueId', which automantically sizes the buckets, handling it at any scale. Note that Leagues usually don't contain more than 1000 participants. There will be a point where the scale goes too far in the partition zone, in which case, league and division, and eventually simply league would suffice in truly large data. After the join, 'puuid' was duplicated, and a quick fix is to simply unselect it. 

Finally, since people are more interested in the top ranks more than the lower ranks, it is ordered by highest league points in descending order. Data Flows can be optimized using Spark, it can be partitioned by 'leagueID' since all tiers Masters and above are unique, and those not in those higher echelons won't have a 'leaguePoints' parameter for the ordinal function to consider. Normally ordinals are not ideal for big data, and if they are used, they should be used when the dataset is at its smallest. But in this case, it is relative to the entire dataset. If this were to be transferred for all ranks, this process can even scale in divisions and ranks.


![Fct_Match_Info_Transformation](readme_links/Fct_Match_Info_Transformation.png)

This one replaces the enumerated items with the item names for one portion of the dataset for those that want a single dataset (e.g. those not experienced with joins). It will also be useful for those looking to build a simple interactive dashboard. A **(WIP)** is to provide one for learners to use.

![Downloading Data to Synapse SQL Server](readme_links/Download_To_SQL.png)

Here is the updated pipeline to grab challenger data. Will scale to more if Dev Key is upgraded. Much of the cleanup is done in the notebook.

![Orchestration Example](readme_links/Orchestration_Example.png)

To show how orchestration is done in Synapse. Another sensible orchestration at scale would be per patch. It could either be triggered by a notification or by every two weeks (easiest assuming patches are on schedule).

Some samples of successful pipeline runs can be found below.

![Validation_Success](readme_links/Latest_Successful_Validation_Pipeline_Run.png)

The dataset is outputted to the integrated Synapse file system, but for the viewer's convenience, it can also be found in the "final_dataset" folder. The first version is titled "challenger_details".

![Transformation_Success](readme_links/Latest_Successful_Transformation_Pipeline_Run.png)

## Visualization: Integration with Power BI (WIP)
Since this is entirely on the Microsoft Azure Cloud, Power BI does have a set integration with the service. Below are screenshots on how to link to the service.

![Select Synapse as Source](readme_links/Select_Synapse_as_Source.png)

![Synapse Endpoint](readme_links/Synapse_Endpoint.png)

![Preview Data](readme_links/Preview_Data.png)

## Summary
In total, the project currently pulls data from 4 different sources (2 JSON, 2 APIs), uses joins where necessary, and collates them into a single dataset. Azure Synapse Analytics was used as the tech stack, which is an all-in-one tool that functions as a workspace for data engineering and analytics. In it, SQL pools are provisioned to cache the data in flow. Pipelines are triggered to ensure the runs were successful.

Futures are to implement more datasets, increase dataset size with longer compute or upgraded Dev Key with higher API limits, scrape the data from more reliable websites, and eventually ingest the data for data anaytics. This has been a stellar journey so far, and with the data engineering skills I've gained, I feel that I've unblocked many of the barriers I have faced as a data scientist.
