---
layout: post
comments: true
title:  "Logistics Regression"
excerpt: ""
date:   2019-01-01 11:00:00
mathjax: true
---

### Introduction to classification
Classification is one of the most fundamental problem in machine learning. It is also very common in real 
life systems, such as:
* In mailing system, an email is automatically classified as spam or not spam.
* In credit scoring, classifying a customer is low\-risk or high\-risk.
* In social media analytics, classifying users comments as positive, neural or negative.
* Modern smart phones cameras detect and classify photos background (into some known background like text, flowers) to 
beautify the photos.

<p align="center">
<img src="/assets/logistics/spamclassification.png" width="100%"/>

Figure 1.1: Example from gmail web client, spam emails are detected automatically.
</p>


From examples above, An email or a photo is called an instance, data point, or observation,.. it's 
,by some way, vectorized to a vector $$x$$. Classification is the task of taking an observation and assign it to one of
 $$K$$ classes $$C_k$$ where $$k=1,..,K$$. Usually, these classes are disjoint so that each observation is assigned to 
 one and only one class. For example, an email is classified as spam \- not spam or classified as primary \- social \- 
 promotions but not spam \- social. In case the outcome we'd want to predict takes only two possible value,  we call 
 that **binary classification**; if there is more than 2 classes, the problem is called **multiclass classification**.
 
 <p align="center">
 <img src="/assets/logistics/classification.png" width="60%" height="60%"/>
 </p>
 <p align="center">
 Figure 1.2: Geometrically, emails are represented as a point in two dimensions with vertical axis as length and 
 horizontal axis as number of urls, classes are represented as x/o symbol. The dashed line is called decision boundary 
 or decision surface.
 </p>
 
 In this blog post, we are going to learn about the most fundamental model for classification in machine learning called the 
 *Logistic regression*. It's very important to understand this model clearly, since it's the building block for the 
 Artificial neural networks which are highly applicable today.
 
### Logistic regression
Before diving into logistic regression, we might ask why not just use linear regression for binary classification problem ? 
We could assume the two outcome is either $$0$$ or $$1$$ (without a loss of generality) and build a regression model on that data. 
Recall the predicted outcome of linear regression has the form:

$$\hat{y} = w^Tx$$

Then, if the predicted outcome $$\hat{y}$$ is greater than a threshold, says $$0.5$$ then $$\hat{y} = 1$$, otherwise $$0$$.

What's the problem with this approach? Recall in linear regression, the output is an approximation of the 
conditional expectation $$E[y|x]$$. With $$y \in \{0,1\}$$, $$\hat{y}$$ becomes an estimation of the probability 
of $$1$$ given input vector $$x$$: $$P(y=1|x)$$. On the other hand, some of our estimates might be beyond 
the $$[0, 1]$$ interval making them hard to interpreted as probabilities.   

Another drawback of using linear regression for classification is that this method cannot be applied when
 the number of classes exceeds 2 because we cannot imply the label as $$1,..,K$$ 
 for a number of classes $$K > 2$$, since the ordering actually matters. 
 For those reasons, we should a more appropriate method which is natural to solve the problem like
  the one we are going to study next.

 