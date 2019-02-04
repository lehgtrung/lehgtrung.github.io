---
layout: post
comments: true
title:  "Santander Product Recommendation"
excerpt: "My solution for the Santander Product Recommendation competition on Kaggle"
date:   2019-01-09 11:00:00
mathjax: true
---

*Under their current system, a small number of Santander’s customers receive many recommendations while many others 
rarely see any resulting in an uneven customer experience. In their second competition, Santander is challenging 
Kagglers to predict which products their existing customers will use in the next month based on their past behavior 
and that of similar customers. With a more effective recommendation system in place, Santander can better meet the 
individual needs of all customers and ensure their satisfaction no matter where they are in life.*

## Data Description

In this competition, we are provided with *1.5* years of customers behavior data from Santander bank to predict what new 
products customers will purchase. The data starts at *2015-01-28* and has monthly records of products a customer has, 
such as "credit card", "savings account", etc. We will predict what **additional** products a customer will get in the 
last month, *2016-06-28*, in addition to what they already have at *2016-05-28*. 
These products are the columns named: *ind_(xyz)_ult1*, which are the columns $$#25 - #48$$ in the training data. 
We will predict what a customer will buy **in addition to** what they already had at *2016-05-28*. 

|      Column Name      	| Description                                                                                                                                                                                                      	|
|:---------------------:	|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| fecha_dato            	| The table is partitioned for this column                                                                                                                                                                         	|
| ncodpers              	| Customer code                                                                                                                                                                                                    	|
| ind_empleado          	| Employee index: A active, B ex employed, F filial, N not employee, P pasive                                                                                                                                      	|
| pais_residencia       	| Customer’s Country residence                                                                                                                                                                                     	|
| sexo                  	| Customer’s sex                                                                                                                                                                                                   	|
| age                   	| Age                                                                                                                                                                                                              	|
| fecha_alta            	| The date in which the customer became as the first holder of a contract in the bank                                                                                                                              	|
| ind_nuevo             	| New customer Index. 1 if the customer registered in the last 6 months.                                                                                                                                           	|
| antiguedad            	| Customer seniority (in months)                                                                                                                                                                                   	|
| indrel                	| 1 (First/Primary), 99 (Primary customer during the month but not at the end of the month)                                                                                                                        	|
| ult_fec_cli_1t        	| Last date as primary customer (if he isn’t at the end of the month)  Customer type at the beginning of the month ,1 (First/Primary customer), 2 (co-owner ),P (Potential),3 (former primary), 4(former co-owner) 	|
| tiprel_1mes           	| Customer relation type at the beginning of the month, A (active), I (inactive), P (former customer),R (Potential)                                                                                                	|
| indresi               	| Residence index (S (Yes) or N (No) if the residence country is the same than the bank country)                                                                                                                   	|
| indext                	| Foreigner index (S (Yes) or N (No) if the customer’s birth country is different than the bank country)                                                                                                           	|
| conyuemp              	| Spouse index. 1 if the customer is spouse of an employee                                                                                                                                                         	|
| canal_entrada         	| channel used by the customer to join                                                                                                                                                                             	|
| indfall               	| Deceased index. N/S                                                                                                                                                                                              	|
| tipodom               	| Addres type. 1, primary address                                                                                                                                                                                  	|
| cod_prov              	| Province code (customer’s address)                                                                                                                                                                               	|
| nomprov               	| Province name                                                                                                                                                                                                    	|
| ind_actividad_cliente 	| Activity index (1, active customer; 0, inactive customer)                                                                                                                                                        	|
| renta                 	| Gross income of the household                                                                                                                                                                                    	|
| segmento              	| segmentation: 01 - VIP, 02 - Individuals 03 - college graduated                                                                                                                                                  	|
| ind_ahor_fin_ult1     	| Saving Account                                                                                                                                                                                                   	|
| ind_aval_fin_ult1     	| Guarantees                                                                                                                                                                                                       	|
| ind_cco_fin_ult1      	| Current Accounts                                                                                                                                                                                                 	|
| ind_cder_fin_ult1     	| Derivada Account                                                                                                                                                                                                 	|
| ind_cno_fin_ult1      	| Payroll Account                                                                                                                                                                                                  	|
| ind_ctju_fin_ult1     	| Junior Account                                                                                                                                                                                                   	|
| ind_ctma_fin_ult1     	| Más particular Account                                                                                                                                                                                           	|
| ind_ctop_fin_ult1     	| particular Account                                                                                                                                                                                               	|
| ind_ctpp_fin_ult1     	| particular Plus Account                                                                                                                                                                                          	|
| ind_deco_fin_ult1     	| Short-term deposits                                                                                                                                                                                              	|
| ind_deme_fin_ult1     	| Medium-term deposits                                                                                                                                                                                             	|
| ind_dela_fin_ult1     	| Long-term deposits                                                                                                                                                                                               	|
| ind_ecue_fin_ult1     	| e-account                                                                                                                                                                                                        	|
| ind_fond_fin_ult1     	| Funds                                                                                                                                                                                                            	|
| ind_hip_fin_ult1      	| Mortgage                                                                                                                                                                                                         	|
| ind_plan_fin_ult1     	| Pensions                                                                                                                                                                                                         	|
| ind_pres_fin_ult1     	| Loans                                                                                                                                                                                                            	|
| ind_reca_fin_ult1     	| Taxes                                                                                                                                                                                                            	|
| ind_tjcr_fin_ult1     	| Credit Card                                                                                                                                                                                                      	|
| ind_valo_fin_ult1     	| Securities                                                                                                                                                                                                       	|
| ind_viv_fin_ult1      	| Home Account                                                                                                                                                                                                     	|
| ind_nomina_ult1       	| Payroll                                                                                                                                                                                                          	|
| ind_nom_pens_ult1     	| Pensions                                                                                                                                                                                                         	|
| ind_recibo_ult1       	| Direct Debit           

