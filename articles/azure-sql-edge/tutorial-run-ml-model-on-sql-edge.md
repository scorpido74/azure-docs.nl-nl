---
title: ML-model op Azure SQL Edge implementeren met behulp van ONNX
description: In deel drie van deze driedelige Azure SQL Edge-zelfstudie over het voorspellen van verontreinigingen in ijzererts voert u de ONNX-machine learning-modellen uit op SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: f38a973611cb1ab18eead4ec51e6be91ada2cc40
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85318638"
---
# <a name="deploy-ml-model-on-azure-sql-edge-using-onnx"></a>ML-model op Azure SQL Edge implementeren met behulp van ONNX 

In deel drie van deze driedelige zelfstudie over het voorspellen van onzuiverheden in ijzererts in Azure SQL Edge gaat u het volgende doen:

1. Azure Data Studio gebruiken om verbinding te maken met SQL Database in de Azure SQL Edge-instance.
2. Verontreinigingen in ijzererts voorspellen met ONNX in Azure SQL Edge.

## <a name="connect-to-the-sql-database-in-the-azure-sql-edge-instance"></a>Verbinding maken met de SQL Database in de Azure SQl Edge-instance

1. Open Azure Data Studio.

2. Start op het tabblad **Welkom** een nieuwe verbinding met de volgende gegevens:

   |_Veld_|_Waarde_|
   |-------|-------|
   |Verbindingstype| Microsoft SQL Server|
   |server|Openbaar IP-adres dat wordt vermeld in de VM die is gemaakt voor deze demo|
   |Gebruikersnaam|sa|
   |Wachtwoord|Het sterke wachtwoord dat is gebruikt bij het maken van de Azure SQL Edge-instance|
   |Database|Standaard|
   |Servergroep|Standaard|
   |Naam (optioneel)|Geef desgewenst een naam op|

3. Klik op **Verbinden**

4. Open in de sectie **Bestand** een nieuw notitieblok of gebruik de sneltoets Alt+Windows+N. 

5. Stel de kernel in op Python 3.

## <a name="predict-iron-ore-impurities-with-onnx"></a>Verontreinigingen in ijzererts voorspellen met ONNX

Voer de volgende Python-code in het Azure Data Studio-notitieblok in en voer deze uit.

1. Installeer en importeer eerst de vereiste pakketten.

   ```python
   !pip install azureml.core -q
   !pip install azureml.train.automl -q
   !pip install matplotlib -q
   !pip install pyodbc -q
   !pip install spicy -q
   
   import logging
   from matplotlib import pyplot as plt
   import numpy as np
   import pandas as pd
   import pyodbc
   
   from scipy import stats
   from scipy.stats import skew #for some statistics
   
   import azureml.core
   from azureml.core.experiment import Experiment
   from azureml.core.workspace import Workspace
   from azureml.train.automl import AutoMLConfig
   from azureml.train.automl import constants
   ```

1. Definieer de Azure AutoML-werkruimte en de AutoML-experimentconfiguratie voor het regressie-experiment.

   ```python
   ws = Workspace(subscription_id="<Azure Subscription ID>",
                  resource_group="<resource group name>",
                  workspace_name="<ML workspace name>")
   # Choose a name for the experiment.
   experiment_name = 'silic_percent2-Forecasting-onnx'
   experiment = Experiment(ws, experiment_name)
   ```

