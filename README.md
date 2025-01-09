# Flight delay predictor for Hartsfield–Jackson Atlanta International Airport

Flight Delay Predictor
We developed a serverless machine learning pipeline for our final project, it is capable of predicting the flight delays of flights departing daily from Hartsfield-Jackson Atlanta International Airport (ATL). The predictions are based on weather conditions and historical flight delay information. In this repository, you will find:
•	Flight Info and Meteorological Data for the year 2024, including detailed weather and flight data for ATL. 
•	Graphical User Interface hosted on HuggingFace 🤗 using Gradio, where you can interact with the data and predictions.
Architecture
The architecture of the machine learning pipeline follows a serverless structure, enabling scalability and adaptability. Each pipeline runs as a scheduled task in a serverless environment, ensuring low maintenance and flexibility. The data flows through multiple stages, from collection to model training and finally to prediction and visualization.
Pipelines Description
This pipeline gathers and processes historical data from multiple sources to form the foundation of the machine learning model. The sources of the data include:
•	Meteorological Data: Collected from Openmeteo.
•	Flight Information: Collected from Bureau of Transportation Statistics (BTS).
The processed datasets are merged and saved into a Feature Group on Hopsworks.
After preprocessing, the model is trained using XGBoost, a Gradient Boosting framework. Furthermore, we tries to fine-tune the model with zylalabs api historical flight data but it made the model performance worse than before. The final model is saved in Hopsworks Model Registry.
The results are saved and made available via a Gradio user interface on HuggingFace.
Results
Graphical User Interface
To make the results accessible and interactive, a Gradio interface is deployed on HuggingFace 🤗. This interface allows you to:
•	Select a specific flight by answering relevant questions.
•	Then, you will get the prediction whether the flight will delay or not..
Software Used
•	Google Colab: Running environment for development
•	HuggingFace: GUI
•	Gradio: GUI framework
•	Hopsworks: MLOps platform for feature store and model registry
•	Bureau of Transportation Statistics (BTS): Historical flight information API
•	Openmeteo: Historical weather data API







