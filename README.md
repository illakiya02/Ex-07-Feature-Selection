# Ex-07-Feature-Selection
## AIM
To Perform the various feature selection techniques on a dataset and save the data to a file. 

# Explanation
Feature selection is to find the best set of features that allows one to build useful models.
Selecting the best features helps the model to perform well. 

# ALGORITHM
### STEP 1
Read the given Data
### STEP 2
Clean the Data Set using Data Cleaning Process
### STEP 3
Apply Feature selection techniques to all the features of the data set
### STEP 4
Save the data to the file


# CODE
import pandas as pd import numpy as np import matplotlib.pyplot as plt df=pd.read_csv('/content/titanic_dataset.csv') df.head()

df.isnull().sum()

df.drop('Cabin',axis=1,inplace=True) df.drop('Name',axis=1,inplace=True) df.drop('Ticket',axis=1,inplace=True) df.drop('PassengerId',axis=1,inplace=True) df.drop('Parch',axis=1,inplace=True) df.head()

df['Age']=df['Age'].fillna(df['Age'].median()) df['Embarked']=df['Embarked'].fillna(df['Embarked'].mode()[0]) df.isnull().sum()

plt.title("Dataset with outliers") df.boxplot() plt.show()

cols = ['Age','SibSp','Fare'] Q1 = df[cols].quantile(0.25) Q3 = df[cols].quantile(0.75) IQR = Q3 - Q1 df = df[~((df[cols] < (Q1 - 1.5 * IQR)) |(df[cols] > (Q3 + 1.5 * IQR))).any(axis=1)] plt.title("Dataset after removing outliers") df.boxplot() plt.show()

from sklearn.preprocessing import OrdinalEncoder climate = ['C','S','Q'] en= OrdinalEncoder(categories = [climate]) df['Embarked']=en.fit_transform(df[["Embarked"]]) df.head()

from sklearn.preprocessing import OrdinalEncoder climate = ['male','female'] en= OrdinalEncoder(categories = [climate]) df['Sex']=en.fit_transform(df[["Sex"]]) df.head()

from sklearn.preprocessing import RobustScaler sc=RobustScaler() df=pd.DataFrame(sc.fit_transform(df),columns=['Survived','Pclass','Sex','Age','SibSp','Fare','Embarked']) df.head()

import statsmodels.api as sm import numpy as np import scipy.stats as stats from sklearn.preprocessing import QuantileTransformer qt=QuantileTransformer(output_distribution='normal',n_quantiles=692)

df1=pd.DataFrame() df1["Survived"]=np.sqrt(df["Survived"]) df1["Pclass"],parameters=stats.yeojohnson(df["Pclass"]) df1["Sex"]=np.sqrt(df["Sex"]) df1["Age"]=df["Age"] df1["SibSp"],parameters=stats.yeojohnson(df["SibSp"]) df1["Fare"],parameters=stats.yeojohnson(df["Fare"]) df1["Embarked"]=df["Embarked"] df1.skew()

import matplotlib import seaborn as sns import statsmodels.api as sm %matplotlib inline from sklearn.model_selection import train_test_split from sklearn.linear_model import LinearRegression from sklearn.feature_selection import RFE from sklearn.linear_model import RidgeCV, LassoCV, Ridge, Lasso

X = df1.drop("Survived",1) y = df1["Survived"]

plt.figure(figsize=(12,10)) cor = df1.corr() sns.heatmap(cor, annot=True, cmap=plt.cm.RdPu) plt.show()

cor_target = abs(cor["Survived"]) relevant_features = cor_target[cor_target>0.5] relevant_features

X_1 = sm.add_constant(X) model = sm.OLS(y,X_1).fit() model.pvalues

cols = list(X.columns) pmax = 1 while (len(cols)>0): p= [] X_1 = X[cols] X_1 = sm.add_constant(X_1) model = sm.OLS(y,X_1).fit() p = pd.Series(model.pvalues.values[1:],index = cols)
pmax = max(p) feature_with_p_max = p.idxmax() if(pmax>0.05): cols.remove(feature_with_p_max) else: break selected_features_BE = cols print(selected_features_BE)

model = LinearRegression()

rfe = RFE(model,step= 4)

X_rfe = rfe.fit_transform(X,y)

model.fit(X_rfe,y) print(rfe.support_) print(rfe.ranking_)