## Objective
Specifically, the challenge was to recommend top $$7$$ products to each user in the test data. Recommendation was
evaluated using the *Mean Average Precision* at $$7$$ (MAP@7):

$$\begin{aligned} MAP@7 = \frac{1}{|U|} \sum_{u=1}^{|U|} \frac{1}{min(m,7)} \sum_{k=1}^{min(n,7)} P(k) \end{aligned}$$

where $$|U|$$ is the number of rows (users in two time points), $$P(k)$$ is the precision at cutoff $$k$$, $$n$$ is the 
number of predicted products, and  $$m$$ is the number of added products for the given user at that time point. 
If $$m = 0$$, the precision is defined to be $$0$$. For example, we recommend 

The intuition behind this scoring metric is that for every product we recommend and actually being bought by the user, 
the earlier the product is listed, the more point we are rewarded. We don't lose any point for recommending products to 
users that don't buy anything.

## Approach

The original requirement was to recommend **addtional** products that customers would buy in June, $$2016$$, a product is
considered to be additional if it is owned in June $$2016$$ but not in May $$2016$$. Given training data, I wanna
build a machine learning model to learn a mapping from customer data to products that they would newly own in the month
followed. From here, we've got $$2$$ strategies to build such a model:
* By a binary classifier that classify whether a product would be owned in the next month, regardless of being
newly owned or not.
* By a multi-class classifier that predict which product would be newly owned in the next month. If several products
being owned, we would choose a random one as our label.

Both of those models output the probability of being newly bought by a user for each product which can be used as 
a score to rank $$24$$ products. We can build many models of both types and do an ensemble to obtain the final results.
However, in this post, I select the second type as my primary model since it require less computing power which is limited
on my computer.

At first glance, I want to load the whole training data to my machine to do some exploratory analysis. But since the 
training data was too big to fit into $$8$$GB RAM memory, I have to think of a more memory efficient way to handle them. 
As every row in training data has a unique month, I use a simple bash script to divide the data into several files, 
each file contains only $$1$$ partitioned month data.

