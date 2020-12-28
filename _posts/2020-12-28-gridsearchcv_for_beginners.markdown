---
layout: post
title:      "GridSearchCV for Beginners"
date:       2020-12-28 23:18:08 +0000
permalink:  gridsearchcv_for_beginners
---


It is somewhat common knowledge in the data science world that 80% of the time spend on a project consists of collecting, cleaning, and organizing data. The remaining 20% is where the all the fun happens; models are trained, tested, validated, and then re-trained until the model performance is found to be adequate. The project included in this blog was the culmination of 2 months of cleaning and organizing data, creating visualizations, a bit of a mental breakdown, and then finally model selection, validation, and tuning. I’ll skip right to parameter tuning to avoid having to re-live through the nightmare of cleaning this dataset.

Before this project, I had the idea that hyperparameter tuning using scikit-learn’s `GridSearchCV` was the greatest invention of all time. It runs through all the different parameters that is fed into the parameter grid and produces the best combination of parameters, based on a scoring metric of your choice (accuracy, f1, etc). Obviously, nothing is perfect and `GridSearchCV` is no exception:

- “best parameters” results are limited
- process is time-consuming

The “best” parameters that `GridSearchCV` identifies are technically the best that could be produced, but only by the parameters that you included in your parameter grid.

```
from sklearn.model_selection import GridSearchCV
from sklearn.neighbors import KNeighborsClassifier
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import MinMaxScaler

knn_pipe = Pipeline([('mms', MinMaxScaler()),
                     ('knn', KNeighborsClassifier())])
										 
params = [{'knn__n_neighbors': [3, 5, 7, 9],
         'knn__weights': ['uniform', 'distance'],
         'knn__leaf_size': [15, 20]}]
				 
gs_knn = GridSearchCV(knn_pipe,
                      param_grid=params,
                      scoring='accuracy',
                      cv=5)
gs_knn.fit(X_train, y_train)
gs_knn.best_params_

Output:
{'knn__leaf_size': 15, 'knn__n_neighbors': 5, 'knn__weights': 'distance'}

# find best model score
gs_knn.score(X_train, y_train)

Output:
0.84035137

```

The exhaustive search identified the best parameters for our K-Neighbors Classifier to be `leaf_size=15`, `n_neighbors=5`, and `weights='distance'`. This combination of parameters produced an accuracy score of 0.84. Before improving this result, let’s break down what `GridSearchCV` did in the block above.

1. ___estimator___: estimator object being used
2. ___param_grid___: dictionary that contains all of the parameters to try
3. ___scoring___: evaluation metric to use when ranking results
4. ___cv___: cross-validation, the number of cv folds for each combination of parameters

The estimator object, in this case `knn_pipe`, must be scaled accordingly, based on the distribution of the dataset as well as the type of classifier being used. The scoring metric can be any metric of your choice. However, just like the estimator object, the scoring metric should be chosen based on what type of problem the project is trying to solve. The other two parameters in the grid search is where the limitations come in to play.

***

## Limitations

The results of `GridSearchCV` can be somewhat misleading the first time around. The best combination of parameters found is more of a conditional “best” combination. This is due to the fact that the search can only test the parameters that you fed into `param_grid`. There could be a combination of parameters that further improves the performance of the model. So why not just include more values for each parameter?

As mentioned before, conducting an exhaustive search of all the parameters is an incredibly time-consuming task.

```
params = [{'knn__n_neighbors': [3, 5, 7, 9],
         'knn__weights': ['uniform', 'distance'],
         'knn__leaf_size': [15, 20]}]
```

In our example, there are 4 values for `n_neighbors`, and 2 each for `weights` and `leaf_size`. This will produce a total of 16 different combinations which might not seem like very much. But in order to generate better results, we also included a cross-validation value of 5 which brings the total number of jobs to 80. Each value added to the parameter grid dictionary can significantly increase the total runtime of the function. By adding another `leaf_size` variable, the total number jobs jumps from 80 to 120 with a single value!

Not only will waiting for the grid search to complete take some time, but once you have your results, best parameters, and scores, there could still be more changes made to your parameter grid to possibly improve your results again.

For example, if the best `n_neighbors` was found to be 9, the max value in our list, how can we be sure that an integer greater than 9 won’t improve our results even further? The only way is to test it out by adding more values and running the search again. But we just discussed how adding parameters increases the runtime significantly. To prevent the search from taking too long to finish, whenever I increase the max (or decrease the min) value of a list, I always remove the same number of values from the other end of the list. To see if `n_neighbors` > 9 will give me better results, I added 2 more values while also removing 3 and 5 from the list.

```
params = [{'knn__n_neighbors': [7, 9, 11, 13],
           'knn__weights': ['uniform', 'distance'],
           'knn__leaf_size': [15, 20]}]		 
```

This way, I am still conducting the same number of jobs but with different values for my parameters. I can comfortably drop the lower values because I know that the largest `n_neighbors` produced the best results.

__WARNING: This will not always be the case. Like everything else in data science, it is entirely dependent on the data and the problem. If your results worsen, try running the search again with different values.__

## Results

After spending hours on cleaning the data to fit your model and tuning the parameters using `GridSearchCV`, you may come to find that all that hypertuning didn’t improve your model performance by very much. Was it worth it? Maybe. It all depends on how much time you spent on tuning your model. If you took a whole day to test out parameters and only improved your model accuracy by 0.5%, perhaps that wasn’t the best use of your time. I am definitely, absolutely not speaking from personal (read: painful) experience.

## Suggestion

Although `GridSearchCV` has numerous benefits, you may not want to spend too much time and effort perfectly tuning your model. A better use of time may be to investigate your features further. Feature engineering and selecting subsets of features can increase (or decrease) the performance of your model tremendously. This will take much more effort than plugging in numbers into a parameter grid but, in return, also further develop your understanding of the dataset and possibly discover new relationships between features.

## The Bottom Line

`GridSearchCV` is a useful tool to fine tune the parameters of your model. Depending on the estimator being used, there may be even more hyperparameters that need tuning than the ones in this blog (ex. K-Neighbors vs Random Forest). Do not expect the search to improve your results greatly. It may be more efficient to go back and explore your selected features or find other relationships between features to improve your model performance.


https://scott-okamura.medium.com/gridsearchcv-for-beginners-db48a90114ee
