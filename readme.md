


  <h2 align="center">Hindi Open Domain Question Answering</h2>

 

<!-- TABLE OF CONTENTS -->
<details open="open">
  <summary><h2 style="display: inline-block">Table of Contents</h2></summary>
  <ol>
    <li>
      <a href="#about-the-project">About The Project</a>
      <ul>
        <li><a href="#built-with">Built With</a></li>
      </ul>
    </li>
    <li>
      <a href="#getting-started">Getting Started</a>
      <ul>
	      <li><a href="#installation">Installation</a></li>
        <li><a href="#setting-up-selenium">Setting up Selenium</a></li> 
              </ul>
    </li>
    <li><a href="#google-snippet-extraction-method">Google snippet extraction method</a></li>
    <li><a href="#paragraph-extraction-method">Paragraph extraction Method</a></li>
    <li><a href="#results">Results</a></li>
    <ul>
	      <li><a href="#classifier-results">Classifier Accuracies</a></li>
        <li><a href="#answer-classification-accuracies">Answer Classification Accuracies</a></li> 
    <li><a href ="#average-runtime-analysis">Average runtime analysis</a></li
              </ul>
    </li>

  </ol>
</details>



<!-- ABOUT THE PROJECT -->
## About The Project



Question answering (QA) is a computer science discipline within the fields of information retrieval and natural language processing (NLP), which is concerned with building systems that automatically answer questions posed by humans in a natural language
Our model pays emphasis on Open Domain Question Answering  for Hindi language.
While there are a plenty of such systems for languages like English, low resource languages like Hindi can hardly find a proper implementation of Open Domain Question Answering systems. This model aims to ease the efforts required to get satisfactory answer for a query made in Hindi language, spoken by almost 58% of the National Population


### Built With

* Python



<!-- GETTING STARTED -->
## Getting Started

To run the entire model, you would need a python environment.
We recommend the Google Collaboratory for ease of usage and speed.
Alternatively, you can also use the conda environment

### Installation

   ```bash
    pip install -r requirements.txt
   ```
<!--selenium-->

<div style="page-break-after: always;"></div>

