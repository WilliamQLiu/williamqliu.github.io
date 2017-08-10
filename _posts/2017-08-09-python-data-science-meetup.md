---
layout: post
title: Colorado Python Data Science Meetup
---


# {{ page.title }}


Meetup

## Reddit Extremist Finder

By: ?

Using reddit comments, determine if hate speech.

* Selecting Comments and Labeling Nuts or Not Nuts (240 people each)
* Clean Comments
* Words vs Comments using Term-Frequency Idf/Bag of Words
* NMF/LDA for Topic Modeling
* Created a 'Comment Count per Topic Vector'
* Went with Gradient Boost, Random Forest, Adaptive Boost Classifiers
* Results can be four categories: 'True Positive', 'False Positive', 'True
  Negative', 'False Negative'
* Plotted against ROC Curve

## Wildflower Finder

By: Jennifer Waller, Ph.D.

Use camera to determine what flower.

* Pl@ntNet app looks at photo, tells you what plant
* Pl@ntNet uses data from Western Europe, not very good for Colorado
* First Approach was to scrape Colorado Front Range wildflower images (n=651),
  create custom convolution neural network (.88 accuracy)
* Second Approach to use a fine-tuned pre-trained **ResNet50** model; deeper
  network = more features (~50 layers), avoids underfitting that happens in
  traditional deep networks. E.g. one layer might be texture, another color.
  Added additional custom layers
    * Model - fully connected layers (flatten, dense w/ 512)
    * Image Preprocessing helps, shift/tilt, change saturation
    * Results: Accuracy with random guessing (.09), Top 1 model accuracy on
      validation data (.97), misclassified 4/306
    * www.wildflowerfinder.com - built web app with Flask with predicted species and predicted accuracy

## Yelp Reviews

By: Dale Taylor

Investigating the Anna Karenina Effect in Yelp Denver reviews.

* Are all happy reviews alike? Are all unhappy reviews different?
* Took 200,000 reviews
* Can we predict a restaurant's reviews based on number of topics?
* Looked at closed restaurants too so there's no 'survival bias'
* Looked at number of topics
* The Gini coefficient is a way to measure income inequality
* Results
    * Skip waiters (topics with waiters scored lower scores)
    * Skip delivery (topics with delivery scored lower scores)
    * Be fast casual

## Intro to Deep Quality Networks

By: Tabor C Henderson

Intro to Deep Quality Networks (e.g. DeepMind plays Atari)

* Reinforcement Learning - it sucks; an agent does an action in an environment,
  interprets the state and reward
* DeepMind plays Atari, only knows that high score is better
* DQN Algorithm is pretty small
* Utilized 'experience replay' to avoid overfitting and local minima
* Neural Nets are a stochastic method of solving problems, meaning they're
  approximate
* A deterministic algorithm follows specific rules

## Solving LSAT Logic Games

Can we solve large text based questions with machine learning?

* A team of Stanford data scientists solved it
* Hardest part is Parsing Rules
* NLP Landscape:
    * Mostly Solved
        * Spam Detection
    * Somewhat Solved
        * Text Parsing
* Uses Stanford/Google's **Parsey McParseface** (2016)
* Translator using Google's **Seq2Seq** (what Google Translate is using)
    * Source Sequence (e.g Das ist mein Haus.) to Target Sequence (That is my
      House)
    * Models include Neural Net, then a Recurrent Neural Net, then an
      Encoder/Decoder, then LSTM/GRU, then Bidirectional encoding, and
      currently Attention (released around 2015)
        * Issue with NN were they can't do sequences well (e.g. many words back to back)
        * Issue with recurrent NN was reading v writing
        * Issue with Encoder/Decoder is forgets meaning context after about 7 words
        * Issue with LSTM/GRU is can't look ahead
        * Issue with Bidirectional encoding is single, fixed-length vector
          (heavy lift for a single vector)
        * Attention works pretty well because it allows putting emphasis on
          different words
    * You can use Seq2Seq even with Source Language as English to Target
      Language as Python
* Some say that NLP future isn't bright. Neural Nets are powerful, will probably
  replace
* Google uses **SyntaxNet**, part of a larger suite that includes **Parsey McParseface**