nof_list=np.arange(1,6)
high_score=0 nof=0
score_list =[] for n in range(len(nof_list)): X_train, X_test, y_train, y_test = train_test_split(X,y, test_size = 0.3, random_state = 0) model = LinearRegression() rfe = RFE(model,step=nof_list[n]) X_train_rfe = rfe.fit_transform(X_train,y_train) X_test_rfe = rfe.transform(X_test) model.fit(X_train_rfe,y_train) score = model.score(X_test_rfe,y_test) score_list.append(score) if(score>high_score): high_score = score nof = nof_list[n] print("Optimum number of features: %d" %nof) print("Score with %d features: %f" % (nof, high_score))

cols = list(X.columns) model = LinearRegression() rfe = RFE(model, step=2)
X_rfe = rfe.fit_transform(X,y)
model.fit(X_rfe,y)
temp = pd.Series(rfe.support_,index = cols) selected_features_rfe = temp[temp==True].index print(selected_features_rfe)

reg = LassoCV() reg.fit(X, y) print("Best alpha using built-in LassoCV: %f" % reg.alpha_) print("Best score using built-in LassoCV: %f" %reg.score(X,y)) coef = pd.Series(reg.coef_, index = X.columns) print("Lasso picked " + str(sum(coef != 0)) + " variables and eliminated the other " + str(sum(coef == 0)) + " variables") imp_coef = coef.sort_values() import matplotlib matplotlib.rcParams['figure.figsize'] = (8.0, 10.0) imp_coef.plot(kind = "barh") plt.title("Feature importance using Lasso Model") plt.show()

# OUPUT

![image](https://github.com/illakiya02/Ex-07-Feature-Selection/assets/112244898/9f020410-980e-43f0-b08d-9e21e66a986f)

![image](https://github.com/illakiya02/Ex-07-Feature-Selection/assets/112244898/073529f4-51cf-4fde-bf88-da7f475e474a)

![image](https://github.com/illakiya02/Ex-07-Feature-Selection/assets/112244898/835183e4-e2d1-44cb-8c80-c72b0909b633)

![image](https://github.com/illakiya02/Ex-07-Feature-Selection/assets/112244898/1e799e57-ec77-4d08-a42e-c48c3f7179a4)

![image](https://github.com/illakiya02/Ex-07-Feature-Selection/assets/112244898/62473c9c-23c1-46ff-b555-6164a4c7f3a0)

![image](https://github.com/illakiya02/Ex-07-Feature-Selection/assets/112244898/c96f3880-1142-4853-b69e-c364656885b7)

![image](https://github.com/illakiya02/Ex-07-Feature-Selection/assets/112244898/5f3dbd29-5b32-4fe6-b85f-1f59d82a40cf)

![image](https://github.com/illakiya02/Ex-07-Feature-Selection/assets/112244898/af740e10-2ce6-4242-ba04-11cdb52c6412)

![image](https://github.com/illakiya02/Ex-07-Feature-Selection/assets/112244898/ec9df63f-5b9d-4055-a153-3ef01e055ad2)

![image](https://github.com/illakiya02/Ex-07-Feature-Selection/assets/112244898/694d1459-0e5a-4ec3-b644-af13311f5ca5)

![image](https://github.com/illakiya02/Ex-07-Feature-Selection/assets/112244898/11808dea-8a78-4e47-8b08-e5624e428a67)

![image](https://github.com/illakiya02/Ex-07-Feature-Selection/assets/112244898/bf8c4549-c903-492c-b3cc-4b31dec0677a)

![image](https://github.com/illakiya02/Ex-07-Feature-Selection/assets/112244898/8b40b1a9-9b91-425d-ac5f-ac9bcc74cdc2)

![image](https://github.com/illakiya02/Ex-07-Feature-Selection/assets/112244898/d5dddb46-c50b-49a8-985c-0aa67dfc8d7f)

![image](https://github.com/illakiya02/Ex-07-Feature-Selection/assets/112244898/58cea638-31c3-4133-9a64-d22e5d01f169)

![image](https://github.com/illakiya02/Ex-07-Feature-Selection/assets/112244898/16874908-127c-47d3-aa39-dec84a565eae)

![image](https://github.com/illakiya02/Ex-07-Feature-Selection/assets/112244898/b88af731-bdbd-4692-b613-c9aa55229b70)


