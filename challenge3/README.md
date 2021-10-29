# VQA Homework

In order to solve the VQA homework, we analyzed the dataset we had, and decided to maintain the association given in the json files and utilize every couple of image-question as training data.
We took inspiration from a paper [1] on Visual Question Answering, which also worked on a dataset very similar to the one we were provided.

We used a very similar Model as the one presented in the paper, with a few changes, mainly in hyperparameters. Our model uses another pre-trained CNN network: InceptionV3; it was mainly chosen as it had trained weights on input images with 299x299 size (which was a higher resolution with respect to the 224x224 of the VGG network proposed in the paper) as we wanted to preserve as much details as possible, and the questions asked were very specific and also involved small objects.

We employed Dropout as a way to perform weight regularization (along with early stopping) in order to prevent overfitting.

A graphical interpretation of the model is available in the attached Jupyter Notebook.

No preprocessing was applied to the images other than the resizing to 299x299 size, as we did not want to interfere with the details and colors of the images which were also subjects (and possible answers) to the questions. We also did not perform data augmentation since we already had a very big and various dataset.
The text was preprocessed in different ways:

- It was striped of the question marks (which would have later increased the number of different words as they would have resulted in different tokens)
- It was changed to lowercase (for the same reason as the question marks)
- It was preprocessed with Porter Stemmer, to further decrease the number of different words (alias tokens in our case) which simplified nouns, verbs, and adjectives without changing the whole context of the phrase.

The answers were encoded as integers (with a mapping provided directly in the Kaggle challenge specifications) and we transformed them further into one-hot encoded labels [2].

Then Keras Tokenizer was fitted with all the aggregated words from the training and test set questions, and then every question was tokenized and padded to the maximum token length found in the longest sentence (word-wise).

We trained the network a first time keeping the feature layers from the InceptionV3 frozen, then we fine tuned training another time making those layers trainable.

The Network was trained with a Categorical Crossentropy loss (since we had labels with one-hot encoding), and we monitored the results with an accuracy metric (which was the one used for evaluation).
After hand-tuning hyperparameters (sizes and number of the inner layers) we achieved a ~62% accuracy score on the testset with similar results obtained ‘locally’ with the validation set we chose (which was a 20% randomly chosen sample from the training dataset).

<br><br><br><br>

[1]: Yin and Yang: [Balancing and Answering Binary Visual Questions](https://arxiv.org/pdf/1511.05099.pdf)

[2]: We had a problem (could have been a bug) while training with a custom defined generator (extending Keras Sequence class) without one-hot encoding the labels, and training with Sparse Categorical Crossentropy loss, which yielded an accuracy of 0% during the whole training process, so we changed to one-hot encodings and Categorical Crossentropy.
