---
layout: post
title:      "Module 4 Blog Post - Draft:"
date:       2019-10-12 13:19:49 +0000
permalink:  module_4_blog_post_-_draft
---


In Data Science, as in life, many times we’re left to play the hand we’re dealt. While the saying is applicable to a wide variety of scenarios, it is perhaps best applied to the task of making the best of an incomplete, or imbalanced dataset.  In a perfect world, every dataset would be massive, contain no missing values, and be ready to be fit for modeling right out of the box. Wouldn’t that be nice?

Because we live in the real world, the datasets we have available to us are often less than perfect. It’s the mark of a good data scientist to be flexible and understand how to use the data available to its fullest potential (maybe something about wringing blood from stone? Idk). One such “shortcoming” seen commonly in datasets is an uneven distribution in class distribution for the desired output, or target variable that we are seeking to classify.

On a recent project, I encountered such an issue when seeking to predict hospital readmission rates for diabetic patients in the United States in an attempt to bolster efficacy of preventative care, and ultimately improve diabetic patient prognosis.  To accomplish my task, I sampled a database containing 100,000 patient visits for patients with diabetes. 

The target multinomial target variable, readmission, was comprised of 3 classes. These classes represented patients that were: not readmitted to the hospital, readmitted in less than 30 days, and readmitted in more than 30 days. The distribution of the target classes was as follows:

￼

As seen by the bar chart above, the distribution of the target classes of the dataset is largely skewed. Of the ~100,000 records, more than half were not readmitted to the hospital, and less than 15% of patients were readmitted in less than 30 days.

￼# Enter your title here

The content of your blog post goes here.
