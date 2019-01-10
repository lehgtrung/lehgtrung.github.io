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
such as "credit card", "savings account", etc. We will predict what additional products a customer will get in the 
last month, *2016-06-28*, in addition to what they already have at *2016-05-28*. 
These products are the columns named: *ind_(xyz)_ult1*, which are the columns $$#25 - #48$$ in the training data. 
We will predict what a customer will buy in addition to what they already had at *2016-05-28*. 


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
We are provided with $$1.5$$ years of customer data of Santander Banks from **January 2015** to **May 2015** of nearly $$1$$ million distinct users.
The goal of this competition is to predict top $$7$$ most likely product that *929,615* customers in the test set would purchase in **June 2016**. The test data 
is split randomly to public and private set using a 30-70% random split. The public leaderboard is ranked on the public test set whereas 
the private set is used to calculate the final standings. The scoring was evaluated using *Mean Average Precision* at $$7$$ (MAP@7):

$$\begin{aligned} MAP@7 = \frac{1}{|U|} \sum_{u=1}^{|U|} \frac{1}{min(m,7)} \sum_{k=1}^{min(m,7)} P(k) \end{aligned}$$

where $$|U|$$ is the number of rows (users in two time points), $$P(k)$$ is the precision at cutoff $$k$$, $$n$$ is the number of predicted products, and 
$$m$$ is the number of added products for the given user at that time point. If $$m = 0$$, the precision is defined to be $$0$$.

## My Approach
This is a interesting competition since we have the freedom to how we process the data, extract features and build our model. At first glance, I want to load the whole training data
to my 8GB RAM machine to do some exploratory analysis. But since the training data was too big to fit to machine memory, I have to think of a more memory efficient way to handle the data.
Since every row in training data has a unique month, I use a simple shell script to divide the data into several files, each file contains only $$1$$ partitioned month data.

```console
foo@bar:~$ awk -F\, '{print>$1}' train_ver2.csv
```
This results in several files like: <code>2015-01-28</code> which contains data from Jan 2015 and so on. For each month, I construct a new file that contains user information and **new** product
that they added in the next month.
