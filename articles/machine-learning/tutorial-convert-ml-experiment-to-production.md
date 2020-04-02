---
title: Machine learning-experimentcode converteren naar productiecode
titleSuffix: Azure Machine Learning
description: Meer informatie over het converteren van experimentele code voor machine learning naar productiecode met behulp van de MLOpsPython-codesjabloon.
author: bjcmit
ms.author: brysmith
ms.service: machine-learning
ms.topic: tutorial
ms.date: 03/13/2020
ms.openlocfilehash: e3c9b16ae3d2b06ec19ecd29d15762a065c0c1ae
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521443"
---
# <a name="tutorial-convert-ml-experimental-code-to-production-code"></a>Zelfstudie: ML-experimentele code converteren naar productiecode

Een machine learning-project vereist experimenten waarbij hypothesen worden getest met flexibele tools zoals Jupyter Notebook met behulp van echte datasets. Zodra het model klaar is voor productie, moet de modelcode in een opslagplaats voor productiecode worden geplaatst. In sommige gevallen moet de modelcode worden geconverteerd naar Python-scripts die in de opslagplaats van de productiecode moeten worden geplaatst. Deze zelfstudie behandelt een aanbevolen benadering voor het exporteren van experimenteercode naar Python-scripts.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Schone niet-essentiële code
> * Refactor Jupyter Notebook code in functies
> * Python-scripts maken voor gerelateerde taken
> * Eenheidstests maken

## <a name="prerequisites"></a>Vereisten

- Genereer de [SJABLOON MLOpsPython](https://github.com/microsoft/MLOpsPython/generate) en gebruik de `experimentation/Diabetes Ridge Regression Training.ipynb` en `experimentation/Diabetes Ridge Regression Scoring.ipynb` notebooks. Deze notitieblokken worden gebruikt als een voorbeeld van het omzetten van experimenten naar productie. U vindt deze [https://github.com/microsoft/MLOpsPython/tree/master/experimentation](https://github.com/microsoft/MLOpsPython/tree/master/experimentation)notebooks op .
- Installeer `nbconvert`. Volg alleen de installatie-instructies onder sectie __Het installeren van nbconvert__ op de [installatiepagina.](https://nbconvert.readthedocs.io/en/latest/install.html)

## <a name="remove-all-nonessential-code"></a>Alle niet-essentiële code verwijderen

Sommige code geschreven tijdens experimenten is alleen bedoeld voor verkennende doeleinden. Daarom is de eerste stap om experimentele code om te zetten in productiecode is om deze niet-essentiële code te verwijderen. Het verwijderen van niet-essentiële code maakt de code ook beter te onderhouden. In deze sectie verwijdert u code `experimentation/Diabetes Ridge Regression Training.ipynb` uit het notitieblok. De instructies afdrukken `X` van `y` de vorm `features.describe` van en en de cel aanroepen zijn alleen voor data-exploratie en kan worden verwijderd. Na het verwijderen `experimentation/Diabetes Ridge Regression Training.ipynb` van niet-essentiële code, moet eruit zien als de volgende code zonder afwaardering:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib
import pandas as pd

sample_data = load_diabetes()

df = pd.DataFrame(
    data=sample_data.data,
    columns=sample_data.feature_names)
df['Y'] = sample_data.target

X = df.drop('Y', axis=1).values
y = df['Y'].values

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=0)
data = {"train": {"X": X_train, "y": y_train},
        "test": {"X": X_test, "y": y_test}}

args = {
    "alpha": 0.5
}

reg_model = Ridge(**args)
reg.fit(data["train"]["X"], data["train"]["y"])

preds = reg_model.predict(data["test"]["X"])
mse = mean_squared_error(preds, y_test)
metrics = {"mse": mse}
print(metrics)

model_name = "sklearn_regression_model.pkl"
joblib.dump(value=reg, filename=model_name)
```

## <a name="refactor-code-into-functions"></a>Refactorcode in functies

Ten tweede moet de Jupyter-code worden omgezet in functies. Refactoring code in functies maakt het testen van eenheden gemakkelijker en maakt de code meer onderhoudbaar. In deze sectie moet u refactor:

- De Diabetes Ridge Regression`experimentation/Diabetes Ridge Regression Training.ipynb`Training notebook( )
- De Diabetes Ridge Regression`experimentation/Diabetes Ridge Regression Scoring.ipynb`Scoring notebook( )

### <a name="refactor-diabetes-ridge-regression-training-notebook-into-functions"></a>Refactor Diabetes Ridge Regression Training notebook in functies

Voer `experimentation/Diabetes Ridge Regression Training.ipynb`in de volgende stappen uit:

1. Maak een `split_data` functie aangeroepen om het gegevensframe op te splitsen in test- en treingegevens. De functie moet het `df` gegevensframe als parameter nemen en `train` een `test`woordenboek met de sleutels retourneren en .

    Verplaats de code onder de kop *Gegevens splitsen in trainings- en validatiesets* naar de `split_data` functie en wijzig deze om het `data` object terug te sturen.

1. Maak een `train_model`functie genaamd , `data` `args` die de parameters neemt en en retourneert een getraind model.

    Verplaats de code onder het kopje *Trainingsmodel op trainingsset* naar de `train_model` functie en wijzig deze om het `reg_model` object terug te sturen. Verwijder `args` het woordenboek, de waarden `args` komen uit de parameter.

1. Maak een `get_model_metrics`functie genaamd `reg_model` , `data`die parameters neemt en , en evalueert het model retourneert vervolgens een woordenboek van metrische gegevens voor het getrainde model.

    Verplaats de code onder de kop *Model valideren op validatieset* naar de `get_model_metrics` functie en wijzig deze om het `metrics` object terug te sturen.

De drie functies moeten als volgt zijn:

```python
# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics
```

Nog `experimentation/Diabetes Ridge Regression Training.ipynb`steeds in, de volgende stappen uitvoeren:

1. Maak een nieuwe `main`functie genaamd , die geen parameters neemt en niets retourneert.
1. Verplaats de code onder de kop `main` 'Gegevens laden' naar de functie.
1. Aanroepingen voor de nieuw geschreven `main` functies toevoegen aan de functie:
    ```python
    # Split Data into Training and Validation Sets
    data = split_data(df)
    ```

    ```python
    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)
    ```

    ```python
    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)
    ```
1. Verplaats de code onder de kop `main` Model opslaan naar de functie.

De `main` functie moet er uitzien als de volgende code:

```python
def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)
```

In dit stadium mag er geen code meer in het notitieblok aanwezig zijn die geen functie heeft, behalve importinstructies in de eerste cel.

Voeg een instructie `main` toe die de functie aanroept.

```python
main()
```

Na refactoring, `experimentation/Diabetes Ridge Regression Training.ipynb` moet eruit zien als de volgende code zonder de markdown:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import pandas as pd
import joblib


# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics


def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)

main()
```

