# Call of Duty: Vanguard v.s. Battlefield 2042: BETA Analysis

## Executive Summary

### Description

We have been tasked to build a machine learning algorithm trained on these 2,000 comments that can predict whether a comment came from Vanguard’s subreddit or Battlefield’s.  We can then use it to test which comments are being classified as Vanguard comments and why along with some rudimentary Exploratory Data Analysis.  If there are specific issues only pertain to Vanguard, we need to make sure our QA and engineering teams are aware of them so we are not losing players to BF2024.


### The Data Science Process

**Problem Statement**
We just launched the beta on September 18th for our new installation in the Call of Duty franchise returning back to WWII (again), for Call of Duty Vanguard, and we’re all set to launch the full game in a little less than a month here on November 5th!  I have been tasked by our executive team to analyze the talk out on the town regarding our beta launch, and compare it to our main competitors over at EA DICE and the feedback they are seeing on their current beta release of Battlefield 2042 who’s open beta starts today, October 8th.

**Data Collection**
You will first run the get-reddit-data.ipynb to scrape the desired Reddit posts.  The notebook will allow you to export the results into a csv after creating a "data" directory in the "project_3_master" directory.  The data will later be read into the subreddit_analysis-vanguard_vs_bf42.ipynbn to conduct the cleaning, preprocessing, and modeling.

We collected data from the Call of Duty subreddit through a third party API called Pushshift.  Their API limits you to 100 posts per pull request,  so we had to run 10 separate requests and compile their results to collect enough data to analyze properly.  We also conducted this same process to compile 1,000 posts from Battlefield 2042’s subreddit to know what we’re up against.

**Data Dictionary**
| subreddit | title | target | tokenized title | lemma_title | stem_title | title_char_length | title_word_count |
| --- | --- | --- | --- | --- | --- | --- | --- |
| str | str | int | list of strs | str | str | int | int |
| Which subreddit post comes from | Title of post | Binarized subreddit: 1 for Vanguard, 0 for Battlefield 2042 | list of tokenized words in title | join of lemmatized tokens | join of stemmed tokens | character length of title | word count of title |

**Data Cleaning, EDA, and Preprocessing**
Each of the 2,000 posts we collected has a title, and together all serve as the corpus to our model.  As opposed to just feeding all this raw data into our model, we are going to clean it up in a variety of ways to improve our models performance.  This cleaning process can be broken down into 4 major steps.

>1.Tokenizing- This is the process of breaking down full sentences into smaller words that are more manageable to work on.  This step can also remove special characters and other unwanted data contained in our titles.
>2. Stop Words- This is the process of removing all words that really don’t help us gain more insight into the posts, and by removing the stop words it helps us identify the true meaning and point of the post.  For this project, we also decided to remove words that where too easy to identify which subreddit the post may have come from like “call” “of” “duty”, “vanguard”, “battlefield”, and “2042”
>3. Stemming a word breaks it down into its stem form.  As you can see in the examples, they don’t break down into actual words but will make different forms of a word equivalent which can greatly improve metrics and analysis designed to measure word frequency.
>4. Lemmatization is very similar to stemming except it converts the word into its base form.  

We created different version of our title column using both stemming and lemmatization and saved them as new columns.

The top combination of two words from our subreddit’s last 1,000 posts has a few combination you can really expect to find up there, like “anyone else” and “playing beta”, but it was interesting to see player concerns topping the list right behind them.  These concerns include how well the new game is going to integrate with the existing games we have on the marketplace, particularly Call of Duty Cold War which launched just under a year ago in November of 2020.  In particular, a handful of posts were asking if the Tier Skips earned in Cold War will transfer over to Vanguard.  Aim Assist for controller players seems to be a mixed bag and is no stranger to controversy.  A few posts have also noted there is a huge glare from the sun that makes it difficult to see opponents.  Another subset talks about how the machine pistols seem to be overpowered.  Lastly, the posts that address Champion Hill, our newest tournament style game mode seems to foster curiosity and excitement.

**Modeling**
Our models that used only english stop words performed the best, however it's to be expected because they didn't remove easy words like "Call", "Duty", "Battlefield", and "2042" etc.  Once we removed these values, our models performed less effectively but it's more of the analysis we are looking for.

We fit three different pipelines and gridsearched a range of hyperparameters 
>1. TfidfVectorizer(), MultinomialNB()
>2. CountVectorizer(), MultinomialNB()
>3. CountVectorizer(), TfidfTransformer(), RandomForestClassifier()

**Evaluation and Conceptual Understanding**
Of the models that removed the easily identifiable words, the best performing model was:
>Pipeline(steps=\[('cvec', CountVectorizer(max_df=0.3, max_features=2000)), ('nb', MultinomialNB())])

We certainly beat the baseline score.

| Column Used | Best Estimator | Train Score | Test Score | True Positive | True Negative | False Positive | False Negative |
| --- | --- | --- | --- | --- | --- | --- | --- |
| stem_title | Pipeline(steps=\[('cvec', CountVectorizer(max_df=0.3, max_features=2000)), ('nb', MultinomialNB())]) | 0.908955223880597 | 0.73 | 234 | 249 | 81 | 96 |

**Conclusion and Recommendations**
Our beta is doing well, but let’s have a dialogue about the issues that keep coming up. 
>Topics of discussion:
>>Tier Skips
>>Cold War
>>Aim Assist
>>Sun Glare
>>Machine Pistol
>>Classes

We beat the baseline, but our model needs work.
>Explore misclassifications
>Sentiment Analysis
>Aggregate models
>More data!

Let’s continue to monitor these subreddits as they move out of beta into full production.