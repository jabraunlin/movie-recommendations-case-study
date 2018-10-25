# movie-recommendations-case-study

Goal: Build a recommendation system based off data from the [MovieLens dataset](http://grouplens.org/datasets/movielens/)

The task was to predict a rating for each user and movie pair in data/requests.csv by utilizing the training.csv, movies.csv, and users.csv datasets.

Recommendation accuracy was based on taking the top 5% of movie ratings we predicted for each user and using that as the "recommendation set". These were then compared with the actual top 5% of movies that were supposed to be recommended to each user (given as a separate dataset by Movielens). However, for purposes of cross-validation, I used RMSE between predicted rating and actual rating. 

## EDA and First Model

The test set and training set had a solid overlap of users and a solid overlap of movies, but it was also fairly sparse (many users only had only rated one movie).

All our dasets given for model training purposes were merged together in hopes that I could gain insights about particular types of users or movies. In order to give meaning to some of the groups, I trained the data on a simple Matrix Facorization model utilizing ALS error minimization, using the movies, users, and ratings as inputs. This was the most logical choice given we had high-quality, explicit feedback. However, due to sparseness in the training set, there were quite a few missing predictions in the validation set. These were imputed with the mean rating for all users across all movies.

From here, I was able to compare subsets of the data by their average RMSE. 

Comparing users of different ages/occupations resulted in a small, but not highly significant difference in error. Comparing different genres of movies resulted in similar findings. Although, when comparing users who had rated the highest number of movies with users who had only rated one movie, there was a glaring difference in error (avg. of 0.72 vs 0.94). This was expected, as matrix factorization can't accurately predict ratings for users with so few data points. I had only selected the few most important latent features, so there was some apparent information loss. This led me to believe most of the error had come from my imputation for missing values. 

## Model Tuning 

In the second implementation, I fit the same ALS model, but when given missing values for the validation set, I instead fit them to another "rules based similarity model". This model was created on the assumption that a user will most likely rate movies belonging to similar genres. 

The model was created by imputing the average rating of each genre for each user. If a new genre was found, the average rating for that movie across all other users was given. If I came across a new movie in the testing or validation set, the rating was the mean for all users and movies.

By ensembling the matrix factorization model with this simplistic rules-based model, my cross validation score significantly improved, corroborating my first assumption that a lot of error was coming from our first imputation of the mean. 

ALS parameters were tuned based on cross-validation results. 

## Future Improvements

Next time, I would like to employ other techniques such as FunkSVD using Stochastic Gradient Descent. SGD would definitely improve upon the model by limiting the amount of ratings we have to predict using the crude "rules based" model. Furthermore, the "rules based" model's importance could be further reduced by replacing it with a model using item-item or user-user collaborative filtering. 




