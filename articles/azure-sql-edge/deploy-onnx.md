---
title: Voor spellingen implementeren en maken met ONNX in Azure SQL Edge (preview)
description: Meer informatie over het trainen van een model, het converteren van het naar ONNX, het implementeren ervan naar Azure SQL Edge (preview) en het uitvoeren van systeem eigen voor spel op gegevens met het geüploade ONNX-model.
keywords: SQL-rand implementeren
services: sql-edge
ms.service: sql-edge
ms.subservice: machine-learning
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.date: 05/19/2020
ms.openlocfilehash: d2ca4cf57cf9e59477688c8e18cf38d734930082
ms.sourcegitcommit: bf8c447dada2b4c8af017ba7ca8bfd80f943d508
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/25/2020
ms.locfileid: "85368087"
---
# <a name="deploy-and-make-predictions-with-an-onnx-model-in-azure-sql-edge-preview"></a>Voor spellingen implementeren en maken met een ONNX-model in Azure SQL Edge (preview)

In deze Quick Start leert u hoe u een model traint, converteert naar ONNX, het implementeert in Azure SQL Edge (preview) en vervolgens systeem eigen voor beeld uitvoert op gegevens met behulp van het geüploade ONNX-model. Zie [machine learning en AI with ONNX in SQL Edge (preview)](onnx-overview.md)voor meer informatie.

