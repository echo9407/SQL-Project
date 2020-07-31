# Ted Talk Data Analysis Project

### 1. Introduction & Project Overview:
We are a small consulting team working with a well-known research university to better understand how they can improve their marketing effectiveness by using digital channels and posting educational videos on the web. The client ultimately wants to help its professors and researchers land speaking gigs at prestigious conferences and gain status in higher academic circles. The client wants to use the TED Talk dataset to better understand how different qualities of videos (e.g. length, title, descriptions, topics) affect their views and ratings numbers. The client can apply these insights to improve the qualities of their own videos in hopes of gaining more views and getting better ratings.

TED Talks are currently one of the main ways that novel research ideas are shared on the internet. TED, which has been available for free online since 2006, has hundreds of millions of views on its videos and is known for spreading new ideas in technology, education, design. Our client’s goal is to post their own videos online that can spread learning to a greater number of people and help the university market itself through new channels. If professionals and researchers associated with the university are able to gain popularity online, this will translate into better opportunities for them to expand their prestige in the academic world and land speaking gigs at conferences such as TED, which in turn will improve the reputation of the university. This dataset can help us in this task by informing the client on how they should present the videos online, which factors to focus on to maximize views and ratings, and which topics to discuss that are most popular amongst viewers. Whether they are posting through TED or another online channel, this information should help the organization understand how to improve its digital presence.

The decision to use this dataset for our client engagement was made after some research about TED Talks was conducted. Up to date, there are about 3000 TED Talks free online, creating 11 billion total number of views on all TED Talks and about 3.2 billion views last year alone. TED.com originated from an annual conference in the 1990s and its early emphasis was on technology and design, consistent with its Silicon Valley origins. As a result, many ideas shared on the platform involve cutting edge ideas and technology, as it is quite common for researchers to share their recent research achievements through TED Talks. With so many views on each TED talk and ample feedback from the users, such as comments and ratings, we have a great opportunity to help our client understand how different aspects of online educational videos are related to the quality of ratings and number of views.

The first step for our team will be to better understand the data at hand by performing some exploratory data analyses. This process should highlight various features of the data including the data structure, any outliers, trends across variables, missing data, amongst others. We may also use feature engineering, in addition to text mining on the character fields, to come up with new variables that could be beneficial to our analysis and provide deeper insights. Based on these insights gained from our preliminary analyses, we will attempt to narrow down the number of variables to include those that are most relevant to the business problem, as well as the optimal way to store our data in a single schema. We will then use various modeling techniques to determine which features better predict higher user ratings and views.

The insights derived from this project will enable the client identify key features that will influence audience engagement with the content they put out. They will be able to put out more targeted content that will increase professors recognition in the academic and related industries as well as the university’s public image. This will in turn create additional revenue for both parties as professors would potentially be invited to other paid global conferences and speaking engagements, and the university would have created a more prestigious image for the caliber of education provided which could increase student enrollment.

### 2. Data Overview & Sample:

**Dataset:**

Description: This data contains information about all audio-video recordings of TED Talks uploaded to the official TED.com website until September 21st, 2017. The TED main dataset contains information about all talks including number of views, number of comments, descriptions, speakers and titles. The TED transcripts dataset contains the transcripts for all talks available on TED.com. For the purposes of this project, these datasets will be combined in the same database schema.

Source: The dataset was sourced from Kaggle

Type: See below for a brief description of each column in the data.

● comments: The number of first level comments made on the talk

● description: A blurb of what the talk is about

● duration: The duration of the talk in seconds

● event: The TED/TEDx event where the talk took place

● film_date: The Unix timestamp of the filming

● languages: The number of languages in which the talk is available

● main_speaker: The first named speaker of the talk

● name: The official name of the TED Talk. Includes the title and the speaker.

● num_speaker: The number of speakers in the talk

● published_date: The Unix timestamp for the publication of the talk on TED.com

● ratings: A stringified dictionary of the various ratings given to the talk (inspiring, fascinating, jaw dropping, etc.)

● related_talks: A list of dictionaries of recommended talks to watch next

● speaker_occupation: The occupation of the main speaker

● tags: The themes associated with the talk

● title: The title of the talk

● url: The URL of the talk

● views: The number of views on the talk

● transcript: The official English transcript of the talk

Extent:

● Main dataset has 2550 tuples and 17 attributes

● Transcripts table is smaller at 2464 tuples and 2 attributes

● Transcripts joins with main dataset on URL (18 distinct features in total)


### 3. Normalization Plan:

The main goal in our normalization plan was to break up the tables in the original data set into a 3NF database, as reflected in our ER diagram. We combined the transcript data set to our original one and with our data in 3NF, we ended up with 15 tables. To achieve 1NF, we first ensured there were no repeating rows in the original dataset so that every record was unique and each cell only contained one value. Since all videos in the dataset were unique there were no repeating rows so we created a videos table and assigned each video a unique identifier. The ratings and related_talks tables contained nested json objects that we had to extract which resulted in duplicate rows as we had to ensure there was only one value in each cell for each record. We removed all duplicates, assigned unique identifiers, and created a table that held only unique records to increase database performance and avoid wasting space. The tags and speaker_occupation columns contained lists that we also had to extract, assign unique identifiers to, and remove duplicates from to make the data look cleaner and ensure easier querying.