1. Importeer de gegevensset in een Panda-frame. Gebruik voor de training van het model de traininggegevensset [Quality Prediction in a Mining Process](https://www.kaggle.com/edumagalhaes/quality-prediction-in-a-mining-process) (kwaliteitsvoorspelling in een mijnproces) van Kaggle. Download het gegevensbestand en sla het lokaal op uw ontwikkelcomputer op. U gebruikt deze gegevens om te voorspellen hoeveel verontreinigingen het ertsconcentraat bevat.

   ```python
   df = pd.read_csv("<local path where you have saved the data file>",decimal=",",parse_dates=["date"],infer_datetime_format=True)
   df = df.drop(['date'],axis=1)
   df.describe()
   ```

1. Analyseer de gegevens om eventuele scheefheid te identificeren. Bekijk tijdens dit proces de distributie en de scheefheidsgegevens voor elk van de kolommen in het gegevensframe.

   ```python
   ## We can use a histogram chart to view the data distribution for the Dataset. In this example, we are looking at the histogram for the "% Silica Concentrate" 
   ## and the "% Iron Feed". From the histogram, you'll notice the data distribution is skewed for most of the features in the dataset. 
   
   f, (ax1,ax2,ax3) = plt.subplots(1,3)
   ax1.hist(df['% Iron Feed'], bins='auto')
   #ax1.title = 'Iron Feed'
   ax2.hist(df['% Silica Concentrate'], bins='auto')
   #ax2.title = 'Silica Concentrate'
   ax3.hist(df['% Silica Feed'], bins='auto')
   #ax3.title = 'Silica Feed'
   ```

1. Controleer en corrigeer het scheefheidsniveau in de gegevens.

   ```python
   ##Check data skewness with the skew or the kurtosis function in spicy.stats
   ##Skewness using the spicy.stats skew function
   for i in list(df):
           print('Skew value for column "{0}" is: {1}'.format(i,skew(df[i])))
   
   #Fix the Skew using Box Cox Transform
   from scipy.special import boxcox1p
   for i in list(df):
       if(abs(skew(df[i])) >= 0.20):
           #print('found skew in column - {0}'.format(i))
           df[i] = boxcox1p(df[i], 0.10)
           print('Skew value for column "{0}" is: {1}'.format(i,skew(df[i])))
   ```

1. Controleer de correlatie van andere kenmerken met het voorspellingskenmerk. Als de correlatie niet hoog is, verwijdert u deze kenmerken.

   ```python
   silic_corr = df.corr()['% Silica Concentrate']
   silic_corr = abs(silic_corr).sort_values()
   drop_index= silic_corr.index[:8].tolist()
   df = df.drop(drop_index, axis=1)
   df.describe()
   ```

1. Start het AzureML-experiment om het beste algoritme te vinden en te trainen. In dit geval test u met alle regressiealgoritmen, met als primaire metriek de genormaliseerde gemiddelde kwadratische fout (NRMSE = Normalized Root Mean Squared Error). Zie [Primaire metriek voor Azure ML-experimenten](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#primary-metric)voor meer informatie. Met de volgende code wordt een lokale uitvoering van het ML-experiment gestart.

   ```python
   ## Define the X_train and the y_train data sets for the AutoML experiments. X_Train are the inputs or the features, while y_train is the outcome or the prediction result. 
   
   y_train = df['% Silica Concentrate']
   x_train = df.iloc[:,0:-1]
   automl_config = AutoMLConfig(task = 'regression',
                                primary_metric = 'normalized_root_mean_squared_error',
                                iteration_timeout_minutes = 60,
                                iterations = 10,                        
                                X = x_train, 
                                y = y_train,
                                featurization = 'off',
                                enable_onnx_compatible_models=True)
   
   local_run = experiment.submit(automl_config, show_output = True)
   best_run, onnx_mdl = local_run.get_output(return_onnx_model=True)
   ```

1. Laad het model in Azure SQL Edge Database voor lokaal scoren.

   ```python
   ## Load the Model into a SQL Database.
   ## Define the Connection string parameters. These connection strings will be used later also in the demo.
   server = '<SQL Server IP address>'
   username = 'sa' # SQL Server username
   password = '<SQL Server password>'
   database = 'IronOreSilicaPrediction'
   db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
   conn = pyodbc.connect(db_connection_string, autocommit=True)
   cursor = conn.cursor()
   
   # Insert the ONNX model into the models table
   query = f"insert into models ([description], [data]) values ('Silica_Percentage_Predict_Regression_NRMSE_New1',?)"
   model_bits = onnx_mdl.SerializeToString()
   insert_params  = (pyodbc.Binary(model_bits))
   cursor.execute(query, insert_params)
   conn.commit()
   cursor.close()
   conn.close()
   ```

1. Ten slotte kunt u het Azure SQL Edge-model gebruiken om voorspellingen te doen met behulp van het getrainde model.

   ```python
   ## Define the Connection string parameters. These connection strings will be used later also in the demo.
   server = '<SQL Server IP address>'
   username = 'sa' # SQL Server username
   password = '<SQL Server password>'
   database = 'IronOreSilicaPrediction'
   db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
   conn = pyodbc.connect(db_connection_string, autocommit=True)
   #cursor = conn.cursor()
   query = \
           f'declare @model varbinary(max) = (Select [data] from [dbo].[Models] where [id] = 1);' \
           f' with d as ( SELECT  [timestamp] ,cast([cur_Iron_Feed] as real) [__Iron_Feed] ,cast([cur_Silica_Feed]  as real) [__Silica_Feed]' \
           f',cast([cur_Starch_Flow] as real) [Starch_Flow],cast([cur_Amina_Flow] as real) [Amina_Flow]' \
           f' ,cast([cur_Ore_Pulp_pH] as real) [Ore_Pulp_pH] ,cast([cur_Flotation_Column_01_Air_Flow] as real) [Flotation_Column_01_Air_Flow]' \
           f' ,cast([cur_Flotation_Column_02_Air_Flow] as real) [Flotation_Column_02_Air_Flow]' \
           f' ,cast([cur_Flotation_Column_03_Air_Flow] as real) [Flotation_Column_03_Air_Flow]' \
           f' ,cast([cur_Flotation_Column_07_Air_Flow] as real) [Flotation_Column_07_Air_Flow]' \
           f' ,cast([cur_Flotation_Column_04_Level] as real) [Flotation_Column_04_Level]' \
           f' ,cast([cur_Flotation_Column_05_Level] as real) [Flotation_Column_05_Level]' \
           f' ,cast([cur_Flotation_Column_06_Level] as real) [Flotation_Column_06_Level]' \
           f' ,cast([cur_Flotation_Column_07_Level] as real) [Flotation_Column_07_Level]' \
           f' ,cast([cur_Iron_Concentrate] as real) [__Iron_Concentrate]' \
           f' FROM [dbo].[IronOreMeasurements1]' \
           f' where timestamp between dateadd(hour,-1,getdate()) and getdate()) ' \
           f' SELECT d.*, p.variable_out1' \
           f' FROM PREDICT(MODEL = @model, DATA = d) WITH(variable_out1 numeric(25,17)) as p;' 
     
   df_result = pd.read_sql(query,conn)
   df_result.describe()
   ```

1. Maak met behulp van Python een grafiek van het voorspelde silicapercentage op basis van de ijzerfeed, datum/tijd en silicafeed.

   ```python
   import plotly.graph_objects as go
   fig = go.Figure()
   fig.add_trace(go.Scatter(x=df_result['timestamp'],y=df_result['__Iron_Feed'],mode='lines+markers',name='Iron Feed',line=dict(color='firebrick', width=2)))
   fig.add_trace(go.Scatter(x=df_result['timestamp'],y=df_result['__Silica_Feed'],mode='lines+markers',name='Silica Feed',line=dict(color='green', width=2)))
   fig.add_trace(go.Scatter(x=df_result['timestamp'],y=df_result['variable_out1'],mode='lines+markers',name='Silica Percent',line=dict(color='royalblue', width=3)))
   fig.update_layout(height= 600, width=1500,xaxis_title='Time')
   fig.show()
   ```

## <a name="next-steps"></a>Volgende stappen

Zie [Machine learning and AI with ONNX in SQL Edge (Preview)](onnx-overview.md) voor meer informatie over het gebruiken van ONNX-modules in Azure SQL Edge.
