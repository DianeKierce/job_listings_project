# job_listings_project
Acquiring data set of job listings and analyzing to predict salary, cluster into job categories, and figure out how Amazon listings differ from others


## Technical Summary

### Data Acquisition and Preparation
I used the indeed.com API to obtain the data set for this project. Using the search terms "data scientist," "data analyst," "data engineer," "business intelligence," and "machine learning," I was able to download 1960 unique job listings for the greater Seattle area that were current as of June 1, 2017. On that date I was also able to obtain the following average salary estimates from indeed.com for each of those search terms:

| Search Term        | Average Salary           |
| ------------- |:-------------:|
| data analyst      | \$ 82,502      |
| data engineer | \$ 114,691      |
| data scientist      | \$ 132,071 |
| business intelligence      | \$ 142,139      |
| machine learning | \$ 147,173      |

(See the indeed_api_data_acquisition iPython Notebook for the code used for data collection via the Indeed.com API.)

From this data set, I isolated the useful features: city, company name, job title, and a one to two sentence "snippet" description of the job.

Using natural language processing (NLP) techniques, I engineered new predictors including vector counts for both the job titles and the snippets obtained for each job listing. I also calculated the length of each job title.

### Predicting salary for data job openings:
Salary is one of the most important features of a job for job-seekers and employers alike. I was able to identify certain words in job titles and job listings that are good indicators of estimated salary along with certain employer names and the length of a job listing.

#### Methodology
Based on the frequencies of certain words in the snippets and job titles, I narrowed down the list from hundreds of words to the most frequently used words for each: three for job titles and fifteen for snippets. These highest-frequency words along with city, employer name, and lenth of job title in words were the predictors for my models.

#### Results
After testing out decision tree, clustering, and naive Baysian models, I found that a decision tree performed the best, accurately predicting a job's salary category about 70% of the time.

The key predictors were the number of times the words "data" and/or "scientist" appear in a snippet; whether the word "scientist" and/or the word "data" appear in the job title; whether the organization hiring is Tableau, Amazon, the University of Washington, or another employer; and whether the job title is longer eight words.


The number of times that the word "data" appears in the job snippet is the most important feature in predicting salary. If "data" appears 0-1 times, then the salary is most likely in one of the highest two categories. In this case, if the hiring company is Tableau, the salary is likely to be in the second highest rather than the highest category.


If the word "data" appears two or more times in the snippet, then the next most important feature is whether the word "scientist" appears in the job title. If it does and the hiring organization is the University of Washington, then the salary is likely in the lowest category. If "scientist" is not in the job title but the employer is not UW, then the salary is likely in the middle category if the job title has eight words or fewer or the highest category if the title has more than eight words. And so on.


If "data" appears in the listing at least twice but "scientist" is not in the title, then having a job title containing "data" suggests a higher salary, in the second lowest category if the job is at Amazon and potentially higher if it is not. If "data" does not appear in the job title, then the salary is likely in the lowest category.



### Predicting job categories:
What keywords in job listings predict whether an employer is looking for a data scientist, data analyst, data engineer, business intelligence analyst, or a machine learning engineer?

#### Methodology
For this analysis I used several different types of models to test hypotheses about the categories of job types within this data set, including decision trees and ensemble methods, naive Baysian modeling, and clustering techniques.

I used the complete vector counts for the job titles as one set of predictors and the complete vector counts for the snippets as another. For each, the models aimed to predict the job category as labeled by the search terms I used to obtain the data.

#### Results
The snippets' optimal tree performed better than the job titles' tree, presumably because the snippets contained more text for the models to work with. With job title vector counts, the optimal tree could predict the correct category about 71% of the time, whereas the snippets vector counts achieved about 91% accuracy. (The baseline accuracy was about 42%.)

Ensemble methods such as random forests and bagging did not significantly improve the results, nor did naive Baysian modeling.

In the best performing decision tree model, the stems "machin," "busi," "data," "scientist," "analyst," "engin," and "intellig" were the most important features in the model for predicting the category. These terms make sense since they are all closely related to the category names.

As a check on whether the search terms actually did represent the best categories for these job listings, I utilized unsupervised learning techniques to see how many clear clusters of categories appear in the data and topic modeling with LDA (latent Dirichlet allocation) to describe those clusters.

Hierarchical clustering indicated that the job listings cluster into 3-6 clusters, depending on how coarsely or finely grained the segmentation is. Comparing LDA using 3, 4, 5, and 6 topics supported the selection of three clusters as the most natural, since the more clusters I considered, the harder it got to differentiate the topics generated by the model. The three topics can be understood as Self-motivated Designer, Scientist, and World-class Discoverer. For each listing, the model can classify it in terms of what proportion of the listing fits into each of these three categories.


### Predicting if a job at at Amazon or not:

With nearly 40% of the job listings, Amazon is the biggest player in data-related hiring in Seattle, so it is worth examining what sets them apart from the competition. What keywords in job listings predict whether a data-related job is at Amazon?

#### Methodology
Again I used the vector counts of the snippets as the predictors of whether a given listing is for a job at Amazon or some other employer. I developed naive Bayesian and decision tree models.

#### Results
Decision tree model could predict with 70% accuracy whether a job listing is for Amazon, while the naive Baysian model could predict with 64% accuracy. Not surprisingly, having the word 'Amazon' in a snippet is the best indicator that a job listing is for Amazon. 'Success' and 'use,' and to a lesser extent, 'portal' and 'recommend' also determine whether a job is at Amazon.


### Next Steps:
To improve this analysis I recommend further web scraping to obtain more text for each job listing to gather more data.

I also recommend adding meaningful phrases to the vector counts in addition to the single word counts utilizing n-grams and/or noun phrases.
