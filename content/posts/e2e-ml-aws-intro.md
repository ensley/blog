---
title: "End-to-End Machine Learning with AWS: Part 1"
description: "An introduction"
date: 2022-03-07T21:52:23-07:00
draft: true
tags:
  - End-to-End Machine Learning with AWS
---
  
## An Introduction

Yes, it's ANOTHER goddamn "how to be a data scientist" blog post.

Maybe it's just the corner of the internet I tend to find myself in, but it seems like data science people love nothing
more than telling other people how to start learning data science. Learn SQL. Come up with a personal project to 
work on. Practice coding interview questions, learn Pandas, study this flow chart of which ML algorithms to use for 
some reason. Okay, fine. (The tendency of these posts to never say "you should probably know some math in order to 
justify your work" is concerning to me, but that's not the point here.)

The flood of blog posts in that particular style has slowed somewhat in recent years as the data science craze has 
taken a backseat to the new hotness: deep learning and AI. And no, those aren't just a different branch of the same 
field - if they were, then why are their names so cool and futuristic? Anyway, despite the slowdown, it is still 
difficult to sift through and find helpful information on a very important topic: **what does it look like to not just 
develop a data science solution to a business problem, but to actually put that solution into production in such a 
way that coworkers or clients can interact with it and benefit from it?**

Too much of the data science material on the Internet would have you believe that the end product of your analysis is a 
Jupyter notebook. Tons of workshops and examples out there will get you to the point of having a script that can 
process data, train a model on your local computer and output predictions, and then just end there as if it's 
mission accomplished. And depending on what you're doing, maybe that is sufficient. But in many real-world settings, 
you might need a way to feed a data point into your model on-demand. You might need to re-train your model 
periodically on new data to prevent its accuracy from degrading. And crucially, you might need a way to enable a 
non-data scientist to do this themselves at their convenience. This is for your own benefit as much as theirs - they 
don't want to have to wait around every time they need a new model output until you have the time to help them out, 
and you don't want to try to come back to your project in 18 months and have to remember all the details of the 
project. At the same time, simply emailing them your Jupyter notebook isn't good enough. There's an excellent chance 
that they've never used Python before, or at the very least have never had a reason to install it on their machine, 
and they will not appreciate you asking them to go through all the setup *and* learn enough Python to be able to 
plug their new data into your notebook with no issues. They don't have time for your nerdy nonsense, just get them 
the results.

From an instructional point of view, this mentality of Jupyter notebooks being the end product is understandable. 
After all, the appealing thing about data science to most people that practice it is the model building itself - 
experimenting with different modeling approaches, trying to engineer features that drive better performance, and so 
on. Once we're done writing the code to do all that good stuff, why bother fussing with any of the 
productionalization stuff? This mentality even shows up in many official examples of cloud ML tools. AWS has a 
[Github repository full of SageMaker examples](https://github.com/aws/amazon-sagemaker-examples), but almost all of 
them are - you guessed it - notebooks. Notebooks work just fine with SageMaker, but they are difficult to automate 
and very difficult to share and track changes in version control, which are two big reasons to use the cloud in the 
first place!

Recently, I needed to provide a full end-to-end data science solution for a team that I work with often. Machine 
learning was a big aspect of the project, but it was far from a one-and-done analysis. The team needed to be able to 
periodically supply their own datasets and visualize the model outcomes against the actual data. They needed to see 
the results more or less as soon as they provided them, and they needed everything to be easily accessible in a web 
browser, rather than having to install any software. I knew I wanted to use AWS for the architecture of the solution,
but I found it very difficult to find resources that would help me build it right: defining as many things as 
possible in code, making it as repeatible and error-proof as possible, and keeping myself out of Jupyter 
notebook-land.

Eventually, I got everything working, but I kept thinking things could have gone much more smoothly if I had been 
able to find a good explanation of how the pieces ought to fit together along the way. So I decided to write a 
series of blog posts that walk through my use case in detail. I will touch on SageMaker Pipelines, and how to get 
SageMaker to interact with a completely custom model implemented in R. I will go over the website I created using 
Flask to allow users to upload and validate their data before it is supplied to the trained model, although I am 
very much not a web developer so bear that in mind. And I will illustrate how to provision all of these resources 
with the [AWS CDK](https://docs.aws.amazon.com/cdk/v2/guide/home.html) and avoid the console as much as I can. I 
work mostly in Python and R, so this will be a very Python-focused guide.

My goal is to write the material that I wish I had been able to find when I started this process, and maybe someone 
will find some part of it useful for their own work.

[Next: Defining the business requirements]({{< relref "e2e-ml-aws-overview" >}} "Defining the business requirements")
