---
layout: post
comments: true
title:  "Logistics Regression"
excerpt: ""
date:   2019-01-01 11:00:00
mathjax: true
---

### Introduction to classification
Classification is one of the most fundamental problem in machine learning. They are also very common in real life systems
that employ intelligence, such as:
* In mailing system, an email is automatically classified as spam or not spam.
* In credit scoring, classifying a customer is low\-risk or high\-risk.
* In social media analytics, classifying users comments as positive, neural or negative.
* Modern smart phones cameras detect and classify photos background (into some known background like text, flowers) to 
beautify the photos.

<p align="center">
<img src="/assets/logistics/classificationgmail.png" width="100%"/>

Figure 1.1: Example from gmail web client, emails are automatically categorized to Primary, Social and Promotions.
</p>


From examples above, An email or a photo is called an instance, data point, or observation,.. it's 
,by some way, vectorized to a vector $$x$$. Classification is a task of taking an observation and assign it to one of
 $$K$$ classes $$C_k$$ where $$k=1,..,K$$. Usually, these classes are disjoint so that each observation is assigned to 
 one and only one class. For example, an email is classified as spam \- not spam or classified as primary \- social \- 
 promotions but not spam \- social.
 
 <p align="center">
 <img src="/assets/logistics/classification.png" width="60%" height="60%"/>
 </p>
 <p align="center">
 Figure 1.2: Geometrically, emails are represented as a point in two dimensions with vertical axis as length and 
 horizontal axis as number of urls, classes are represented as x/o symbol. The dashed line is called decision boundary 
 or decision surface.
 </p>
 
 Today, we are going to learn about the most fundamental model for classification in machine learning called the 
 *Logistic regression*. It's very important to understand this model clearly, since it's the building block for the 
 Artificial neural networks which are highly applicable today.
 
### Logistic regression


 