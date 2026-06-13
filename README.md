# Ames Housing Price Prediction
This project follows the CRISP-DM methodology.


## 1. Bussiness Understanding
The objective of this project is to predict house sale prices using property and neighborhood characteristics from the Ames Housing Dataset. The results of this project can be valuable for home buyers, sellers, and real estate professionals to determine the key factors affecting housing prices and make the best decision. In addition, the findings may help real estate developers and renovation companies by providing insights into different influential factors and helping them to make more informed investment decisions.   


## 2. Data Understanding
The Ames Housing Dataset was compiled by Dean De Cock for educational purposes in data science. It contains information on residential properties sold in Ames, lowa, USA, between 2006 and 2010. The dataset includes 79 variables describing various aspects of residential properties along with an Id column and the target variable SalePrice, resulting in a total of 81 columns. The training set consists of 1460 observations, while the test set contains 1459 observations. 
The features cover a wide range of property characteristics such as building age, living area, basement and garage information, quality ratings, and neighborhood attributes. Variables are a mix of numerical and categorical features, although some numerical variables represent discrete categories. 
Exploratory Data Analysis(EDA) was performed using descriptive statistics, scatter plots, correlation analysis and heatmaps which revealed that variables such as 'OverallQual','GrLiveArea','TotalBsmtSF','1stFlrSF','GarageArea',and 'YearBuilt' have strong relationships with SalePrice. The analysis also depicted potential multicollinearity among some predictors such as 'TotalBsmtSf' and '1stFlrSf', as well as 'GarageYearBlt' and 'YearBuit'. this issue was addressed using regularization techniques(Ridge and Lasso) in linear models, while tree-based models were not affected by multicollinearity.
Although the dataset is rich and well_structured, additional external factors such as access to public trasportation, proximity to schools, and nearby commercial facilities could further improve prediction perform. 


## 3. Data preparation
### Handling missing values: 
There are two types of Missing Values in the dataset. In some columns, NaN does not represent a lost or unrecorded measurement; Instead, it indicates the absence of that particular feature. For example, a high percentage of NaN values in columns such as 'PoolQC','Fence', and 'MiscFeature' indicates that those houses do not have these features.
Missing values in several Features related to Basement, Garage, FirePlace, and Alley have the same meaning. In such cases, missing values in categorical features were replaced with "None", while missing values in numerical features were replaced with "0".
However, NaN values in some columns such as 'MSZoning' and 'LotFrontage' represent genuinely unrecorded or missing information. For these features imputation was performed based on domain Knowledge, the number of missing values, the underlying pattern of missingness like the type of randomness. For example, Missing values in 'LotFrontage'  were imputed using the median LotFrontage within each neighborhood group, obtained through a group-by operation. This approach was chosen because 'LotFrontage'(the linear feet of street connected to property)  is strongly related to the characteristics of each neighborhood. 'MSZoning' is the same as 'LotFrontage' in NaNs imputation.  
Other features with a small number of missing values were also imputed using appropriate statistics such as the mode or median, depending on the nature of the variable.
### log transformation of SalePrice: 
The distribution of the target variable (SalePrice) was examined using both a histogram and a Q-Q plot.
The analysis showed that SalePrice was positively skewed and deviated from a normal distribution. Since many regression models perform better when the target variable is closer to normality, a logarithmic transformation was applied. This transformation made it significantly closer to normal.
### Encoding Categorical Variables:
After handling missing values, categorical variables were transformed into numerical representations using one-hot encoding(get_dummies)
### Feature Scaling:
Finally, feature scaling was performed using StandardScaler to standardize the numerical inputs before model training. It improves the performance of scale-sensitive models such as Linear Regression, Ridge, and Lasso. 


## 4. Modeling
Several machine learning models were evaluated to predict house sale prices. A baseline Linear Regression model was first fitted. Diagnostic analysis indicated violations of some classical linear regression assumptions and the presence of multicollinearity among predictors. In some cases, VIF is infinite, which shows complete multicollinearity. To address these issues, regularized linear models including Ridge and Lasso Regression were performed. The optimal regularization parameters were selected using k-fold-cross-validation. 
In addition, several tree_based methods were examined. First, a single Decision Tree model was tuned over different values of maximum depth, minimum samples per leaf, and cost-complexity pruning(ccp-alpha). Bagging was then applied to reduce variance and improve predictive performance.
More advanced ensemble methods, including Random Forest, Gradient Boosting, Tuned Gradient Boosting, and Extreme Gradient Boosting(XGB) were subsequently trained and compared. For each model, hyperParameters were selected based on cross-validation performance. 


## 5. Evaluation
The performance of all models was evaluated using R^2, RMSE, MAPE, and several absolute-error statistics. 
The results of R^2, RMSE and MAPE is summarized in the following table, and detailed results are given in the following image.

Note that RMSE,R^2, and absolute-error statistics were computed on the log-transformed target variable, whereas MAPE was calculated on the original sale prices.

|Model | R^2 | RMSE | MAPE(%) |
|------|------|------|------|
|Linear Regression     | 0.818  | 0.185 | 9.18 |
|Ridge Regression      | 0.875  | 0.153 | 10.39|
|Lasso Regression      | 0.866  | 0.158 | 10.58|
|Decision Tree         | 0.789  | 0.198 | 14.81|
|Bagging               | 0.865  | 0.159 | 10.76|
|Random Forest         | 0.857  | 0.164 | 10.99|
|Gradient Boosting     |0.884   | 0.147 | 10.40|     
|Tuned GradientBoosting| 0.892  | 0.142 | 10.20|
|XGBoost               | 0.894  | 0.141 | 9.61 |  

![Model Comparison](images/model_comparison.PNG)

The results indicate that XGBoost achieved the best overall performance, obtaining the highest R^2 (0.894) and the lowest RMSE(0.141). The average percentage error on the original sale prices(MAPE) is 9.61%, which is among the lowest values after Linear Regression, indicating that this model has high predictive accuracy.   
Tuned Gradient Boosting also performed competitively, suggesting that boosting_based ensemble methods are particularly effective for this dataset. 
As expected, a single Decision Tree produced the weakest performance, with the lowest R^2(0.789) and the highest RMSE(0.198).Moreover this method shows the largest average percantage error on the original sale prices(14.8%), likely because individual trees are prone to overfitting and can have high variance, which limits their ability to generalize well to unseen data.  
Ensemble methods such as bagging,Random Forest, Gradient Boosting, and XGBoost substantially improved predictive accuracy by reducing variance and capturing more complex relationships within the data. 

## 6. Deployment
This project is implemented in a notebook environment and has no deployed application. However, in a real-world scenario the trained model can be deployed using lightweight frameworks such as Flask or Streamlit to provide a simple web interface for users to input data and receive predictions.