---
layout: post
title:  resume CE - Jio Institute
full-width: true
---

## The problem of matching job descriptions and resumes using machine learning algorithms to output a Skill Match Index in percentage involves several complex tasks, including data cleaning and preparation, feature extraction, algorithm selection and training, and metric evaluation. It is a challenging problem that requires expertise in NLP, machine learning, and data science, as well as a deep understanding of the job market and hiring practices and we hope to increase the performance of the ATS system with help of new technologies in NLP.

## As the advancements in deep learning are outperforming humans, I have also applied one of the state of the art deep learning algorithm to achieve our goal.

## DATA SET DESCRIPTION

#### To tackle this problem I will be using a dataset titled __UpdatedResumeDataSet.csv__ which is available on Kaggle. This dataset only consist data points from IT domain and have 2 columns, i.e. categories(job description) and Resume. Reason I choose only the IT domain is because I want to first create a baseline model which could prove our technique to be beneficial or not.


## REAL-WORLD CHALLENGES AND CONSTRAINTS

In the practical world, there are no rigid time constraints for calculating the degree of match between job descriptions and resumes. This calculation can be performed daily, hourly, or after the submission deadline. Similarly, scalability is not a significant concern, as strict latency requirements do not exist. While interpretability of the model is not mandatory, it is desirable to have. To achieve interpretability, keywords can be extracted from the resume or LIME or SHAP can be used for explanations. The primary challenge we may encounter with the model is the possibility of missing out on suitable candidates if the model produces a low match value for a well-suited candidate. Conversely, if the model produces a high match value for an unsuitable candidate, we may have a large pool of unsuitable candidates.

We can fine-tune a model easily in places where we have massive amounts of relevant and labelled data. We can use a simple supervised fine-tuning approach. Unfortunately, these scenarios are few and far between. It is for this reason that existing models have this limited scope. So, what can we do?

## As we are applying deep neural network, we don't want to extract data by cleaning as it will be loss in information and let the model train with these biases, some benefits. of NN. We will apply some data pre-processing to create the data more structure.

The Idea is that we will fine-tune the model to produce task centric labelled dataset and produce sentence embeddings which are aligned to our given tasks and then use these embeddings in distance formula to score the distance.

** More analysis can be done at iterations to improve the accuracy.

## DATA PREPROCESSING
1. Drop NULL columns
2. Remove special character
3. Reduce text to 400 words/tokens
4. converted to jsonl to for easy data loading


## Deep learning technique

We will be fine-tuning the pre-trained model but we don’t have label data therefore we will be applying a mixture of unsupervised learning algorithm to generate labels and then score it to train our model.

These are the steps I followed to train our model where I will try to explain the sub-problem and the solution in each step.

1. Problem: We don't have labelled data.
   Solution: We will use the technique called Domain Adaptation with Generative Pseudo-Labelling (GPL). 
   Domain Adaptation vs. Unsupervised Learning :There exists methods for unsupervised text embedding learning, however, they generally perform rather badly. They are not really able to learn domain specific concepts. A much better approach is domain adaptation, Here you have an unlabelled corpus from your specific domain together with an existing labelled corpus.


2. Query Generation: For a given text from our domain, we first use a T5 model that generates a possible query for the given text. E.g. when your text is “Python is a high-level general-purpose programming language”, the model might generate a query like “What is Python”. You can find various query generators on our doc2query-hub.
    
```
    Input: Paragraph: ted the documentation to provide the disaster recovery processes. database administrator ceige havana, il september 2007 to october 2014 ceige, la havana cuba produced entity relationship data flow diagrams, database normalization schemata logical to physical database maps, and data table parameters. installed and managed database server created and monitored user accounts, user permissions, database extents and table partitions. implemented robust backup and recovery procedures based on data volatility and application availability requirements monitored and optimized system performance using index tuning, disk optimization, and other methods. monitored the size of the transaction log. managed databases on multiple disks using disk mirroring technology...

    output:
    Generated Queries:
    1: what is the definition of database administrator
    2: what did database administrator ceige
    3: what is a database administrator
```


3. Negative Mining: Next, for the generate query “What is Python” we mine negative passages from our corpus, i.e. passages that are similar to the query but don’t which a user would not consider relevant. Such a negative passage could be “Java is a high-level, class-based, object-oriented programming language.”. We do this mining using dense retrieval, i.e. we use one of the existing text embedding models and retrieve relevant paragraphs for the given query.

```
    output:
    Negative sampling
    1. what is python?
    2. what is database
    3. what did database administrator do
```


4. Pseudo Labelling: It might be that in the negative mining step we retrieve a passage that is actually relevant for the query (like another definition for “What is Python”). To overcome this issue, we use a Cross-Encoder to score all (query, passage)-pairs.


5. Training: Once we have the triplets (generated query, positive passage, mined negative passage) and the Cross-Encoder scores for (query, positive) and (query, negative) we can start training the text embedding model using MarginMSELoss.