To get the data in 2NF we made sure all tables had a unique identifier that every other attribute in that table was fully dependent on, and also made sure that for all tables with composite keys, all other attributes in that table were fully dependent on the entire composite key and not just part of it. We transformed tables such as main_speakers to include information that was more relevant to our business case and that would make writing queries easier such as assigned them an id and including personal information like occupations. We merged comments, views, and duration to create a more insightful table called video_stats that contained information about each video’s performance and audience engagement. We clearly identified foreign keys and made sure they related back to the right tables to avoid any errors. Once all this was put in place, we had also achieved 3NF.


### 4. ETL Description:

Once we merged the transcripts and main datasets together to create a main data frame, and ensured there were no duplicates, we cleaned up the data a little using Python. We changed date formats from Unix timestamps to DD-MM-YY format, checked for null values and made all of them blank instead.

We first created the videos table and dropped all duplicates so we could easily index the table which would serve as unique identifiers for each video. We then joined this table back to the main data so that the creation of all other tables will reference the right video. For each table we created, we joined it back to the main data frame to have all data in a central location and make it easier to create subsequent tables. For the events table, we had to create start and end dates attributes for each event and assign an id.

For the occupations table, we extracted all unique occupations from the main table, deleted all duplicates, and assign each occupation an id. Since we did not have individual languages for each video but only the language count, we created the language table using only the video_id and languages attributes. Creating the main_speakers table resulted in duplicate rows so they all had to be dropped before an id could be assigned to each speaker. The presentations, url_links, transcripts, dates, and video_stats table were more straightforward and made by extracting all pre-decided attributes from the main data frame, with all duplicate rows dropped.

To create the video_tags table, all individual tags had to be extracted from their list. Doing so resulted in duplicates as the table contained every tag for each video as a new record so in creating the tags table. To use this data to create the tags table, all duplicates had to be dropped and then ids were assigned to each tag. These ids were then merged with the video_tags table, the columns were renamed, and the tag column was dropped as it was no longer relevant.

The nested jsons in related_talks were extracted and transformed into a data frame and all duplicates were dropped. We followed the same process for ratings where each rating was extracted from the json object and all duplicates were dropped so the table only contained unique ratings and their assigned ids. Video_ratings was then created matching video ids to their various rating ids. The tables and data were then pushed into the database using the sample code below.



### 5. Analytical Procedures:

Since our client is interested in understanding the aspects of our project that relate to video views, we have taken preliminary steps to conduct an exploratory data analysis for features that may be related to the outcome variable. To simulate how a real database user would conduct an analysis, we are using a combination of Postgres and Python, including the SQL Alchemy, Pandas, Matplotlib, and Seaborn packages. Data is queried from the database using SQL Alchemy, stored in a Pandas dataframe, and visualized with Matplotlib and/or Seaborn. Specific parts of the EDA are detailed below:

**Procedure 1: What is the distribution of the outcome variable – views?**

To help the client with their goal of producing popular online learning videos, we must first understand how many views the TED Talks in our database generated. Let’s examine the views variable from the video_stats table.

**Procedure 2: The outcome variable looks highly skewed to the right – would it look better under a log transformation?**

The distribution of the outcome variable takes on a much more Gaussian form when we apply a log transformation. If we decide to use a parametric method to build a machine learning model, we should probably apply this transformation before we do so. This will help to eliminate outliers from the training examples and ultimately generate predictions about video view statistics that are more accurate. Let’s now check out some of the predictor variables at our disposal.

**Procedure 3: How does the duration of a video affect its number of views?**

One of the other features in the queried table is video duration. Let’s check it out in a scatterplot to see if we can observe any relationships in the data.

Unfortunately, most observations are bunched in the bottom right and we cannot easily see a relationship in the data. Does it look any different under a log transformation as performed in Procedure 2?

Not really – the observations are certainly more spread out than they were previously, but there are no easily identifiable trends that could benefit us for predictive modeling.

Let’s do some feature engineering to create new variables.

**Procedure 4: Feature Engineering on Video Data**

The flexibility of Python and Pandas allows us to easily create new columns using a few short lines of code. In this case, we are interested in seeing whether the length of a video title or description have an effect on its view count. In this part, we go back to the video_data table from previous procedures to make new columns for the length of their titles and descriptions (measured in number of characters).

Looking at the current data, it may be helpful to also include new features that count the number of words, not characters, that appear in each field. We update the table below:

Now that the new features are ready, we can continue to explore them through plots and graphs.

**Procedure 5: Visualizing relationship between title length and view count**

We seek to know if the length of a video’s title has any significant effect on its view count. To better understand the relationship, we can once again use a scatter plot in Matplotlib to visualize the data.

Despite the new feature engineering efforts, the plot still shows no strong relationship between title length and number of views. The videos that show the most views here are also in the range of values that are most common for observations in our database, so it is not entirely clear if these would be useful predictors for modeling endeavors. Let’s check out the descriptions to see if we get better results.

**Procedure 6: Visualizing relationship between description length and view count**

The plots above show a slight relationship between description lengths and view counts. Through the similar distributions in both plots, we can see that videos with the highest views tended to have description lengths between 25 and 60 words, or alternatively, between 150 and 400 characters. These insights could be actionable for our client and help generate some initial best practices for the videos they post.

Let’s continue to explore the data and check out the tags on each video.

**Procedure 7: Which tags were included on the videos with the highest average number of views?**

One of the features captured in our database is tags, which has 594 distinct values. We can combine the video stats and tags data to generate summary statistics about each tag and see which has the highest average view counts.

