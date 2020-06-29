# Wine-Recs

Wine Recommendation System

Table of Contents

1.0 Introduction
2.0 Data Sets
3.0 Wine Review Analysis
4.0 Food pairing Analysis 
5.0 Wine recommendation
6.0 Future Challenges and Limitations

1.0 Introduction

The main purpose of this project was to explore and learn more about text preprocessing. In this project we are building a wine recommendation system with food pairings based on a user's food and wine preferences. We hope that this is a better method to help people choose new wines to try rather than relying on the points ratings (between 80-100) that all wines are given, as flavor profiles vary within similar point ranges and most wine drinkers care more about finding wine’s with similar flavor profiles that they enjoy, as well as providing the best wine and food pairing options

Our goal was to recommend wines with reviews that are most similar to the users described wine taste preferences, and that pair well with a specific food in a food category of the user’s choice. 

2.0 Data Used 

The first data set, the Wine data set, is from Kaggle and includes a list of ~130,000 different wines with wine name, review, reviewer, ratings, price, and some other information about the wine, and these reviews come from the Wine Enthusiasts magazines’s website. 

The second data set, the wine and food pairing dataset, is from the Wine Folly blog and this data set contains a list of the different styles of wine and their corresponding wine varieties and it lists the foods by food category that pair with that wine. This data set provides the user with known foods and food categories that pair with different wines. 

The third data set, the recipes data set, is from Kaggle and listed out ~40,000 different recipes from different cuisines with their corresponding ingredients.  


3.0 Wine Reviews Text Analysis 

First, we had to do some preprocessing in the wine data by doing a variety substitution to make sure that the wines fall under the same main variety category provided in the wine and food pairing dataset. For example, Muscat of Alexandria and Muscat Blanc both fall into the main Muscat variety.  We also realized we would need to split the full wine dataset up into the different styles of wine provided to us by the Wine Folly file, since we only wanted to search for wines in categories where we knew the wine style was a good pairing for the inputted food. 
We built and trained our model using Gensim’s Doc2Vec method with all the descriptions appearing in the wine description dataset.  The Doc2Vec model is an extension of the Word2Vec approach towards documents instead of words. It computes a feature vector for every document in the corpus, and this can be used to find similarity between documents and words. 
We did this, first, by using Spacy's natural language processing for each review and tokenizing each description into a list of words containing the root word (lemma) for each word in the description, this was to avoid complications with variations of the same word. This resulted in a list of tokenized wine descriptions (sentences) for each wine. Next ,we added 2-word and 3-word phrases using Gensim’s Phrases module.  After this we were finally able to build and train our model with the Doc2Vec module.  
Using this model we then created datasets for each style of wine (Bold Red, Medium Red, etc.) to build a dictionary of words used in the descriptions and the TF-IDF models for each.  We then built a similarity matrix of the word vectors in the dictionary with the TF-IDF as an additional parameter for term weightings, as well as compute the cosine similarity (and vector index) for each description in the dataset. By doing this we are able to then compute the similarity of an outside description vector to the vectors calculated in the model, and the return from the dataset the vector with the highest cosine similarity to the inputted description. Additionally, we saved the similarity matrix and index for each wine style dataset for ease of access later on when calling our functions, and only having to compute the similarity models once.

4.0 Food pairing Analysis

For the food pairing analysis we are using two data sets, the recipes data set and the wine and food pairings from the Wine Folly blog. The user inputs the category they want, from the available food categories, and what ingredient inside that category they want their wine to pair with. If the ingredient inside that category is already present in our Wine Folly dataset, we return all the possible styles of wines that pair with it. 

In case that the ingredient is not present in the Wine Folly dataset, but it is present in our recipes dataset we find which ingredient in the Wine Folly data set is most similar to the ingredient inputted in that food category. 

To do this we build an undirected network representation of the ingredients present in the recipes data set using the NetworkX module. In this network each ingredient represents a node, and when two ingredients are present in the same recipe there is a link between them. The purpose of creating this network using the recipes dataset was to handle the ingredients that the user specifies which aren’t present in the Wine Folly data set. 

We then use this network and the ingredients present in both the Wine Folly dataset and the recipes ingredients list to find the ingredient under that food category that is most similar to the inputted ingredient using the Jaccard Coefficient similarity function. Finally, we use the most similar ingredient to find the styles of wines that most likely pair with the inputted ingredient. 

5.0 Wine recommendation 

The final pairing recommendation function works through several steps, the first is to gather the food and wine description inputs required to make the recommendation. A user will first input a food category (with a list of accepted categories provided), and then input an ingredient from that category. If the input is already present in the wine pairing dataset from Wine Folly, the input is accepted as the ingredient. If not it is checked against the list of ingredients in our Recipe dataset, if the inputted food is not present the user is asked to input more ingredients until they input an ingredient that is present in that list, at which point the similarity of the inputted ingredient to ingredients present in both the Wine Folly dataset and Ingredients list is calculated to find which ingredient in our Wine Folly dataset is most similar to the inputted ingredient, and, for all purposes going forward, the most similar ingredient is assumed as the new ‘inputted ingredient’.  Finally, the user inputs a list of description words and phrases that they would use to describe wine that they enjoy.
The function works to retrieve wine recommendations after this in two main steps, the first is to find the wine styles (Bold Red, Light White, etc.) that pair well with the inputted food. And the second is to compute the similarity between the inputted description vector to all the wines in the wine style models that paired well with the food.  As an example, if someone were to input ‘Meat’ as the food category, and ‘beef’ as the ingredient,  the only two wine styles that pair well with beef are Medium Red and Bold Red.  Following this, the only wine styles that the function would work with going forward are Medium Red and Bold Red.   The getTopWines() function then goes through to compute the similarity of the description vector to each of the descriptions in the Bold Red and Medium Red models , and then returns a sorted list containing the wine style, description index, and similarity between the for each. 
Finally, with everything calculated, the getTopThreeWines() function takes the top three wines from the list and gathers all the information about the wines needed for the recommendation output and formats that information to be readable for the user.

6.0 Future Challenges and Limitations

One of the main challenges of this analysis was to handle ingredients inputted that were not present in the Wine Folly data set. Our solution of using the Recipes dataset to find the foods most similar to ones in the Wine Folly dataset did expand the list of food input possibilities, however any ingredient not present in the Recipe dataset would still not be a valid input.  

We also still required that the user specify the food category in order to limit which foods the input should be compared to. To improve this, the system would have to assign a food category to the inputted ingredient without the user having to specify the food category that the ingredient belongs to. This could be achieved by creating a classifier to assign a food to a specific food category. 

The second limitation we have is that there is no way for us to evaluate our model since we don’t have any labels or data to tell us whether our recommendations are correct or not. In order to achieve this we would have to find a data set that has information about the user's wine and food selection information or run a survey to see whether the recommended wines matched their preference. 
