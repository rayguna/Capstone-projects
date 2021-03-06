---
title: "Capstone Project 1: IR Spectral Analysis of Organic Compounds via a Machine Learning Approach"
author: "Ray Gunawidjaja"
date: "March 25, 2018"
output: html_document
---

Abstract  
<p align="justify">
Organic compounds can be uniquely identified using an infrared spectroscopy. An infrared spectrum can be produced using a portable system that consists of an infrared light source and an infrared detector. However, intrepreting an infrared spectrum for a mixture of compounds is not straightforward. In this work, I attempt to use machine learning algorithms to correlate organic compounds spectral features with chemical functional groups.       
</p>

***

## Table of contents    
#### 1. Introduction   
#### 2. Data overview   
#### 3.  Machine learning approach  
#### 4.  Preliminary conclusions and next steps   


### Appendix 
#### Appendix A. Technical Fundamentals
#### Appendix B. Python Files
#### Appendix C. Data Wrangling
#### Appendix D. Inferential Statistics
#### Appendix E. Machine Learning¶
#### Appendix F. Deep Learning
#### Appendix G. Other Dependent Files


***

### 1.   Introduction

<i>1.1 Problem statement</i>  

<p align="justify">
The idea of a portable optical sensor for chemical identification is becoming widespred. This is because the fabrication of low-cost electronic devices that are lightweight, compact, and high-speed has become possible. For example, Si-Ware Systems can fabricate an infrared system in a chip size module (http://www.neospectra.com/products-overview/). This is in contrast to the traditional bench-top and bulky infrared system. The infrared signal (i.e., a spectrum) of a pure material is easy to interpret. On the other hand, the spectrum of a mixture of two or more materials is generally ambiguous. It requires a trained personnel to interpret the spectrum. The process can be tedious and prone to errors. Thus, there is a need to establish a routine algorithm that yields a reliable and consistent result. The goal of this project is to select a suitable machine learning model to interpret the infrared spectra of unknown materials with a good accuracy.   
</p>


<i>1.2. How does it benefit the client?</i>  

<p align="justify">
The client is an FTIR spectroscopy sensor manufacturer. The client desires a universal method for unambiguously identifying the chemical compounds of an unknown material from the measured FTIR spectra. The conventional approach used to interpret an unknown IR spectrum is to mathematically compare the unknown spectra against all possible spectra within the database (i.e., a spectral matching approach). The reference compounds with similar spectral features to the unknown material are ranked according to their similarity index. If it is done on a defined class of material, this process is very likely to yield an accurate result. However, for an entirely unknown material it will require a trained professional to then rationalize the result. The human interference is not necessarily consistent and error-proof. The proposed approach is to utilize machine learning algorithms. A successful model is expected to yield consistent results within minimum errors.   
</p>

<i>1.3. Data source</i>    

<p align="justify">
The list of required data sets are as follows:  

1. The main reference/training data set of over 40,000 spectra of known chemical compounds will be automatically downloaded from NIST website (http://webbook.nist.gov/chemistry/vib-ser/) with the aid of a python script.  

2. A group frequency table will be downloaded or web-scraped from a certain website to aid in chemical group assignment of occuring peaks in the FTIR spectra. There are a number of websites that carry this information, but the appropriate website will be selected for its reliable source of information and completeness.  

3. A list of representative FTIR spectra that have been properly assigned will be downloaded from NIST website (http://cccbdb.nist.gov); it will be used to verify the accuracy of the peak assignment that rely on the group frequency table.    
</p>

<i>1.4. Proposed approach</i>   

<p align="justify">  
The tasks for this project are as follows:    

1. Download the appropriate data sets from the sources listed above.  

2. Perform cluster analysis of the reference data set to reduce the data size to a reasonable number (e.g., by chemical elements, molecular weight).    

3. Standardize reference spectra (uniformize units, perform spectra treatments as needed, e.g., interpolation, smoothing).  
4. Elucidate the spectral features by correlating with information provided in the group frequency table and utilizing EDA. Verify the accuracy of the peak assignment by using representative data from fully assigned spectra.   

5. Develop models for materials identification based on a combination of spectral matching algorithms and machine learning tools (e.g., neural network, cluster analysis).  

6. Select relevant test data sets that range form simple to hard and test the model. Improve the model as needed.  

7. Build a user friendly GUI (if time allows).  

8. Deploy application and present results.  
</p>

### 2. The dataset
<i>2.1. Basic principle</i>   

<p align="justify">
The mid-infrared absorption spectrum of organic materials can be correlated with the functional groups that make up its molecular structure. The knowledge of the material's functional groups can be used to derive its molecular structure. To name a few, this knowledge is useful for materials identification in forensics science, reverse engineering of products, or for the purpose of correlating the material's properties to its chemical structure.    

Please refer to <b>Appendix A</b> for the definition of the various technical terminologies and for more detailed technical fundamentals. Reusable functions that are used to process the data are located in my_jcamp.py and common.py python files. Please refer to <b>Appendix B</b> for more details.  
</p>


<i>2.2. A general overview of approach</i>   

<p align="justify">
<b>Figure 2.1</b> below summarizes the entire process involved in this work. The steps are as follows:  

- The first step is taking the NIST chemical list that contains over 70,000 chemicals. Data wrangling is performed on the data to yield a shorter chemicals list. The shorter list contains no null entries and include only a list of organic chemicals.   
- Next, inferential statistics is performed on the data and representative list of chemicals that represent certain organic functional groups are selected.   
- The corresponding infrared spectra of this chemicals list are downloaded from NIST website. This data serves as our dataset. 
- Subsequently, machine learning and deep learning models are considered. The best model is selected.  
- The final model is expected to be able to extract the functional groups that are present in an unknown IR spectrum with a high accuracy.   
</p>

<img src="data/Fig 2.1. Flowchart.png" width="400"><br>
<b>Figure 2.1.</b> A flowchart summarizing the processess carried out in this work.

*2.3. Data wrangling*   

<p align="justify">
Below shows a portion of the starting NIST chemical list. It is seen that the list consists of just three columns: chemical name, chemical formula, and CAS number. CAS stands for chemical abstract service, which refers to a unique identification number. Note that any of these fields may contain NaN. 
</p>

```python
#import pandas
import pandas as pd

#load NIST_chemicals_list.csv and inspect
pd.read_csv('data/NIST_chemicals_list.csv', names=['Name','Formula', 'CAS']).head() 
```
<img src="data/Table 1.png"><br>
<br>

<p align="justify">
The following summarizes the data wrangling steps, some findings, and the outcome:

- All rows that contain null entries in any of the three columns are deleted.<br>
- Only the list of compounds that are non ions, non radicals, and non-polymeric are retained. <br>
- Only the list of organic compounds containing the following elements: C, H, O, N, S, P, F, Cl, Br, and I are retained. <br>
- Each entry in the final DataFrame has a unique CAS number, but there are duplicate names and isomers and allotropes.<br>
- Shortened the NIST chemical list from 72,618 to 36,416. <br>
- The shortened DataFrame has been exported into a csv file called NIST_chemicals_list_organic.csv. <br>

See <b>Appendix C</b> for details.

The data wrangling step yields a shortened chemicals list with two additional columns (shown below):  

- Elements: a list of the unique elements in the compound. This list can be used to check whether the compound is organic or not (i.e., organic compounds contain just a handful of elements in the periodic table). <br>
- Mw: the total molecular mass of the compound. This is proportional to the size of the molecule.<br>  


```python
#load NIST chemicals_list_organic.csv and inspect
pd.read_csv('data/NIST_chemicals_list_organic.csv').head() 
```
<img src="data/Table 2.png"><br>

<br>


<i>2.4. Inferential statistics</i>

<p align="justify">
Taking the shortened chemicals list with the two additional columns, I perform inferential statistics. The following summarizes the efforts, some findings, and the outcome:

- The calculated Mws are accurate within 10% with respect to those posted on NIST website. <br>
- The distribution of Mws are generally not normal with the median being in the 100-200 range. <br>
- The shortened NIST chemicals database mostly consist of the following functional groups:'ester', 'ketone', 'alcohol', 'alkane', 'alkene', 'amine', 'aldehyde', 'acid', 'halide', 'thiol', and 'benzene'.  <br>
- IR spectra (in jcamp format) are downloaded to represent each of these group. <br>
- The Pearson correlation coefficient of the representative spectra from each group reveal that the alkane spectra have a high correlation, whereas the halides have a low correlation. <br>
- Generally, there is very little correlation between spectra, even within the same group. For spectra that have a high correlation, the features are expected to be mostly identical. <br>
- Probably, we cannot rely simply on the overall shape of the spectrum to distinguish functional groups. <br>   
- For the purpose of identifying unknown compounds from its IR spectra, we therefore need to undertake features selection. <br>
- One specific approach is to correlate each functional group with specific regions in the spectrum.<br>
- There is also a need to utilize more sophisticated algorithms (i.e., machine and deep learning algorithms) to more effectively correlate the chemicals' functional groups to its distinct features.<br>  

See <b>Appendix D</b> for details.
</p>
<p align="justify">
In most cases, little linear correlation is seen between spectra within the same group. This justifies the use of the more advanced machine learning tools that are available in Scikit-learn. This step yields the needed dataset. Each row in the dataset represents a single infrared spectrum. Each spectrum is labelled as one of ten functional groups (see below).
</p>

```python
pd.read_csv('data/NIST_selected_organic_spectra.csv').head()
```
 
<img src="data/Table 3.png"><br>
<br>

### 3.  Machine learning approach  

To utilize the dataset in Scikitlearn, first I convert the functional group labels into MultiLabelBinarizer. The labels are as follows:

<img src="data/Table 4. MultiLabelBinarizer.png">
<br>  

*The above table is generated using an online markdown tables generator https://www.tablesgenerator.com/markdown_tables  

For instance, a multilabel binarizer for an ester compound is given by [1,0,1,1,0,0,0,0,0,0]. The label translates to a compound that contains the fragments C-H, C=O, and C-O.   

Using the prepared dataset, the following summarizes the subsequent efforts, some findings, and the outcome:  

- Five machine learning models that are suitable for multilabel classification problems (http://scikit-learn.org/stable/modules/multiclass.html) are assessed (i.e., DecisionTreeClassifier, DTC; ExtraTreeClassifier,ETC; ExtraTreesClassifier , ETC_E; KNeighborsClassifier ,KNC; and RandomForestClassifier. RFC). Below compares the accuracy comparison between the five models.

<img src="data/Fig 3.1. compare_models.png"><br>

<b>Figure 3.1.</b> Algorithm comparison.

- Initial screening reveals that DecisionTreeClassifier better than the other four algorithms with an accuracy score of about 55%. 

- Next, I apply principal component analysis (PCA) to the data. Figure 3.2 shows the plot of % Variation versus number of components. It is seen that the optimum number of components - lie approximately between 20 and 40.  

- The pca model is saved to a file called pca_model.sav for later use. The pca-transformed data, along with the multilabel binarizer is saved to a file called df_X_PCA_y_label.csv.  

<img src="data/Fig. 3.2. n_components_plot.png"><br>
<b>Figure 3.2.</b> A plot of % Variation versus number of components, n_components.  

- I then utilize dimensionality reduction techniques (i.e., PCA) along with DecisionTreeClassifier to optimize the parameter in the model.The best score that is achieved is 59% (Components = 20 , max_depth = 10). This is a very modest score.  
- By carrying out k-fold cross-validation, the uncertainty in the score for DecisionTreeClassifier is 56.28 ± 5.79%.
- To further improve the model accuracy, next I consider a neural network model to achieve an accuracy score that is better than 56.28 ± 5.79% (see below).  

See <b>Appendix E</b> for detailed analysis.  


### 4.  Neural network approach  

To apply a neural network model, I use the saved pca-transformed data, along with the multilabelbinarizer (i.e., df_X_PCA_y_label.csv). I also load the saved pca model (i.e., pca_model.sav) to transform a new set of validation data. Using the prepared dataset, the following summarizes the subsequent efforts, some findings, and the outcome:  

- First, I feed the dataset into a baseline neural network model that consists of an input layer and an output layer with n components as the input, 10 as the output (this corresponds to the number of multilabelbinarizer), and 128 neurons. The n components is varied between 20 and 40.  

- An accuracy score of 94.81% is achieved for n component=40. This is a significant improvement from the machine leraning approach, where only an accuracy score of 56.52 ± 8.64% is obtained using an DecisionTreeClassifier.  

- Next, I refine the hyperparameters by assessing a deeper and wider neural network model. I also carry out a cross-fold validation run to get the score's uncertainty. The best score is 86.3±4.9% with the following neural network architecture, one hidden layer and 512 neurons.
Finally, I applied a model checkpoint on a 100 epoch runs. The parameters that correspond to the best accuracy score during an epoch run is saved. The saved filename is called weights-improvement-02-0.86.hdf5.  

- I apply the final model to a validation dataset. The accuracy score range between 60 and 100%.  

- The model seems to perform badly in identifying N-H and O-H fragments.   

- A larger number and a more precise training data are required to improve the model.   

See <b>Appendix F</b> for detailed analysis.  

### 5. Model deployment  

In the intended application, the user will interact with the program using a graphical user interface (GUI). Below demonstrates a simple design for a proof-of-concept. The description of the GUI (<b>Figure 5.1a</b>) is as follows:  

1. The GUI allows user to locate and open a .jdx file by clicking on the button "Open and predict spectrum".  

2. The spectrum will be treated for baseline, normalization, and unit conversion. 

3. The treated spectrum is then analyzed for the presence/absence of the ten functional groups.  

<b>Figure 5.1b</b> shows an example of the output.

\(a)<br>  
<img src="data/Fig. 5.1a. A_simple_GUI.png"><br>  

\(b)  <br>
<img src="data/Fig. 5.1b. GUI_output_example.png"><br>  

<b>Figure 5.1.</b> (a) A simple GUI for model deployment. (b) An example of output.  


### 6. Conclusions and future works

Conclusions:  

- By utilizing a neural network model, the accuracy is improved from about 60% to about 90%.    

- The final model consists of one hidden layer and 512 neurons.  

- A validation dataset is applied to the final model. An accuracy score between 60 and 100% is obtained. While the model is trained using compounds that belong to a single functional group, the validation dataset contains compounds that consist of multiple fragments.   

- The model seems to perform badly in identifying N-H and O-H fragments.   

- A larger number and a more precise training data are required to improve the model.   

Some recommendations for future works are as follows:  

- A more accurate model may be obtained by utilizing: a better quality dataset, a larger amount of training dataset, and a more precise labeling scheme.  

- A more robust model can be generated using a convolutional neural network (CNN). 

- Using CNN, it is possible to use a raw dataset as an input (i.e., the spectral pre-treatment steps that consists of a baseline subtraction and normalization may be skipped.  

- A different metric may be utilized to monitor the model's accuracy during optimization runs.  
 
- A more elaborate GUI can be designed for a user friendly utilization of the model.  

***


## Appendix

### Appendix A. Technical Fundamentals  
Refer to the accompanying file: Appendix A. Technical fundamentals.ipnyb

### Appendix B. Python Files  
Refer to the accompanying file: Appendix B. Python Files.ipynb, as well as my_jcamp.py and common.py.  

### Appendix C. Data Wrangling  
Refer to the accompanying file: Appendix C. Data Wrangling.ipnyb  

### Appendix D. Inferential Statistics  
Refer to the accompanying file: Appendix D. Inferential Statistics.ipnyb

### Appendix E. Machine Learning  
Refer to the accompanying file: Appendix E. Machine Learning.ipnyb  

### Appendix F. Deep Learning  
Refer to the accompanying file: Appendix F. Deep Learning.ipnyb  

### Appendix G. Other Dependent Files  
G1. csv files:  
- data/NIST_chemicals_list.csv  
- data/NIST_chemicals_list_organic.csv  
- data/NIST_selected_organic_spectra.csv  

G2. py files:  
- common.py  
- my_jcamp.py  

G3. jcamp files   
- jcamp files with extension .jdx are downloaded from the NIST website (https://webbook.nist.gov/chemistry/)  
- downloaded jcamp files are stored in the folder called reference. Due to the large number of files, these are not included in this Github project.  
- validation dataset with extension .jdx are also downloaded from NIST website and are placed in a folder called validation.

G4. List of figures and tables   
- all figures and tables are saved in a png format and are placed in the data folder.   
