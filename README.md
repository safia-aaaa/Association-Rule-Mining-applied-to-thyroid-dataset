# Association-Rule-Mining-applied-to-thyroid-dataset

# Project Title: Knowledge Discovery of Thyroid Diseases: Association Rule Mining Approach 

## Project Discrition
This project applies Association Rule Mining (ARM) to investigate the hidden patterns in cases diagnosed with hyperthyroid and hypothyroid. Jupyternotebook platform is used to process the thyroid data set provided by:	https://archive.ics.uci.edu/ml/datasets/thyroid+disease
Due to the nature of the dataset, important features were dropped to generate rules. In future scope, we hope to include Hormone measurements. 

## How to Install and Run the Project
Download JupyterNotebook with Python version 3. 
Download the Data Mining Project.ipynb file and the ThyroidDF.csv dataset in one file on the your PC. 
Type in file directory "cmd", then run "jupyter notebook" command in the cmd. 
Run the files.

 ## Prerequisite 
 install Pandas library. 
 install Seaborn library. 
 
 ## Code: 
 ###### Import OS:
The main purpose of the OS module is to interact with your operating system. The primary use I find for it is to create folders, remove folders, move folders, and sometimes change the working directory. You can also access the names of files within a file path by doing listdir(). 
 ###### Import Pandas:
pandas is a Python package providing fast, flexible, and expressive data structures designed to make working with “relational” or “labeled” data both easy and intuitive. 

 ###### Import seaborn
Seaborn Python data visualization library based on matplotlib. It provides a high-level interface for drawing attractive and informative statistical graphics.


 ###### mlxtend.frequent_patterns: 
A library consisting of useful tools and extensions for the day-to-day data science, we will import Apriori, FP-Growth and assocation rules as follows: 

from mlxtend.frequent_patterns import apriori
from mlxtend.frequent_patterns import fpgrowth
from mlxtend.frequent_patterns import association_rules

#### REading the csv file:
df = pd.read_csv("thyroidDF.csv") #read files
we apply several EDA methods to discover the dataset. 

#### Data Preprocessing: 
We check null values in each feature using the commnad: df.isnull().sum(). we drop TBG because it has many missing values. 
Also the numerical features, and the redundant features.
df.drop(['TBG'], axis =1,inplace=True)
df.drop(['TSH','TT4', 'T3','T4U','FTI'],axis =1, inplace=True)
df.drop(['query_on_thyroxine','query_hypothyroid','query_hyperthyroid','TSH_measured', 'T3_measured', 'TT4_measured', 'T4U_measured', 'FTI_measured', 'TBG_measured', 'patient_id', 'referral_source'], axis=1,inplace=True)

 ###### age feature 
from df.describe() earlier in the EDA, We see that age has max value of 65526. hence it appears to be 4 values which are higher than 100. we fill null and drop these four rows.

df[df.age > 100]
df['age'] = np.where((df.age > 100), np.nan, df.age)

 ###### sex feature
 if there is pregnant patient that has no sex value, we set the value to "F".
df['sex'] = np.where((df.sex.isnull()) & (df.pregnant == 't'), 'F',df.sex)

 ###### Target feature
Since there is 31 type of the diagnosis, we only decided to focus only on hyperthyroid and hypothyroid conditions. 
diagnoses = {
             'A': 'hyperthyroid', 
             'B': 'hyperthyroid', 
             'C': 'hyperthyroid', 
             'D': 'hyperthyroid',
             'E': 'hypothyroid', 
             'F': 'hypothyroid', 
             'G': 'hypothyroid', 
             'H': 'hypothyroid'}

df['target'] = df['target'].map(diagnoses)
df.dropna(subset=['target'], inplace=True) 

## Data Visulization
using Seaborn library, we plot the distribution of the two conditions based on age and sex. We can observe that rules generated will relate to hypothyroid, females, adults. Catplot() mehtod is used to plot the catgorical features. 

![image](https://user-images.githubusercontent.com/79324284/207425897-bbef7867-f0c1-402a-aefc-384481b1b6c8.png)


## One Hot Encoding 

since there is Categorical features we dummy code them using get_dummies method. Then Concatenate the new columns to the dataset. Sex and Target are done in a similar way as below:

dummies = pd.get_dummies(df['sex'],prefix='sex')
df = pd.concat([df,dummies],axis=1)
df.drop('sex', axis=1, inplace=True)

###### However, age will have one extra step which is cut  () method the covert the age into ranges. 
The range of ages was segmented into four bins: Children (0-14 years), Youth (15-24 years), Adults (25-64 years), and Seniors (65 years and over) Based on the Canada national statistical office. Link: https://www.statcan.gc.ca/en/concepts/definitions/age2 

## Data Visulization: 
Checking the correlation of the features using Seaboem Heatmap, we removed 'goitre,'hypopituitary' features that are badly correlated with other features. 
![image](https://user-images.githubusercontent.com/79324284/207426309-affdd60a-a159-4399-aec2-e3431684a04f.png)

## Association Rules Mining 
 ###### WE generate the frequent items using Apriori algorithm. with miniumum support of 0.01. Experimenting many thersholds, this value founded to give the best results. Reasons are that dataset is small and thyroid diseases are difficult to diagnose.
 frequent_itemsets = apriori(df, min_support = 0.01, use_colnames = True )
 
 ###### Then rules are generated with confidence value of 0.80 and sorted based on the confidence. 
conf_rules = association_rules(frequent_itemsets, metric = "confidence", min_threshold = 0.80)
sorted_rules = rules.sort_values(['confidence'], ascending = False)

###### Similarly The process is done but using FP-Growth algorithm. 
frequent_itemset=fpgrowth(df, min_support = 0.01, use_colnames = True)
conf_rules = association_rules(frequent_itemset, metric = "confidence", min_threshold = 0.80)

## Results:

Rules are in form of X ⇒ Y.  Where X is the antecedent and Y is the consequent. Hvaing X will lead to having Y. For Example, (tumor)⇒ (sex_F). This implies that having tumor means that patient will be female with confidence value of 0.964286. 

Done. 
Thank You! 

