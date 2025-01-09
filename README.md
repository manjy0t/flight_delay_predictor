# Flight Delay Predictor for Hartsfield–Jackson Atlanta International Airport

We developed a serverless machine learning pipeline for predicting flight delays at **Hartsfield-Jackson Atlanta International Airport (ATL)**. The predictions are based on weather conditions and historical flight delay data. 

### In this repository, you will find:
- **Flight Info** and **Meteorological Data** for the year 2024, including detailed weather and flight data for ATL.
- **Graphical User Interface** hosted on HuggingFace 🤗 using **Gradio**, where you can interact with the data and predictions.

---

## Architecture

The architecture of the machine learning pipeline follows a **serverless** structure, enabling scalability and adaptability. Each pipeline runs as a scheduled task in a serverless environment, ensuring low maintenance and flexibility. The data flows through multiple stages, from collection to model training, and finally to prediction.

---

## Pipelines Description

### **Historical Data Collection and Preparation (Historical Feature Pipeline)**
This pipeline gathers and processes historical data from multiple sources to form the foundation of the machine learning model. The sources of the data include:
- **Meteorological Data**: Collected from Openmeteo.
- **Flight Information**: Collected from the **Bureau of Transportation Statistics (BTS)**.

The processed datasets are merged and saved into a **Feature Group** on **Hopsworks**.

### **Model Training**
After preprocessing, the model is trained using **XGBoost**, a Gradient Boosting framework. We attempted to fine-tune the model using historical flight data from **Zylalabs API**, but this resulted in worse performance than before. The final model is saved in **Hopsworks Model Registry**.

### **Inference Pipeline**
Once the model is trained, the results are saved and made available via a **Gradio user interface** on **HuggingFace**.

---

## Results

### **Graphical User Interface**
To make the results accessible and interactive, a **Gradio interface** is deployed on **HuggingFace 🤗**. This interface allows you to:
- Select a specific flight by answering relevant questions.
- Receive predictions on whether the flight will be delayed or not.

---

## Software Used
- **Google Colab**: Running environment for development
- **HuggingFace**: GUI hosting platform
- **Gradio**: GUI framework
- **Hopsworks**: MLOps platform for feature store and model registry
- **Bureau of Transportation Statistics (BTS)**: Historical flight information API
- **Openmeteo**: Historical weather data API
