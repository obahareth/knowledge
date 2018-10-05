# Overfitting

Overfitting is when a machine learning model is fitting a data set too exactly and will cause it to fail when new data is introduced to it. In this case, the model fails \(or simply has a high error rate\) to deal with anything outside of the data set it was trained on.

tl;dr It's as if the model memorized the data set and knows nothing else outside of it.



Possible solutions:

Split the data set into one used for training, and one used for validation. If the model generalizes well, it should have similar loss metrics across the training and validation data sets.