### Setting up Selenium
---
After installing the requirements, the next task is to setup the selenium webdriver,
**Selenium WebDriver** is a web framework that permits you to execute cross-browser tests.
* While using **Google Collaboratory**, run the following code to setup the headless webdriver.
```python
!apt-get update # to update ubuntu to correctly run apt install
!apt install chromium-chromedriver  # installing chromium webdriver
!cp /usr/lib/chromium-browser/chromedriver /usr/bin
import sys
sys.path.insert(0,'/usr/lib/chromium-browser/chromedriver')
from selenium import webdriver
chrome_options = webdriver.ChromeOptions()
chrome_options.add_argument('--headless') #using headless chrome so there is no popup window generated for every query
chrome_options.add_argument('--no-sandbox')
chrome_options.add_argument('--disable-dev-shm-usage')
driver = webdriver.Chrome('chromedriver',options=chrome_options)
```
* To run the code on local machine:
	* Download the webdriver from the link [here](https://chromedriver.chromium.org/downloads) 
	* Next run the following code 
	 ```python
	 from selenium import webdriver
	 driver = webdriver.Chrome(<path_of_driver>)
	 ```

### Downloading pre-trained Question Classifiers (QC)
To ease the process and make it ready to  deploy, we have saved the pre-trained **classifiers, encoders** and **TF-IDF Vectorizer** in .pickle format.
Make sure to include these files in the same folder as the code file.
*  **Classifiers**
	* lr.pickle 
	* svm.pickle
	* mlp.pickle
* **vectorizer.pickle**
* **encoder.pickle**

Alternatively, you can also train your model and create a .pickle file from  the **model_creation.ipynb** notebook.

<div style="page-break-after: always;"></div>

### Downloading additional requirements

* **NLTK** punkt corpus:
		To download, run the code below:
	```python
		import nltk
		nltk.download('punkt')
	```
		
* **Spacy** trained English pipeline:
		To download, run the code below:
	```python
		import spacy.cli
		spacy.cli.download("en_core_web_sm")')
	```
Now that we have completed all the basic installations and downloads, we are all set to move forward.


# Google snippet extraction method

In the google snippet method, for a given query, we directly look for the answer in the Google search result page. The main components of a google search result page are

* Google Featured Answer
* Search result snippets 

If an answer is found in the Google featured answer, it is returned as the answer after translating to Hindi. Otherwise, NER is applied on the snippets to  find the correct answer.

**The main parts of this module are:**
	

 1. Translator
 2. Search snippet extractor
 3. Question Classifier
 4. Answer Extractor


To run the model,
1. Run all the cells in the notebook, **google_snippet.ipynb** 
2. Add and run the following code at the end to get the answer for the given user query
	* **query** : User Query
	* **num_results**: number of results to scrap snippets from
	* **classifier** : QC model (lr, svm, mlp)
```python
answer_extraction(query= "भारत के प्रधानमंत्री कौन है",num_results=10,classifier='svm')
```
The answer will be returned in Hindi.

<div style="page-break-after: always;"></div>

###  Getting answers for questions in bulk

To fetch answers for questions in bulk, 
* If the data is in a csv file:
	```python
	import pandas as pd
	test = pd.read_csv(file_path, names=['question'])
	answers = test['columns'].apply(lambda x: answer_extraction(x, num_results, classifier))
	```
* If the data is in list (queries):
	```python
	answers=[]
	for x in answers:
		answers.append(answer_extraction(x, num_results, classifier))
# Paragraph extraction method

In this method, we first request a search page for the User query using a search engine( bing or google).
The page is then scrapped to get the corresponding url links. The links are then scrapped for text paragraphs  and then the paragraphs are searched for the most relevant answer.

**The main parts of this module are:**
	

 1. Translator
 2. Url extractor
 3. Sentence Extractor
 4. Cosine Similarity
 5. Question Classifier
 6. Answer Extractor

To run the model,
1. Run all the cells in the notebook, **google_snippet.ipynb** 
2. Add and run the following code at the end to get the answer for the given user query. Specify the arguments according to your need
	* **query** : User Query
	*  **engine**: Search engine to use (google/bing)
	* **num_results**: number of results to scrap snippets from
	* **num_sentences** :Number of sentences to fetch (integer)
	* **classifier** : QC model (lr, svm, mlp)
	```python
	answer_extraction("भारत के प्रधानमंत्री कौन है",search_engine='google',results=10,num_sentences=40,classifier='lr')
	```

<div style="page-break-after: always;"></div>

  # Results
 
The entire model was trained on 1200 question answer pairs in Hindi. The testing was performed on 300 sentences
## Classifier Results

Three classifier models were used for the Question classification part. The classification accuracies of each are tabulated below:
|  **Classifier**| **Accuracy** |
|--|--|
| SVM |83%  |
|MLP|86%|
|LR|83%|

#
| Classifier | Correct Predictions|Incorrect predictions|
|--|--|--|
|Logistic Regression|262|38|
|SVM|283|17|
|MLP|292|8|

## Answer Classification accuracies

| Model | Partial Match | Exact Match|
|--|--|--|
| Google Snippet | 42% |40%|
|Google Paragraph|8%|4%|
|Bing Paragraph|4%|2.6%|


## Average runtime analysis

The average runtime for one query  has been tabulated for each model.
**Note:** 
*The analysis was performed on Google Collaboratory on a standard CPU runtime environment. The runtimes may change based on the machine specifications and also the internet speeds.*

|Model|Average runtime per query  |
|--|--|
| Google snippet | 5 seconds  |
|Google paragraph|18 seconds|
|Bing paragraph|14 seconds|















