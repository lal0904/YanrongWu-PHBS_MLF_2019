# A Recession Indicator Generated by Interest Rates

## Group Members

| Name         | StudentID  | GithubID                                          |
| ------------ | ---------- | ------------------------------------------------- |
| Yanrong Wu   | 1801212952 | [YanrongWu](https://github.com/YanrongWu)         |
| Anlei Liu    | 1801212887 | [lal0904](https://github.com/lal0904)             |
| Yiming Zhang | 1801212981 | [zhang1ming137](https://github.com/zhang1ming137) |
| Tianqi Gao   | 1901212578 | [preciousky](https://github.com/preciousky)       |

## 1. Introduction

Economic recession is one of the most important economic phenomenon in macroeconomics. Predicting economic decline has always been a tricky but attractive topic. Due to the gaming of the market and the excessively complex elements included in the market, predicting the time point of economic decline has almost become an unsolvable problem in history. 

Referring to the professor's suggestion, our group decided to launch the project to practice the programming methods learned in the class to preliminarily explore the effect on financial recession, which is generated from the relationship between short-term market interest rates and long-term market interest rates on financial weakness. This project attempts to construct multiple indicator variables based on the interest rates of different financial products in different periods, and seeks the contribution of these indicator variables by machine learning method, moreover, for the chance to get more details of the theoretical explanation of economic recession.

## 2. Project Goal
When the gap between the long-term bonds and short-term bonds shrinks, it always indicates that the market anticipates the interest rates will fall in future as a hedge to upcoming downturn. In some extreme circumstances, this indicator can even alert the recession of the stock market. Our goal is to capture this relationship and make the model useful for recession prediction.  

## 3. Data Acquisition and Description
### 3.1 Raw Data
1. We download the YTM (yield to maturity) of the US treasuries with various duration range from 1 month to 30 years,from [fred.stlouisfed.org](fred.stlouisfed.org). The data is arranged by daily data and the time span from 1962 to the present. You can see in the [Yield_newVersion.csv](https://github.com/YanrongWu/YanrongWu-PHBS_MLF_2019/blob/master/Final%20Group%20Project/data/Yield_newVersion.csv)

2. Considering that only use the data of the YTM of treasuries maybe not sufficient，we collect more data contains the volatility of the stock market and the changes of the monthly economic indicators such as the rate of unemployment and core CPI from [Wind](https://www.wind.com.cn/). The raw data is stored in:

[DJ_weekly.csv](https://github.com/YanrongWu/YanrongWu-PHBS_MLF_2019/blob/master/Final%20Group%20Project/data/DJ_weekly.csv) for weekly price and volatility data of DJIA (Dow Jones Industrial Average) index.

[Economic_data.csv](https://github.com/YanrongWu/YanrongWu-PHBS_MLF_2019/blob/master/Final%20Group%20Project/data/Economic_data.csv) for monthly CPI and unemployment rate.

### 3.2 Feature Engineering
**The table below illustrates the summary of the potential features we get**

![Features](https://github.com/YanrongWu/YanrongWu-PHBS_MLF_2019/blob/master/Final%20Group%20Project/Figures/data.png)

•	***DGS1:*** The YTM of the one-year US treasury 

•	***DGS3:*** The YTM of the 3-year US treasury 

•	***DGS5:*** The YTM of the 5-year US treasury 

•	***Weekly return:*** DJIA (Dow Jones Industrial Average) weekly return at the date

•	***Return of past month:*** DJIA accumulated monthly return of the last month

•	***Return of past half year:*** DJIA accumulated return of the last half year

•	***Return of past year:*** DJIA accumulated return of the last year

•	***Volatility:*** Volatility of DJIA this week

•	***Average weekly Volatility of past month:*** the average of weekly Volatility of DJIA last month

•	***Average weekly Volatility of past quarter:*** the average of weekly Volatility of DJIA last quarter

***Tips:***

***Consering the YTM is daily data but the date in the [DJ_weekly.csv](https://github.com/YanrongWu/YanrongWu-PHBS_MLF_2019/blob/master/Final%20Group%20Project/data/DJ_weekly.csv) and 
[Economic_data.csv](https://github.com/YanrongWu/YanrongWu-PHBS_MLF_2019/blob/master/Final%20Group%20Project/data/Economic_data.csv) are arranged in weekly or monthly data. As a result, for everyday, we chose to trace back to the first accessable date to fill in the blanks in the features.***

### 3.3 Classifications of Recession

According to [NBER's Business Cycle Dating Committee](http://www.nber.org/cycles/recessions.html), important recessions of US is illustrated in the table below:

![US_Business_Cycle](https://github.com/YanrongWu/YanrongWu-PHBS_MLF_2019/blob/master/Final%20Group%20Project/Figures/US_Business_Cycle.png)

According to the table, we can define the period between the peak and through of an cycle as recession interval. For example, the ***December 1969*** is a peak of an cycle and ***February 1970*** is the through of the same cycle. As a result, we can define the ***December 1969 to February 1970*** as an recession.

Our process model of the fact is class all the date between an recession as 1 and 0 for others, which means that all ***y*** during the interval like ***December 1969 to February 1970*** is 1 and the others is 0

### 3.4 Generate the Whole Dataset Divided into True or False

The dataset is illustrated below:
![divided_data](https://github.com/YanrongWu/YanrongWu-PHBS_MLF_2019/blob/master/Final%20Group%20Project/Figures/divided_data.png)

***Tips:*** 
1) ***10M1*** means the differnce of YTM of 10-year US treasury and 1-year US treasury. ***10M3***, ***10M5***, ***5M3***, ***5M1*** have the same meaning.

2) Considerng that our target is to predict the likelyhood of the recession accur. It's meaningless to use the synchronized features and classification. ***The calssification result in the table below in fact is delayed for six months, which means that the y of 2000/1/1 is the division of 2000/7/1 which is six month later.***

### 3.5 Data Visualization
In our following process, we will use data from 1962.  
![](https://github.com/YanrongWu/YanrongWu-PHBS_MLF_2019/blob/master/Final%20Group%20Project/Figures/1960s%20onwards%20yield.png)  
![](https://github.com/YanrongWu/YanrongWu-PHBS_MLF_2019/blob/master/Final%20Group%20Project/Figures/1960s%20onwards%20recession.png)   


## 4. Model-Simple Version Only Fcous on Interest Spread

At the first stage of our model, we want to just use the features of interest spread to predict the recession. We take the ***10M1*** and ***10M3*** as the features and the result of classification as the ***y***. ***We totally have about 170000 daily data***

We tried three models(LR,SVM and Tree), by dividing the dataset into training and validation set, we can get the accuracy for both of them. At the same time, we also use confusion matrix to assess model performance. By changing the numbers of data in the training set, we can find the best division of the dataset for each model.

#### Traditional Model：LR

![learning curve figure lr](https://github.com/YanrongWu/YanrongWu-PHBS_MLF_2019/blob/master/Final%20Group%20Project/Figures/LR.png)

For LR model, we can see the validation accuracy is stable as around ***86%*** as the changing of the scale of traning set, which means that if we wants to improve the performance further, maybe more features is necessary.

#### Traditional Model：SVC

![learning curve figure svc](https://github.com/YanrongWu/YanrongWu-PHBS_MLF_2019/blob/master/Final%20Group%20Project/Figures/SVM.png)

For SVC model, we can see the validation accuracy is indeed improving as we distribute more data for traning ***(propotion from 0.1 to 0.3)***, but stable after 0.3. The optimal accuracy is around ***83%*** which is worse than LR.

#### Traditional Model：Desicion Tree

![learning curve figure tree](https://github.com/YanrongWu/YanrongWu-PHBS_MLF_2019/blob/master/Final%20Group%20Project/Figures/Tree.png)  


For Desicion Tree model, we can see the validation accuracy is improving as the proportion of traning set ***changing from 0.1 to 0.2)***, but stable after 0.2. The optimal accuracy is around ***86%*** which is similar to LR.

## 5. Current Conlusion  

Comparing the three traditional model, we can see that ***by changing the scale of training set, we can only get an optimal accuracy about 86%***, We think this result is not very satisfying. However, continue to exploit the data of interest spread is not effctive any more and we think more features is needed. At the same time, we think we can also do more upgrade on the designing of the model and feature so we wants to develop an new model in the following part.

## 6. Model-Upgrade Version to Cover the features of Stock Market and Short-term Economic data (Still Working Now)

 
**From professor‘s suggestions, further we can define a score (continuous value) for recession rather than just use binary value. For example, score can be +5, -4, -3, +3, etc and score < 0  can be recession. To realize it, we have the ideas below:**

*1) As we have get the data of the performance of the stock market and real economy, we think we can build a score based on the features because the real economy can be seen as the result of the recession, it's always a little bit  behind the actual economy. At the same time, the stock market can be seen as an anticipation of the future which means it will goes down and recover earlier than the real economy. We think by combination of the two catergrioes togather, we can build an reasonable score for defining the satge of the economy.*

*2) When we reasonably defined the algorithm of score computing, we are going to use the methods we have implemented in the simple version model. After that, we will compare the performance of the two model. In our expectation, our new model can significantly improve the accuracy on prediciting.


## 7. Our Future Improvement

1. Try all possible "long term yield" and "short term yield" combinations to find the best prediction one.  

2. Our project will improve further, continuing in progress...  


## 8. Reference  
[1] fred.stlouisfed.org:  https://fred.stlouisfed.org/  
[2] U.S. DEPARTMENT OF THE TREASURY: https://www.treasury.gov/resource-center/data-chart-center/interest-rates/Pages/TextView.aspx?data=yieldAll  
[3] The National Bureau of Economic Research: https://www.nber.org/cycles.html    
[4] St Louis Federal Research Economic Database: https://fred.stlouisfed.org/series/JHDUSRGDPBR  
[5] Wind   
[6] Ang, A., Piazzesi, M. and Wei, M. (2003). What Does the Yield Curve Tell Us about GDP Growth?. SSRN Electronic Journal.    

## 9. Appendix 
### Data...  
The following figures show the different yield curve after 1990.  
![Interest rate](https://github.com/YanrongWu/YanrongWu-PHBS_MLF_2019/blob/master/Final%20Group%20Project/Figures/Interest%20rate.png)  
![Different yield](https://github.com/YanrongWu/YanrongWu-PHBS_MLF_2019/blob/master/Final%20Group%20Project/Figures/different%20yield.png)  

A technical recession is defined as two consecutive quarters of negative growth(GDP), as such we can obtain the dates that fit this description from the St Louis Federal Research Economic Database. This time series takes binary form, with 1 denoting a recession period and 0 other wise.

![recession](https://github.com/YanrongWu/YanrongWu-PHBS_MLF_2019/blob/master/Final%20Group%20Project/Figures/recession.png)  

The following figures are interest spread with recessions highlighted.  
![10-1/-3](https://github.com/YanrongWu/YanrongWu-PHBS_MLF_2019/blob/master/Final%20Group%20Project/Figures/10%20year%20yield%20minus.png)  
![10-5](https://github.com/YanrongWu/YanrongWu-PHBS_MLF_2019/blob/master/Final%20Group%20Project/Figures/10%20year%20yield%20minus%205.png)  
![5-](https://github.com/YanrongWu/YanrongWu-PHBS_MLF_2019/blob/master/Final%20Group%20Project/Figures/5%20year%20yield%20minus.png)  

##### ***Part 1: Some Difference***s

- *Moody's Seasoned Aaa Corporate Bond **Minus** Federal Funds Rate* (**AAAFFM**)
- *Moody's Seasoned Baa Corporate Bond **Minus** Federal Funds Rate* (**BAAFFM**)
- *3-Month Commercial Paper **Minus** Federal Funds Rate* (**CPFF**)
- *3-Month Treasury Constant Maturity **Minus** Federal Funds Rate* (**T3MFF**)
- *6-Month Treasury Constant Maturity **Minus** Federal Funds Rate* (**T6MFF**)
- *1-Year Treasury Constant Maturity **Minus** Federal Funds Rate* (**T1YFF**)
- *5-Year Treasury Constant Maturity **Minus** Federal Funds Rate* (**T5YFF**)
- *10-Year Treasury Constant Maturity **Minus** Federal Funds Rate* (**T10YFF**)
- *10-Year Treasury Constant Maturity **Minus** 2-Year Treasury Constant Maturity* (**T10Y2Y**)
- *10-Year Treasury Constant Maturity **Minus** 3-Month Treasury Constant Maturity* (**T10Y3M**)
- *3-Month Treasury Bill **Minus** Federal Funds Rate* (**TB3SMFFM**)
- *6-Month Treasury Bill **Minus** Federal Funds Rate* (**TB6SMFFM**)

##### ***Part 2: Treasury Bond and Treasury Bill***

- *1-Month Treasury Constant Maturity Rate* **(DGS1MO)**
- *3-Month Treasury Constant Maturity Rate* **(DGS3MO)**
- *6-Month Treasury Constant Maturity Rate* **(DGS6MO)**
- *1-Year Treasury Constant Maturity Rate* **(DGS1)**
- *2-Year Treasury Constant Maturity Rate* **(DGS2)**
- *3-Year Treasury Constant Maturity Rate* **(DGS3)**
- *5-Year Treasury Constant Maturity Rate* **(DGS5)**
- *7-Year Treasury Constant Maturity Rate* **(DGS7)**
- *10-Year Treasury Constant Maturity Rate* **(DGS10)**
- *20-Year Treasury Constant Maturity Rate* **(DGS20)**
- *30-Year Treasury Constant Maturity Rate* **(DGS30)**
- *4-Week Treasury Bill: Secondary Market Rate* **(DTB4WK)**
- *3-Month Treasury Bill: Secondary Market Rate* **(DTB3)**
- *6-Month Treasury Bill: Secondary Market Rate* **(DTB6)**
- *1-Year Treasury Bill: Secondary Market Rate* **(DTB1YR)**

##### ***Part 3: Commercial Paper, Company Bond and Mortgage*** 

- *1-Month AA Financial Commercial Paper Rate* **(DCPF1M)**
- *3-Month AA Financial Commercial Paper Rate* **(DCPF3M)**
- *3-Month AA Nonfinancial Commercial Paper Rate* **(DCPN3M)**
- *10-Year High Quality Market (HQM) Corporate Bond Par Yield* **(HQMCB10YRP)**
- *1-Year High Quality Market (HQM) Corporate Bond Spot Rate* **(HQMCB1YR)**
- *5-Year High Quality Market (HQM) Corporate Bond Spot Rate* **(HQMCB5YR)**
- *10-Year High Quality Market (HQM) Corporate Bond Spot Rate* **(HQMCB10YR)**
- *15-Year High Quality Market (HQM) Corporate Bond Spot Rate* **(HQMCB15YR)**
- *20-Year High Quality Market (HQM) Corporate Bond Spot Rate* **(HQMCB20YR)**
- *30-Year High Quality Market (HQM) Corporate Bond Spot Rate* **(HQMCB30YR)**
- *90-Day AA Financial Commercial Paper Interest Rate* **(RIFSPPFAAD90NB)**
- *90-Day AA Nonfinancial Commercial Paper Interest Rate* **(RIFSPPNAAD90NB)**
- *Moody's Seasoned Aaa Corporate Bond Yield Relative to Yield on 10-Year Treasury Constant Maturity* (**AAA10Y**)
- *Moody's Seasoned Baa Corporate Bond Yield Relative to Yield on 10-Year Treasury Constant Maturity* (**BAA10Y**)
- *15-Year Fixed Rate Mortgage Average in the United States* **(MORTGAGE15US)**
- *30-Year Fixed Rate Mortgage Average in the United States* **(MORTGAGE30US)**

##### Part 4: London Interbank Offered Rate on U.S. Dollar

- *1-Week London Interbank Offered Rate (LIBOR), based on U.S. Dollar* **(USD1WKD156N)**
- *1-Month London Interbank Offered Rate (LIBOR), based on U.S. Dollar* **(USD1MTD156N)**
- *2-Month London Interbank Offered Rate (LIBOR), based on U.S. Dollar* **(USD2MTD156N)**
- *3-Month London Interbank Offered Rate (LIBOR), based on U.S. Dollar* **(USD3MTD156N)**
- *6-Month London Interbank Offered Rate (LIBOR), based on U.S. Dollar* **(USD6MTD156N)**
- *12-Month London Interbank Offered Rate (LIBOR), based on U.S. Dollar* **(USD12MD156N)**