### <a name="refactor-diabetes-ridge-regression-scoring-notebook-into-functions"></a>Refactor Diabetes Ridge Regression Scoring notebook in functies

Voer `experimentation/Diabetes Ridge Regression Scoring.ipynb`in de volgende stappen uit:

1. Maak een nieuwe `init`functie genaamd , die geen parameters neemt en niets terug te keren.
1. Kopieer de code onder de kop `init` 'Laadmodel' naar de functie.

De `init` functie moet er uitzien als de volgende code:

```python
def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

Zodra `init` de functie is gemaakt, vervangt u alle code onder het `init` kopje "Laadmodel" als volgt:

```python
init()
```

Voer `experimentation/Diabetes Ridge Regression Scoring.ipynb`in de volgende stappen uit:

1. Maak een nieuwe `run`functie `raw_data` genaamd `request_headers` , die neemt en als parameters en retourneert een woordenboek van de resultaten als volgt:

    ```python
    {"result": result.tolist()}
    ```

1. Kopieer de code onder de koppen 'Gegevens voorbereiden' `run` en 'Gegevens scoren' in de functie.

    De `run` functie moet er uitzien als de volgende code (Vergeet `request_headers`niet om de instructies `run` te verwijderen die de variabelen `raw_data` instellen en , die later worden gebruikt wanneer de functie wordt aangeroepen):

    ```python
    def run(raw_data, request_headers):
        data = json.loads(raw_data)["data"]
        data = numpy.array(data)
        result = model.predict(data)

        return {"result": result.tolist()}
    ```

Zodra `run` de functie is gemaakt, vervangt u alle code onder de koppen 'Gegevens voorbereiden' en 'Gegevens scoren' door de volgende code:

```python
raw_data = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(raw_data, request_header)
print("Test result: ", prediction)
```

De vorige code `raw_data` stelt `request_header`variabelen `run` in `raw_data` en `request_header`roept de functie aan met en , en drukt de voorspellingen af.

Na refactoring, `experimentation/Diabetes Ridge Regression Scoring.ipynb` moet eruit zien als de volgende code zonder de markdown:

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, {})
print("Test result: ", prediction)
```

## <a name="combine-related-functions-in-python-files"></a>Gerelateerde functies in Python-bestanden combineren

Ten derde moeten gerelateerde functies worden samengevoegd in Python-bestanden om het hergebruik van code beter te kunnen gebruiken. In deze sectie maakt u Python-bestanden voor de volgende notitieblokken:

- De Diabetes Ridge Regression`experimentation/Diabetes Ridge Regression Training.ipynb`Training notebook( )
- De Diabetes Ridge Regression`experimentation/Diabetes Ridge Regression Scoring.ipynb`Scoring notebook( )

