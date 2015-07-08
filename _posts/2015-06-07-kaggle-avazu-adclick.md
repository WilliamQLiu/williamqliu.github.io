---
layout: post
title: Kaggle's Click-Through Rate Prediction
---

## {{ page.title }}

- - - -

__Summary__

Back in Feb 2015, I finished my first machine learning competition.  The competition was through Kaggle.com and sponsored by Avazu.  The goal was to determine the probability of whether someone would click on a mobile ad or not based on 10 days of their data.

I spent about 2 1/2 weeks doing the analysis and it was an incredible learning experience.  Mostly picking the right library, putting in a decent amount of time and effort, and tuning some parameters will get you in the top third.  The top ranked people put in exponentially more effort, so much that I doubt the $15k prize is financially worth it (especially when split among a team), but really people do these competitions more for just the knowledge.

__About the data__

The dataset was split into a training file (a little over 6 GB) and testing file (700 MB) sets.  Some of the field descriptions were openly shared (e.g. date), but most of it was anonymized and hashed (e.g. 'C19' with a value of 'dfhlsa8z').  The goal was to guess the probability of whether an interaction was a click (1) or non-click (0).  Say there was an interaction ID of '10000554139829200000', we could guess the probability (e.g. 0.218537).  The data looked like this:

    id    click   hour    C1  banner_pos  site_id site_domain site_category   app_id  app_domain  app_category    device_id   device_ip   device_model    device_type device_conn_type    C14 C15 C16 C17 C18 C19 C20 C21
    1.00001E+18 0   14102100    1005    0   1fbe01fe    f3845767    28905ebd    ecad2386    7801e8d9    07d7df22    a99f214a    ddd2926e    44956a24    1   2   15706   320 50  1722    0   35  -1  79

__My approach__

I started by exploring the data in an ipython notebook and loading the data into a pandas dataframe.  I looked at basic stats (describe, info) that gave me back quartiles, medians, and sums across all columns.  Since most of the data was anonymized, I didn't have much to refine except for some of the data types and the datetime into specific days and hours groupings (e.g. '14091123' means '23:00' on 'Sep 11, 2014' so I extract out the day and hour).  I used a correlation matrix to see if there were any obvious relationships between any variables; nope there weren't.

__Using scikit-learn__

Since I'm trying to predict a categorical variable (clicked or not), I figured Logistic Regression would be a good first try with some variables that looked promising.  The scoring was with a log loss so the smaller the score the better.  Here were the fields I picked:
  
    'time_day', 'time_hour', 'banner_pos', 'C18', clf = LogisticRegression()

Logistic Regression did decently (a score of 0.4391227), which I managed to beat the benchmark (.5 possibility for everything).  If I stopped here I would have finished 1340th out of 1605 teams.

I then tried an SGD Classifier with a log loss and some slight parameter tuning that did slightly better, netting me a score of (0.4303073).  This would have made me jump from 1340 to 1294th place.  Hmmm, it's doing slightly better, but not by much.

After a lot more parameter tuning, careful variable choosing (feature engineering), increasing my training set (by removing holdout), and increasing iterations, I ended up with my best sklearn score of (0.3976942), which would have been 1112th place.  I decided to change strategies and learn a new machine learning library.

__Using Vowpal Wabbit__

The Kaggle forums were really friendly and informative; the community focuses on learning and someone recommended the __vowpal wabbit__ library.  I ended up using __feature hashing__, which is an efficient way of vectorizing features.

Before I explain what feature hashing is, let's go over __term-document matrix__; it's a giant matrix where each __feature__ (i.e. word) is a column and a row consists of either a 1 (word was present) or 0 (word was not present).  Say we have the following text:

    "Will likes to watch movies with Laura"
    "Laura likes dogs"

This might get converted to a matrix like the one below.  As you can see, the matrix can get pretty large.

                               Will likes to watch movies with Laura dogs
    first_sentence             1    1     1  1     1      1    1     0
    second_sentence            0    1     0  0     0      0    1     1

__Feature hashing__ builds on top of the term-document idea by applying a __hash__ function to each word.  It then uses the hash values as feature indices and updates the vector.   Umm... so what does that mean?  The way I understand it is that feature hashing is like a __sparse matrix__ (i.e. doesn't need to represent the 0 values) and only says where the position of a value that is present.

The feature/word 'Will' is hashed to the value of something like '180798'.  You can check with the tools vw has like `vw-varinfo` to see the details.  Use the command: `vw-varinfo mydata.train`.  We now have index 1 (first_sentence) and a hash of 180798 with one occurrence.  Now we have a matrix of: mymatrix(1, 180798)=1

The feature/word 'likes' is hashed to the value of something like '98383'.  We now have index 1 (first_sentence) and index 2 (second_sentence) that has these features.  This results in 'mymatrix(1, 98383)=1' and 'mymatrix(2, 98383)=1'.  If there's more frequencies (e.g. first_sentence says 'likes' 3 times), we just modify the other side of the equation (=3)

The advantage to feature hashing is this lets us handle large amounts of anonymous features one line at a time (we don't need to construct the entire matrix).  When it gets really really large, there might be hash collisions (two distinct data have the same hash values).
 
__Results of Vowpal Wabbit__

 Anyways, Vowpal Wabbit gave incredible baseline results (.39723).  I used vowpal-wabbit through the command line (even though there was a Python library).  The biggest issue was getting the data to vw's format since it had its own specific formatting (that was slightly different than comma separated value files).

I tried the logistic loss function with variations of the number of passes, holdout, hash size, learning rate, and quadratic and cubic features.  I even spinned up an Amazon EC2 server to handle the maximum RAM that vowpal wabbit could handle (`-b 32`).  The EC2 had unexpected results.  At first I thought it would be easy to get all my data and code on there, but Kaggle stops you from downloading the data with `curl` or `wget` commands.  I even tried to fake my cookies by importing them as a text file, but it didn't work for some reason.  Finally I got a 'text based Linux web browser' called __Lynx__ that I installed to login to Kaggle, then download the data.  I could have transferred the data from my laptop, but that would've been really slow and the server was costing me per hour (which ended up being about $15 in charges).  Besides the data, Git made getting code easy.  Anyways, the hash collisions somehow helped the score so my EC2 server results came out worse.

__Final Thoughts__

I wish I started the competition sooner, but I'm pretty happy with learning a new library and even some unexpected things (who would've thought I'd learn about a text based browser called Lynx?).  Maybe next time I'll enter a competition where I can feature engineer more.  The people on the Kaggle forums were really collaborative and friendly, thank you for making machine learning a fun community.  My final score was .3926272 (459th place out of 1604 teams).  My command line instruction for vowpal wabbit was:

     vw click.train_original_data.vw --loss_function logistic -b 30 --passes=10 -c -k --holdout_off -l .1 -f click.model.vw
