# Flight Delay Predictor for Hartsfield–Jackson Atlanta International Airport

We developed a serverless machine learning pipeline for predicting flight delays at **Hartsfield-Jackson Atlanta International Airport (ATL)**. The predictions are based on weather conditions and historical flight delay data. 

### In this repository, you will find:
- **Flight Info** and **Meteorological Data** for the year 2024, including detailed weather and flight data for ATL.
- **Graphical User Interface** using **Gradio**, where you can interact with the data and predictions.

---

## Architecture

The architecture of the machine learning pipeline follows a **serverless** structure, enabling scalability and adaptability. Each pipeline runs as a scheduled task in a serverless environment, ensuring low maintenance and flexibility. The data flows through multiple stages, from collection to model training, and finally to prediction. Three different regression models are implemented in the pipeline, with the purpose of performance comparison and model selection. These models are: XGBoost, MLP and a Ridge Regressor. For fine-tuning, the Optuna library is used.

---

## Pipelines Description

### **Historical Data Collection and Preparation (Historical Feature Pipeline)**
This pipeline gathers and processes historical data from multiple sources to form the foundation of the machine learning model. The sources of the data include:
- **Meteorological Data**: Collected from Openmeteo.
- **Flight Information**: Collected from the **Bureau of Transportation Statistics (BTS)**.

The processed datasets are merged and saved into a **Feature Group** on **Hopsworks**.

### **Feature Engineering & Model Training (Feature & Training Pipeline)**
After preprocessing, further feature engineering is performed to the merged dataset. This involves dropping features carrying redundant information, encoding the categorical feature "carrier" (both one-hot encoding and label encoding are used to optimize for use with different machine learning models) and rescaling numerical features with a StandardScaler. We also lagged various delay data by one month to decrease correlation with the target variable (total monthly delay) and to add information about monthly patterns. With a similar objective, the integer feature "month" is transformed to sine and cosine values of itself. Additionally, various tests are performed to determine whether more features can be dropped from the dataset. These tests include: A computation of the coefficient of variation for each feature, a correlation matrix and a feature importance test. Features with too little variation, too high multicollinearity or too low feature importance are dropped from the training data. The updated datasets are saved to new feature groups and corresponding feature views in Hopsworks. 

In the same Jupyter notebook, three regression models are trained to predict the monthly arrival delay for a given carrier, based on flight and weather data. The chosen regression models are: **XGBoost**, **Multi-Layer Perceptron*** and a **Ridge Regressor**. Next, hyperparameter tuning is performed using Optuna's **Tree-Structured Parzen Estimator (TPE)** for fast and precise tuning. The performance for all three models is tested before and after hyperparameter tuning. As can be seen in the notebook, hyperparameter tuning with Optuna's TPE decreased MSE and increased R2 for XGBoost and the Ridge Regressor. The final models are saved in the Hopsworks model registry. 

**Fine-Tuning**

A form of fine-tuning was performed in the previous notebook, using Optuna. However, we wanted to explore how one of our models would behave when trained on and optimized for different datasets. This notebook uses the MLP model and once again optimizes hyperparameters using Optuna's TPE, after being trained on two new datasets. Even though the Ridge Regressor had performed slightly better in terms of MSE and R2 in previous tests, we ended up using the MLP model instead. The justification for this is that the MLP model has more room for optimization, as it has more hyperparameters, compared to the Ridge Regressor, where the only hyperparameter we could tune is alpha (the regularization parameter that controls the strength of the penalty applied to the regression coefficients). 

The datasets we used were:
* A modified version of the dataset used in the training pipeline, but with further time-series feature engineering applied. This time, we lagged parameters from the flight dataset by both two and three months in new respective features.
* Historical flight data from the **Zylalabs API**. This dataset underwent similar feature engineering to the dataset used in training, and was also merged with OpenMeteo weather data.

Both datasets were stored in feature groups on Hopsworks. The modified version of the training dataset ended up achieving the best scores for MSE and R2 after hyperparameter re-tuning with Optuna. That model is uploaded to the Hopsworks model registry as well, and later used in the implementation of our User Interface. 

### **User Interface**
Using **Gradio** as our primary framework for hosting the UI, we also make use of our best-performing model and the Plotly library for visual elements. 

---

## Results

**Training, Testing & Fine-Tuning**
As previously mentioned, we used MSE (Mean Squared Error) and R2 score to measure model performance. Here is a summary of performance metrics for different models.

Before hyperparameter tuning:
* XGBoost Regressor - MSE: 0.49, R²: 0.70
* MLP - MSE: 0.19, R²: 0.73
* Ridge Regressor - MSE: 0.16, R²: 0.76

After hyperparameter tuning:
* XGBoost Regressor - MSE: 0.46, R²: 0.72
* MLP - MSE: 0.20, R²: 0.71
* Ridge Regressor - MSE: 0.15, R²: 0.79

New datasets after hyperparameter tuning:
* MLP with BTS + Openmeteo data - MSE: 0.07, R²: 0.90
* MLP with Zylalabs + Openmeteo data - MSE: 0.90, R²: 0.08
(These results are displayed in different notebooks as we decided to execute them at the same time but in different notebooks. You can find the MLP with BTS + Openmeteo data in the notebook **fine_tuning_pipeline_USE_THIS_ONE.ipynb** and the MLP with Zylalabs + Openmeteo data in the notebook **fine_tuning_pipeline (2).ipynb**. Before deploying Optuna, the notebooks are identical. The latter model was not uploaded to Hopsworks.)

**Graphical User Interface**
To make the results accessible and interactive, a **Gradio interface** is implemented. This interface allows you to:
* Adjust selected inputs using sliders.
* Choose a carrier from a dropdown list.
* Visualize the predicted delay with a color-changing sphere accompanied by a score. A blue sphere and a score close to -1 corresponds to low delay estimates, whereas a red sphere and a score close to 5 corresponds to high delay estimates.
* Rotate the sphere and the score label in a three-dimensional display space. 

---

## Software Used
- **Google Colab**: Running environment for development
- **HuggingFace**: GUI hosting platform
- **Gradio**: GUI framework
- **Hopsworks**: MLOps platform for feature store and model registry
- **Bureau of Transportation Statistics (BTS)**: Historical flight information API
- **Openmeteo**: Historical weather data API
- **Pandas**: Library for storing and manipulating data
- **Optuna**: Library used for hyperparameter tuning
- **Plotly**: Visual library used in the GUI

## How to run the code
The running the code requires you to have an account with Hopsworks and access to the Historical Flights API available with Zylalabs (https://zylalabs.com/api-marketplace/travel/Historical+Flights+API/2583/#info). NOTE: The Historical Flights API requires you to either subscribe for access or start a 7-day free trial with Zyla (as a free trial user you can make 500 API calls, which is more than enough to run this code). You should save your API key to both of these services in separate text files. Name them **'hopsworks-api-key.txt'** and **'zyla_key.txt'**, respectively.

The notebooks are written in Google Colab, and that is the environment we recommend you to run these notebooks in as well, after downloading them here from GitHub.

The order in which to run the notebooks is as follows:
1. **historical_backfill_data (2).ipynb**
2. **feature_and_training_pipeline.ipynb**
3. **fine_tuning_pipeline_USE_THIS_ONE (1).ipynb**
4. **ui.ipynb**

Any other notebooks in this repository are unfinished complements to the ones listed above. 