```console
foo@bar:~$ cat train_ver2.csv | sed "1 d" > train_no_header.csv # New training data file with header removed
foo@bar:~$ mkdir train                                          # Make a new folder named train
foo@bar:~$ awk -F\, '{print>"train/"$1}' train_no_header.csv    # Gather rows begining with the same pattern which is the date into a file, use gawk if u are on OSX
```
This results in several files in <code>train</code> folder such as: <code>2015-01-28</code> which contains data from 
Jan $$2015$$ and so on. As mentioned, we will build a multi-class classifier to predict newly added products, so, 
for each month, I construct a new file that contains user id and **new** products (products that he/she didn't have this
 month but next month) with the following code. 

```python
import pandas as pd
import numpy as np
from tqdm import tqdm
import csv

HEADER = ["fecha_dato", "ncodpers", "ind_ahor_fin_ult1", "ind_aval_fin_ult1",
          "ind_cco_fin_ult1", "ind_cder_fin_ult1", "ind_cno_fin_ult1",
          "ind_ctju_fin_ult1", "ind_ctma_fin_ult1", "ind_ctop_fin_ult1",
          "ind_ctpp_fin_ult1", "ind_deco_fin_ult1", "ind_deme_fin_ult1",
          "ind_dela_fin_ult1", "ind_ecue_fin_ult1", "ind_fond_fin_ult1",
          "ind_hip_fin_ult1", "ind_plan_fin_ult1", "ind_pres_fin_ult1",
          "ind_reca_fin_ult1", "ind_tjcr_fin_ult1", "ind_valo_fin_ult1",
          "ind_viv_fin_ult1", "ind_nomina_ult1", "ind_nom_pens_ult1",
          "ind_recibo_ult1"]

def get_next_date(year, month):
    """
    Get next year-month from current one, ex: 2015-12 -> 2016-01
    """
    month = int(month)
    year = int(year)
    assert year in [2015, 2016]
    assert 1 <= month <= 12  
    return {'year': str(year), 'month': "{:02d}".format(month + 1) } \
           if month < 12 else {'year': str(year + 1), 'month': '01'}
    
def get_additional_products(df):
    additional_products = []
    for _, row in tqdm(df.iterrows()):
        for product in HEADER[2:]:
            x, y = int(row[product + '_x']), int(row[product + '_y'])
            if y == 1 and x == 0:
                additional_products.append((row['fecha_dato_y'], row['ncodpers'], product))
    return additional_products
    
def get_additional_products_monthly(year, month):
    future = get_next_date(year, month)
    df_present = pd.read_csv('../data/train/' + "-".join([year, month, '28']),
                              header=None, names=HEADER, 
                              usecols=[0, 1]+list(range(24,48)))
    df_future = pd.read_csv('../data/train/' + "-".join([future['year'], future['month'], '28']),
                              header=None, names=HEADER, 
                              usecols=[0, 1]+list(range(24,48)))
    df = pd.merge(df_present, df_future, on='ncodpers', how='inner')
    df.fillna(0, inplace=True)
    return get_additional_products(df)
    
def to_csv(my_list, file_path):
    with open(file_path, 'w') as f:
        writer = csv.writer(f)
        writer.writerows(my_list)
            
dates = [(2015, "{:02d}".format(month)) for month in range(1, 13)] + \
        [(2016, "{:02d}".format(month)) for month in range(1, 6)]
for year, month in dates:
    additional_products = get_additional_products_monthly(year, month)
    to_csv(additional_products, '../data/train/additional-' + "-".join([year, month, '28']))
```
After this phrase, I've got $$14$$ files named <code>additional-x-y-28</code> where $$x,y$$ are year and months with 
respect to year and month from training data. Each file contains around $$32,000$$ lines which is just $$1/10$$ comparing
  with the original monthly data. Next, I want to do some exploratory analysis on this reduced data files to understand 
  the data and find new features that help.


