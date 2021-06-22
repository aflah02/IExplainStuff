# Racial Bias in Hate Speech and Abusive Language Detection Datasets

[Original Paper](https://arxiv.org/abs/1905.12516)

## Abstract

- The authors essentially recognised the inherent bias in the types of dataset mentioned in titles. 
- When dealing with tweets with african american english they are much more likely to be recognised as hateful over american english tweets.
- This bias can be detrimental as then the trained models will flag an African American User much more just because of the English they use when the sentiment might not be as negative as the model thinks.
- This means this may flag the victims of abuse instead of protecting them

## Introduction

- Some works have shown bias in trained models which are caused due to bias in the data the model trains on.
- Bias may reduce the accuracy rendering the model unable to detect abuse and in the worst case might even discriminate against the very people it was built to protect.
- The paper studies models trained on Standard American English (SAE) and African American English (AAE) datasets all of which use data scraped from Twitter.
- The authors used Bootstrap Sampling (Bootstrapping is any test or metric that uses random sampling with replacement (e.g. mimicking the sampling process), and falls under the broader class of resampling methods) to estimate how many tweets fall in each class according to the model.
- Bias was observed in all classes with AAE tweets being flagged more than SAE tweets.
- Keeping out certain words does reduce the bias significantly however it still persists and is present in the trained model.

## Related Works 

- Bias often causes wrong predictions as seen with facial recognition technology. They perform far worse for darker-skinned women as compared to a white male face. This is due to the lack of data for the former when compared to the latter.
- Research also shows in unsupervised learning the embeddings learnt also contain biases which persist even after removal.
- Youtube captioning system also works far worse for women.
- Many classifiers even classify AAE as non english.
- Although it is difficult to find a perfect model but still we should be concerned if the model has inherent bias against a group by the virtue of the data it was trained on.
- Annotation Scheme and Annotator Identity manipulation also helps kin reducing bias.
- Google's Perspective API Classifier is an example which was trained on Wikipedia talk comments and poorly classifies even a simple sentence referring to homosexuals in a normal fashion as toxic since a majority of data which it was trained on was biased and saw those words in the wrong light.
- Even Hate speech detectors work very poorly when female identity terms are present.

## Research Design

### Hate Speech & Abusive Language Datasets

- 

