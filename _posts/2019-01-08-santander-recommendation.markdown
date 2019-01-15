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
According to the competition's home page, the scoring was evaluated using *Mean Average Precision* at $$7$$ (MAP@7):

$$\begin{aligned} MAP@7 = \frac{1}{|U|} \sum_{u=1}^{|U|} \frac{1}{min(m,7)} \sum_{k=1}^{min(n,7)} P(k) \end{aligned}$$

where $$|U|$$ is the number of rows (users in two time points), $$P(k)$$ is the precision at cutoff $$k$$, $$n$$ is the number of predicted products, and 
$$m$$ is the number of added products for the given user at that time point. If $$m = 0$$, the precision is defined to be $$0$$. 

That means you can recommend $$1,2,3,...$$ upto $$7$$ products for every user in test set. Obviously, we should always recommend $$7$$ 
products since it'll always give us higher score. The intuition behind this scoring metric is that for every product we recommend, the earlier 
the item is listed, the more point we are rewarded. We don't lose any point for recommending products to users that don't buy anything.

## Approach
Although there's *"Recommendation"* in the name of the competition, the traditional recommendation methods such as *content-based filtering* 
or *collaborative filtering* (I will talk about them at the end of this post) are not very applicable in this case. Instead, we can think of the problem as 
a supervised machine learning task where we have to predict the new products that a user will  buy (the label) **base on** what they already had (the features). There are 
two strategies (in many strategies) to define such a model:
* We can build a binary classifier to predict whether a product would be added by the user in next month, regardless of being newly added or not.
* Or build a multi-class classifier to predict which product will be newly added by the user in next month. If there're many product being newly added, 
a single product is randomly choosen as the target.

To actually compete with other Kagglers in this competition, you should build some seperated models of both types then compute the weighted average
of these models (the weight is tuned and optimized based on a validation set) to get the final prediction. However, for the purpose of this post, I just follow the second strategies since my 
original intent is to understand the problem and do some predictive analysis on it and it's also easier for my laptop to handle the data.
 
At first glance, I want to load the whole training data to my machine to do some exploratory analysis. But since the training data was too big to fit
 into $$8$$GB RAM memory, I have to think of a more memory efficient way to handle them. As every row in training data has a unique month, 
I use a simple bash script (you are encouraged to use use bash script to process data if applicable) to divide the data into several files, each file contains 
only $$1$$ partitioned month data.

```console
foo@bar:~$ tail -n +2 train_ver2.csv > train_no_header.csv      # New training data file with header removed
foo@bar:~$ mkdir train                                          # Make a new folder named train
foo@bar:~$ awk -F\, '{print>"train/"$1}' train_no_header.csv    # Gather rows begining with the same pattern which is the date into a file
```
This results in several files in <code>train</code> folder like: <code>2015-01-28</code> which contains data from Jan 2015 and so on. As mentioned, we will 
build a multi-class classifier to predict newly added products, so, for each month, I construct a new file that contains user id and **new** products 
(products that he/she didn't have this month but next month) with the following code. 

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
            
dates = [(2015, "{:02d}".format(month)) for month in range(1, 12)] + \
        [(2016, "{:02d}".format(month)) for month in range(1, 4)]
for year, month in dates:
    additional_products = get_additional_products_monthly(year, month)
    to_csv(additional_products, '../data/train/additional-' + "-".join([year, month, '28']))
```



