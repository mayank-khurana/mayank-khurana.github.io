---
layout: post
title: Copy editing automation : Digiscape
full-width: true
---
![_config.yml]({{ site.baseurl }}/images/Elsevier.png)
<p>So, there is a journal and research paper publication company Elsevier. They receive a copy of journals from authors and these journals have to go through various stages of copy editing tasks before getting published. Now, all these tasks were done manually previously and we were to automate some of the steps which could reduce the processing time, error and manually.&nbsp;</p>
<p>The problem was divided into different stages</p>
<ol>
    <li>
        <div>The research paper/journal is to be approved or not:-</div>
        <ul>
            <li>
                <div>This is based on hard-coded rules about (100) and Intensive regex and python programming are used to solve. (with the help of an SME)</div>
            </li>
            <li>
                <div>Classifying type of journal (i.e maths, chemistry, medical) using some ML model.</div>
            </li>
            <li>
                <div>Classifying type of language (British English, American English)</div>
            </li>
        </ul>
    </li>
    <li>
        <div>Data: Set of labelled documents/research papers (1000&rsquo;s articles already processed manually) in XML format which helps us to pre-process chemical names/mathematical formulas into text using different libraries like gensim &amp; nltk or by tag names. Like mathematical formulas, XML typically follows the same tags.</div>
    </li>
</ol>
<p>Explaining the tasks</p>
<p>Classifying type of journal (i.e maths, chemistry, medical) -</p>
<ul>
    <li>
        <div>Real world/Business Objectives and Constraints :&nbsp;</div>
        <ul>
            <li>
                <div>The cost of misclassification can be very high</div>
            </li>
            <li>
                <div>Want the probability of the classification task so, that we can set our own threshold</div>
            </li>
            <li>
                <div>No strict latency concerns.</div>
            </li>
            <li>
                <div>Interpretability is partially important.</div>
            </li>
        </ul>
    </li>
    <li>
        <div>Mapping the real-world problem to an ML problem</div>
        <ul>
            <li>
                <div>Type of Machine Learning Problem: multiclass classification problem</div>
            </li>
            <li>
                <div>Performance Metric</div>
                <ul>
                    <li>
                        <div>Multi Log-loss(minimize to 0 or max is infinity)</div>
                    </li>
                    <li>
                        <div>Confusion matrix and f1 score, true positive rate</div>
                    </li>
                </ul>
            </li>
            <li>
                <div>Distribution of data points among output classes: was not balanced therefore apply data imputation &amp; weights while classifying</div>
            </li>
            <li>
                <div>Feature cleaning(using various NLP tasks) &amp; made more features using different techniques</div>
            </li>
            <li>
                <div>Interpretability</div>
            </li>
        </ul>
    </li>
</ul>
<p>Data: In XML format, parse using beautiful soup, and extract features from it</p>
<p>Some features</p>
<ul>
    <li>Text (pre-processed)<ul>
            <li>Remove stopwords</li>
            <li>Remove any tags</li>
            <li>Lower-case</li>
            <li>Remove new lines</li>
            <li>Keep only text, numbers and formulas (as XML will convert it into text)</li>
            <li>Remove other special char.</li>
            <li>Decontractions I&rsquo;ll to I will</li>
            <li>Remove person name using chunking(type person Noun), tree (new york to new_york)</li>
        </ul>
    </li>
    <li>Author names: styles of given name / only initials / &nbsp;full names&nbsp;</li>
    <li>Address: corresponding address, institution address, private. (sometimes institutes play a very important role to tell what type of journal)&nbsp;</li>
    <li>Images involved or not/what type of images</li>
    <li>Chemicals names(involved or not) - chemdata extractor</li>
</ul>
<p>&nbsp;</p>
<div>Data processing&nbsp;</div>
<ul>
    <li>
        <div>Categorical + text(tf-idf) bi, uni grams. Tf-IDF with word vectors i.e tf-idf* trained weights</div>
    </li>
</ul>
<div>Training</div>
<ul>
    <li>
        <div>Hyperparameters that give the highest AUC value</div>
    </li>
    <li>
        <div>K fold, grid search, random search</div>
    </li>
    <li>
        <div>Split data (stratified sampling)</div>
    </li>
    <li>
        <div>KPI, to reduce avg. LOG LOSS (0, INF) because it penalises false classification as the log is a monotonically increasing function</div>
    </li>
    <li>
        <div>Models multiclass </div>
        <ul>
            <li>
                <div>Base model: multinomial Naive Bayes log-loss 0.6 (place smoothing adding alpha to unknown words)</div>
            </li>
            <li>
                <div>Knn (doesn&rsquo;t work because of the curse of dimensionality and no interpretability)</div>
            </li>
            <li>
                <div>Logistic reg. (works well with high dim.) &nbsp;log-loss 0.37</div>
            </li>
            <li>
                <div>Logistic reg. (class balance) 0.34</div>
            </li>
            <li>
                <div>Linear SVM(because of large dimension, interpretable) 0.37</div>
            </li>
            <li>
                <div>DT (high dimensionality failed) overfit</div>
            </li>
            <li>
                <div>RF gave the same results as SVM</div>
            </li>
            <li>
                <div>Stacking(failed because of fewer data) [RF, LR, SVM] then apply LR - 0.4</div>
            </li>
            <li>
                <div>Voting classifier(RF,LR,SVM) - 0.3</div>
            </li>
            <li>
                <div>LSTM(AUC = 0.8) - log loss - 0.28</div>
            </li>
            <li>
                <div>Remove high and low IDF values from the data</div>
            </li>
        </ul>
    </li>
</ul>
<div>*But because of interpretability, we have to go with logistic regression&nbsp;</div>
<p>&nbsp;Classifying type of language (British English, American English)</p>
<ul>
    <li>
        <div>The text data is a combination of N-gram features, Term Frequency-Inverse Document Frequency (TF-IDF) weighting, In the TF-IDF weighting process, a threshold of 2,0 in the Document-Frequency (DF) is given. The classification process is carried out using Support Vector Machine (SVM) algorithm with a linear kernel and the best accuracy obtained is 90 %.</div>
    </li>
</ul>

<iframe
	src="https://multimodalart-mariogpt.hf.space"
	frameborder="0"
	width="850"
	height="450"
></iframe>
