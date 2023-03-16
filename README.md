# Making Movie Recommendations

## Introduction

**Author**: Amelia Dobronyi  
**Pace**: Flex  
**Instructor**: Abhineet Kulkarni  

## Overview

This project introduces a system that recommends movies to users based on how he or she has rated previous films, for use by a streaming service that collects explicit ratings data on content choices by uesrs. The [notebook](https://github.com/adobronyi/predicting_movie_recommendations/blob/main/student_v1.ipynb) goes through the process of data preparation, model building and validation, and creating the recommender. The [presentation](https://github.com/adobronyi/predicting_movie_recommendations/blob/main/presentation.pdf) discusses approaches to building recommendation systems and provides and example of this one.

## Business Understanding

Streaming services such as Netflix generate an abundance of internal data solely by offering users the chance to rate films that they have seen. This data can then be put to use by suggesting other content that the user may enjoy, thus keeping the user engaged with the platform. The methodology by which this data can be incorporated into a so-called "recommendation system" offers many model-based approaches, and the purpose of this project is to choose the best approach (as validated by the data) and then implement it. 

There are two broad-based approaches that are currently used in designing recommendation systems:

- *Collaborative filtering*: A system to produce ratings based solely on previous ratings and using no other endogenous data about either the users or the films. This system makes use of similarity algorithms (more on those below) that either compare users to other users or compare movies to other movies.

- *Content hybrid*: A system to create recommendations based on endogenous features about the product being recommended. In this context, a hybrid rating system would use endogenous data about the movies (i.e. genre, cast, runtime) to find similar movies to what a user has (implicitly or explicitly) expressed a preference for.

In this business case, the stakeholder is a streaming service that possesses user ratings of a set of films, for which the only other data they possess is film genre. To build a usable recommendation system, the notebook will take the following steps towards the deliverable:

1. Import the data and transform into a usable form compatible with the surprise package for collaborative filtering
2. Use GridSearchCV to fit various models via cross-validation and determine the optimal parameters
3. Introduce a content filtering feature to see if it improves results
4. Implement the best model via the recommender method. 

## Data Understanding

This project uses the [MovieLens 100k Dataset](https://grouplens.org/datasets/movielens/latest/), a subset of a larger database that offers roughly 100k ratings on about ten thousand movies from 610 users. While the full dataset containing millions of ratings would likely make for a more robust model, it would also require more computational power than the scope of this project calls for. A recommendation system can be readily built and validated using the smaller dataset.

![fig1](./figures/fig1.png)

![fig2](./figures/fig2.png)

## Modeling

As expected from above, there are 610 users in the dataset, all of which will be included in cross-validation. It is important to note that there is a much larger number of movies than number of users, which is expected - a single user can rate many movies. From the perspective of making recommendations, it will be much faster for similarity algorithm to make comparisons between users than movies, so that will be the input of choice during the cross-validation stage.

The surprise package offers four kinds of similarity algorithms that we can use from:

1. cosine similarity
2. mean-square difference similarity
3. pearson similarity

Additionally, there are numerous approaches to the actual prediction algorithm. The most basic are SVD and KNN, though we will examine the broad set of algortithms in this notebook.

Rather than using a test-train split, which would withhold some of the data for final validation of the model, I have opted to rather use all of the data for cross-validation, and then produce an in-sample error statistic using the final model selected. Since we are choosing the model parameters solely from the results of cross-validation, and then implementing it in the final product that will be used, keeping a hold-out set for the purposes of validation does not serve any real purpose in this project. However, if this stage were one of many in the project, and we were going to continue to build on the results of the model selected from cross-validation, then it might make sense to reserve some data for unbiased estimation later on.

Additionally, we need to consider the choice of error metric. The surprise function offers multiple options, but the two most commonly chosen are MAE (mean absolute error) and RMSE (root mean squared error). Both are useful in that the error is interpretable as a function of the result unit, i.e. an RMSE or MAE of 1 would both represent the average error in the rating being off by a value of 1 (out of 10). The difference is that MAE treats each deviation the same, whereas RMSE penalizes larger deviations from the true value. The choice of error in model selection thus relies on our own interpretation of model value - is the bigger risk the recommendation of a movie very far off from the user's preferences, or movies that are perhaps just so-so but maybe more frequent? We can look at both in the code below, but most likely the RMSE will be used to deliver the final model. 

The documentation for surprise selection algorithms can be found on its website:
https://surprise.readthedocs.io/en/stable/prediction_algorithms_package.html

## Deployment

For a given user (the user number must be inputted), the recommender provides a list of the five most recommended movies. The user will also have the option to get a 'cold-start' recommendation, which simply provides a list of the highest rated movies. The user also has the option to constrain the recommendations via genre.

![fig3](./figures/fig3.png)

![fig4](./figures/fig4.png)

## Conclusion

This notebook contains the process by which user ratings for movies were used to create a recommendation system. The final model chosen to produce the ratings, SVD, had an RMSE of .55 on the data sampled, interpreted as estimated recommendations were off by about half a rating point. While the experience of that error will differ between users - that is, how much utility users get from half a rating point - there is room to improve this model in the next iteration. Instead of using user similarity to generate the ratings, as was done in this project for computational reasons, an extension of the project might look at how movie-to-movie comparisons improve the model. Furthermore, given the abundance of information about movies, more robust content filtering could be and should be introduced.

## Repository structure

- technical notebook: student_v1.ipynb
- figures folder
- data set: README.txt, links.csv, movies.csv, ratings.csv, tags.csv
- README.md
- presentation.pdf
- .gitignore