### <a name="create-python-file-for-the-diabetes-ridge-regression-training-notebook"></a>Python-bestand maken voor het notitieblok Van de Diabetes Ridge Regression Training

Converteer uw notitieblok naar een uitvoerbaar script door de volgende `nbconvert` instructie in `experimentation/Diabetes Ridge Regression Training.ipynb`een opdrachtprompt uit te voeren, waarin het pakket en het pad van:

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Training.ipynb" –output train
```

Zodra het notitieblok is `train.py`geconverteerd naar, verwijder ongewenste opmerkingen. Vervang de `main()` aanroep aan het einde van het bestand door een voorwaardelijke aanroep zoals de volgende code:

```python
if __name__ == '__main__':
    main()
```

Uw `train.py` bestand moet er uitzien als de volgende code:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import pandas as pd
import joblib


# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics


def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)

if __name__ == '__main__':
    main()
```

`train.py`kan nu worden aangeroepen vanuit `python train.py`een terminal door te draaien .
De functies `train.py` van kan ook worden opgeroepen uit andere bestanden.

Het `train_aml.py` bestand in `diabetes_regression/training` de map in de MLOpsPython-repository roept de functies aan die zijn gedefinieerd in `train.py` de context van een Azure Machine Learning-experiment. De functies kunnen ook worden opgeroepen in unit tests, later behandeld in deze gids.

### <a name="create-python-file-for-the-diabetes-ridge-regression-scoring-notebook"></a>Python-bestand maken voor het notitieblok 'Diabetes Ridge Regression Scoring'.

Verkapt uw notitieblok tot een uitvoerbaar script door de volgende `nbconvert` instructie uit te `experimentation/Diabetes Ridge Regression Scoring.ipynb`voeren in een opdrachtprompt die het pakket en het pad van :

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Scoring.ipynb" –output score
```

Zodra het notitieblok is `score.py`geconverteerd naar, verwijder ongewenste opmerkingen. Uw `score.py` bestand moet er uitzien als de volgende code:

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, request_header)
print("Test result: ", prediction)
```

De `model` variabele moet globaal zijn, zodat deze zichtbaar is in het hele script. Voeg de volgende instructie toe `init` aan het begin van de functie:

```python
global model
```

Na het toevoegen van `init` de vorige instructie moet de functie er uitzien als de volgende code:

```python
def init():
    global model

    # load the model from file into a global object
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

## <a name="create-unit-tests-for-each-python-file"></a>Eenheidstests maken voor elk Python-bestand

Ten vierde, maak eenheidstests voor uw Python-functies. Unittests beschermen code tegen functionele regressies en maken het gemakkelijker te onderhouden. In deze sectie maakt u eenheidstests voor `train.py`de functies in .

`train.py`bevat meerdere functies, maar we maken slechts één `train_model` eenheidstest voor de functie met behulp van het Pytest-framework in deze zelfstudie. Pytest is niet het enige Python-testframework, maar het is een van de meest gebruikte. Ga voor meer informatie naar [Pytest.](https://pytest.org)

Een eenheidstest bevat meestal drie hoofdacties:

- Object rangschikken - noodzakelijke objecten maken en instellen
- Handelen op een object
- Beweren wat er wordt verwacht

De eenheidstest `train_model` zal bellen met een aantal hard-gecodeerde gegevens en argumenten, en valideren dat `train_model` gehandeld zoals verwacht met behulp van de resulterende getrainde model om een voorspelling te maken en het vergelijken van die voorspelling met een verwachte waarde.

```python
import numpy as np
from code.training.train import train_model


def test_train_model():
    # Arrange
    X_train = np.array([1, 2, 3, 4, 5, 6]).reshape(-1, 1)
    y_train = np.array([10, 9, 8, 8, 6, 5])
    data = {"train": {"X": X_train, "y": y_train}}

    # Act
    reg_model = train_model(data, {"alpha": 1.2})

    # Assert
    preds = reg_model.predict([[1], [2]])
    np.testing.assert_almost_equal(preds, [9.93939393939394, 9.03030303030303])
```

## <a name="next-steps"></a>Volgende stappen

Nu u begrijpt hoe u converteren van een experiment naar productiecode, raadpleegt u de volgende koppelingen voor meer informatie en de volgende stappen:

+ [MLOpsPython](https://github.com/microsoft/MLOpsPython/blob/master/docs/custom_model.md): Een CI/CD-pijplijn bouwen om uw eigen model te trainen, evalueren en implementeren met Azure Pipelines en Azure Machine Learning
+ [Azure ML-experiment-uitvoeringen en -statistieken bewaken](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments)
+ [Gegevens van ML-eindpunten van webservice controleren en verzamelen](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights)