Deze Quick start is gebaseerd op **scikit-Learn** en maakt gebruik van de [Boston huisvesting-gegevensset](https://scikit-learn.org/stable/modules/generated/sklearn.datasets.load_boston.html).

## <a name="before-you-begin"></a>Voordat u begint

* Als u nog geen Azure SQL Edge-module hebt geïmplementeerd, volgt u de stappen voor [het implementeren van SQL Edge (preview) met behulp van de Azure Portal](deploy-portal.md).

* Installeer [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/download).

* Open Azure Data Studio en voer de volgende stappen uit om de pakketten te installeren die nodig zijn voor deze Quick Start:

    1. Open een [Nieuw notitie blok](https://docs.microsoft.com/sql/azure-data-studio/sql-notebooks) dat is verbonden met de python 3-kernel. 
    1. Klik op **Pakketten beheren** en klik onder **nieuwe toevoegen**, zoek naar **scikit-Learn**en installeer het pakket met scikit-informatie. 
    1. Installeer ook de **installatie**-, **numpy**-, **onnxmltools**-, **onnxruntime**-, **skl2onnx**-, **pyodbc**-en **sqlalchemy** -pakketten.
    
* Voer voor elk script onderdeel dat hieronder in een cel in de Azure Data Studio notitie blok in en voer de cel uit.

## <a name="train-a-pipeline"></a>Een pijp lijn trainen

Splits de gegevensset om functies te gebruiken om de mediaan waarde van een huis te voors pellen.

```python
import numpy as np
import onnxmltools
import onnxruntime as rt
import pandas as pd
import skl2onnx
import sklearn
import sklearn.datasets

from sklearn.datasets import load_boston
boston = load_boston()
boston

df = pd.DataFrame(data=np.c_[boston['data'], boston['target']], columns=boston['feature_names'].tolist() + ['MEDV'])
 
target_column = 'MEDV'
 
# Split the data frame into features and target
x_train = pd.DataFrame(df.drop([target_column], axis = 1))
y_train = pd.DataFrame(df.iloc[:,df.columns.tolist().index(target_column)])

print("\n*** Training dataset x\n")
print(x_train.head())

print("\n*** Training dataset y\n")
print(y_train.head())
```

**Uitvoer**:

```text
*** Training dataset x

        CRIM    ZN  INDUS  CHAS    NOX     RM   AGE     DIS  RAD    TAX  \
0  0.00632  18.0   2.31   0.0  0.538  6.575  65.2  4.0900  1.0  296.0
1  0.02731   0.0   7.07   0.0  0.469  6.421  78.9  4.9671  2.0  242.0
2  0.02729   0.0   7.07   0.0  0.469  7.185  61.1  4.9671  2.0  242.0
3  0.03237   0.0   2.18   0.0  0.458  6.998  45.8  6.0622  3.0  222.0
4  0.06905   0.0   2.18   0.0  0.458  7.147  54.2  6.0622  3.0  222.0

    PTRATIO       B  LSTAT  
0     15.3  396.90   4.98  
1     17.8  396.90   9.14  
2     17.8  392.83   4.03  
3     18.7  394.63   2.94  
4     18.7  396.90   5.33  

*** Training dataset y

0    24.0
1    21.6
2    34.7
3    33.4
4    36.2
Name: MEDV, dtype: float64
```

Maak een pijp lijn om het LinearRegression-model te trainen. U kunt ook andere regressie modellen gebruiken.

```python
from sklearn.compose import ColumnTransformer
from sklearn.linear_model import LinearRegression
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import RobustScaler

continuous_transformer = Pipeline(steps=[('scaler', RobustScaler())])

# All columns are numeric - normalize them
preprocessor = ColumnTransformer(
    transformers=[
        ('continuous', continuous_transformer, [i for i in range(len(x_train.columns))])])

model = Pipeline(
    steps=[
        ('preprocessor', preprocessor),
        ('regressor', LinearRegression())])

# Train the model
model.fit(x_train, y_train)
```

Controleer de nauw keurigheid van het model en Bereken vervolgens de R2-Score en de gemiddelde fout.

```python
# Score the model
from sklearn.metrics import r2_score, mean_squared_error
y_pred = model.predict(x_train)
sklearn_r2_score = r2_score(y_train, y_pred)
sklearn_mse = mean_squared_error(y_train, y_pred)
print('*** Scikit-learn r2 score: {}'.format(sklearn_r2_score))
print('*** Scikit-learn MSE: {}'.format(sklearn_mse))
```

**Uitvoer**:

```text
*** Scikit-learn r2 score: 0.7406426641094094
*** Scikit-learn MSE: 21.894831181729206
```

## <a name="convert-the-model-to-onnx"></a>Het model converteren naar ONNX

Converteer de gegevens typen naar de ondersteunde SQL-gegevens typen. Deze conversie is ook vereist voor andere dataframes.

```python
from skl2onnx.common.data_types import FloatTensorType, Int64TensorType, DoubleTensorType

def convert_dataframe_schema(df, drop=None, batch_axis=False):
    inputs = []
    nrows = None if batch_axis else 1
    for k, v in zip(df.columns, df.dtypes):
        if drop is not None and k in drop:
            continue
        if v == 'int64':
            t = Int64TensorType([nrows, 1])
        elif v == 'float32':
            t = FloatTensorType([nrows, 1])
        elif v == 'float64':
            t = DoubleTensorType([nrows, 1])
        else:
            raise Exception("Bad type")
        inputs.append((k, t))
    return inputs
```

Gebruik `skl2onnx` , converteer het LinearRegression-model naar de ONNX-indeling en sla het bestand lokaal op.

```python
# Convert the scikit model to onnx format
onnx_model = skl2onnx.convert_sklearn(model, 'Boston Data', convert_dataframe_schema(x_train))
# Save the onnx model locally
onnx_model_path = 'boston1.model.onnx'
onnxmltools.utils.save_model(onnx_model, onnx_model_path)
```

## <a name="test-the-onnx-model"></a>Het ONNX-model testen

Nadat u het model hebt geconverteerd naar de ONNX-indeling, moet u het model beoordelen om weinig te laten zien op de prestaties.

> [!NOTE]
> ONNX runtime maakt gebruik van drijvende komma in plaats van dubbele precisie, waardoor kleine verschillen mogelijk zijn.

```python
import onnxruntime as rt
sess = rt.InferenceSession(onnx_model_path)

y_pred = np.full(shape=(len(x_train)), fill_value=np.nan)

for i in range(len(x_train)):
    inputs = {}
    for j in range(len(x_train.columns)):
        inputs[x_train.columns[j]] = np.full(shape=(1,1), fill_value=x_train.iloc[i,j])

    sess_pred = sess.run(None, inputs)
    y_pred[i] = sess_pred[0][0][0]

onnx_r2_score = r2_score(y_train, y_pred)
onnx_mse = mean_squared_error(y_train, y_pred)

print()
print('*** Onnx r2 score: {}'.format(onnx_r2_score))
print('*** Onnx MSE: {}\n'.format(onnx_mse))
print('R2 Scores are equal' if sklearn_r2_score == onnx_r2_score else 'Difference in R2 scores: {}'.format(abs(sklearn_r2_score - onnx_r2_score)))
print('MSE are equal' if sklearn_mse == onnx_mse else 'Difference in MSE scores: {}'.format(abs(sklearn_mse - onnx_mse)))
print()
```

**Uitvoer**:

```text
*** Onnx r2 score: 0.7406426691136831
*** Onnx MSE: 21.894830759270633

R2 Scores are equal
MSE are equal
```

## <a name="insert-the-onnx-model"></a>Het ONNX-model invoegen

Sla het model op in Azure SQL Edge, in een `models` tabel in een Data Base `onnx` . Geef in het connection string het **server adres**, de **gebruikers naam**en het **wacht woord**op.

```python
import pyodbc

server = '' # SQL Server IP address
username = '' # SQL Server username
password = '' # SQL Server password

# Connect to the master DB to create the new onnx database
connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=master;UID=" + username + ";PWD=" + password + ";"

conn = pyodbc.connect(connection_string, autocommit=True)
cursor = conn.cursor()

database = 'onnx'
query = 'DROP DATABASE IF EXISTS ' + database
cursor.execute(query)
conn.commit()

# Create onnx database
query = 'CREATE DATABASE ' + database
cursor.execute(query)
conn.commit()

# Connect to onnx database
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"

conn = pyodbc.connect(db_connection_string, autocommit=True)
cursor = conn.cursor()

table_name = 'models'

# Drop the table if it exists
query = f'drop table if exists {table_name}'
cursor.execute(query)
conn.commit()

# Create the model table
query = f'create table {table_name} ( ' \
    f'[id] [int] IDENTITY(1,1) NOT NULL, ' \
    f'[data] [varbinary](max) NULL, ' \
    f'[description] varchar(1000))'
cursor.execute(query)
conn.commit()

# Insert the ONNX model into the models table
query = f"insert into {table_name} ([description], [data]) values ('Onnx Model',?)"

model_bits = onnx_model.SerializeToString()

insert_params  = (pyodbc.Binary(model_bits))
cursor.execute(query, insert_params)
conn.commit()
```

## <a name="load-the-data"></a>De gegevens laden

Laad de gegevens in Azure SQL Edge.

Maak eerst twee tabellen, **onderdelen** en **doel items**om subsets van de Boston-Veste-gegevensset op te slaan.

* **Functies** bevatten alle gegevens die worden gebruikt voor het voors pellen van het doel, mediaan waarde. 
* **Doel** bevat de mediaan waarde voor elke record in de gegevensset. 

```python
import sqlalchemy
from sqlalchemy import create_engine
import urllib

db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"

conn = pyodbc.connect(db_connection_string)
cursor = conn.cursor()

features_table_name = 'features'

# Drop the table if it exists
query = f'drop table if exists {features_table_name}'
cursor.execute(query)
conn.commit()

# Create the features table
query = \
    f'create table {features_table_name} ( ' \
    f'    [CRIM] float, ' \
    f'    [ZN] float, ' \
    f'    [INDUS] float, ' \
    f'    [CHAS] float, ' \
    f'    [NOX] float, ' \
    f'    [RM] float, ' \
    f'    [AGE] float, ' \
    f'    [DIS] float, ' \
    f'    [RAD] float, ' \
    f'    [TAX] float, ' \
    f'    [PTRATIO] float, ' \
    f'    [B] float, ' \
    f'    [LSTAT] float, ' \
    f'    [id] int)'

cursor.execute(query)
conn.commit()

target_table_name = 'target'

# Create the target table
query = \
    f'create table {target_table_name} ( ' \
    f'    [MEDV] float, ' \
    f'    [id] int)'

x_train['id'] = range(1, len(x_train)+1)
y_train['id'] = range(1, len(y_train)+1)

print(x_train.head())
print(y_train.head())
```

Ten slotte kunt u gebruiken `sqlalchemy` om de `x_train` en `y_train` Panda dataframes in te voegen in de tabellen `features` en `target` respectievelijk. 

```python
db_connection_string = 'mssql+pyodbc://' + username + ':' + password + '@' + server + '/' + database + '?driver=ODBC+Driver+17+for+SQL+Server'
sql_engine = sqlalchemy.create_engine(db_connection_string)
x_train.to_sql(features_table_name, sql_engine, if_exists='append', index=False)
y_train.to_sql(target_table_name, sql_engine, if_exists='append', index=False)
```

U kunt nu de gegevens in de data base bekijken.

## <a name="run-predict-using-the-onnx-model"></a>Voor SPELing uitvoeren met het ONNX-model

Gebruik het model in Azure SQL Edge om systeem eigen voor SPELing uit te voeren op de gegevens met behulp van het geüploade ONNX-model.

> [!NOTE]
> Wijzig de kernel van de notebook in SQL om de resterende cel uit te voeren.

```sql
USE onnx

DECLARE @model VARBINARY(max) = (
        SELECT DATA
        FROM dbo.models
        WHERE id = 1
        );

WITH predict_input
AS (
    SELECT TOP (1000) [id]
        , CRIM
        , ZN
        , INDUS
        , CHAS
        , NOX
        , RM
        , AGE
        , DIS
        , RAD
        , TAX
        , PTRATIO
        , B
        , LSTAT
    FROM [onnx].[dbo].[features]
    )
SELECT predict_input.id
    , p.variable1 AS MEDV
FROM PREDICT(MODEL = @model, DATA = predict_input, RUNTIME=ONNX) WITH (variable1 FLOAT) AS p;
```

## <a name="next-steps"></a>Volgende stappen

* [Machine Learning en AI met ONNX in SQL Edge](onnx-overview.md)
