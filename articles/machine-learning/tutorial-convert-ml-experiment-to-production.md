---
title: Notitieblok code converteren naar python-scripts
titleSuffix: Azure Machine Learning
description: Zet uw machine learning experimentele notebooks om in productie klare code met behulp van de MLOpsPython-code sjabloon. Vervolgens kunt u deze code testen, implementeren en automatiseren.
author: bjcmit
ms.author: brysmith
ms.service: machine-learning
ms.topic: tutorial
ms.date: 04/30/2020
ms.openlocfilehash: a0b66f233de9e1bfdc6d011b65489884a1049a12
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82559678"
---
# <a name="tutorial-convert-ml-experiments-to-production-python-code"></a>Zelf studie: ML experimenten omzetten naar productie python-code

In deze zelf studie leert u hoe u Juptyer-notebooks kunt omzetten in python-scripts om IT-tests en automatiserings vriendelijk te maken met behulp van de MLOpsPython-code sjabloon en Azure Machine Learning. Dit proces wordt doorgaans gebruikt om experimenteren/trainingen uit te voeren vanuit een Juptyer-notebook en dit te converteren naar python-scripts. Deze scripts kunnen vervolgens worden gebruikt voor testen en CI/CD-automatisering in uw productie omgeving. 

Een machine learning project vereist experimenten waarbij hypo Thesen worden getest met Agile-hulpprogram ma's zoals Jupyter Notebook met echte gegevens sets. Zodra het model klaar is voor productie, moet de model code in een opslag plaats voor productie code worden geplaatst. In sommige gevallen moet de model code worden geconverteerd naar python-scripts die in de productie code opslagplaats moeten worden geplaatst. In deze zelf studie wordt een aanbevolen benadering beschreven voor het exporteren van experimenten code naar python-scripts.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Niet-essentiële code wissen
> * Refactorion-Jupyter Notebook code in functions
> * Python-scripts maken voor verwante taken
> * Eenheidstests maken

## <a name="prerequisites"></a>Vereisten

- Genereer de [MLOpsPython-sjabloon](https://github.com/microsoft/MLOpsPython/generate) en `experimentation/Diabetes Ridge Regression Training.ipynb` gebruik `experimentation/Diabetes Ridge Regression Scoring.ipynb` de en-notebooks. Deze notitie blokken worden gebruikt als voor beeld van het converteren van experimenten naar productie. U kunt deze notitie blokken vinden [https://github.com/microsoft/MLOpsPython/tree/master/experimentation](https://github.com/microsoft/MLOpsPython/tree/master/experimentation)op.
- Installeer `nbconvert`. Volg alleen de installatie-instructies onder sectie __Nbconvert installeren__ op de pagina [installatie](https://nbconvert.readthedocs.io/en/latest/install.html) .

## <a name="remove-all-nonessential-code"></a>Alle niet-essentiële code verwijderen

Sommige code die is geschreven tijdens het experiment, is alleen bedoeld voor experimentele doel einden. Daarom is de eerste stap om experimentele code te converteren naar productie code, om deze niet-essentiële code te verwijderen. Als u niet-essentiële code verwijdert, wordt de code ook eenvoudiger te onderhouden. In deze sectie verwijdert u code uit het `experimentation/Diabetes Ridge Regression Training.ipynb` notitie blok. De instructies voor het afdrukken van `X` de `y` vorm van en en `features.describe` de aanroep van de cel zijn alleen bedoeld voor het verkennen van gegevens en kunnen worden verwijderd. Na het verwijderen van de niet `experimentation/Diabetes Ridge Regression Training.ipynb` -essentiële code moet de volgende code worden weer geven zonder prijs:

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

## <a name="refactor-code-into-functions"></a>Refactorion-code in functions

Ten tweede moet de Jupyter-code worden gefactord in-functies. Het herstructureren van code in functions maakt het gemakkelijker om de eenheid te testen en maakt het eenvoudiger om de code te onderhouden. In deze sectie doet u het volgende:

- De Diabetese-regressie-trainings laptop`experimentation/Diabetes Ridge Regression Training.ipynb`()
- De diabetes () van de groef-`experimentation/Diabetes Ridge Regression Scoring.ipynb`regressie ()

### <a name="refactor-diabetes-ridge-regression-training-notebook-into-functions"></a>Refactory, diabetes groef regressie-laptop in functions

Voer `experimentation/Diabetes Ridge Regression Training.ipynb`in de volgende stappen uit:

1. Maak een functie met `split_data` de naam om het gegevens frame te splitsen in test-en Train gegevens. De functie moet data frame `df` als para meter gebruiken en een woorden boek retour neren met de sleutels `train` en `test`.

    Verplaats de code onder de kop *gegevens splitsen in training en validatie sets* in de `split_data` functie en wijzig deze door het `data` object te retour neren.

1. Maak een functie met `train_model`de naam, die de `data` para `args` meters accepteert en een getraind model retourneert.

    Verplaats de code onder het *trainings model voor kopteksten op trainingen* in `train_model` de functie en wijzig deze om het `reg_model` object te retour neren. De `args` woorden lijst verwijderen. de waarden worden opgehaald uit `args` de para meter.

1. Maak een functie met `get_model_metrics`de naam, die `reg_model` para `data`meters en en evalueert het model en retourneert vervolgens een woorden lijst met metrische gegevens voor het getrainde model.

    Verplaats de code onder de kop *model valideren op validatieset* in de `get_model_metrics` functie en wijzig deze om het `metrics` object te retour neren.

De drie functies moeten er als volgt uitzien:

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

`experimentation/Diabetes Ridge Regression Training.ipynb`Voer de volgende stappen uit:

1. Maak een nieuwe functie met `main`de naam, waarvoor geen para meters worden gebruikt en er niets wordt geretourneerd.
1. Verplaats de code onder de kop ' gegevens laden ' naar de `main` functie.
1. Voeg aanroepen voor de zojuist geschreven functies toe aan de `main` functie:
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
1. Verplaats de code onder de kop ' model opslaan ' naar de `main` functie.

De `main` functie moet eruitzien als de volgende code:

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

In deze fase mag er geen code meer in het notitie blok staan dat niet in een functie voor komt, behalve de instructies import in de eerste cel.

Voeg een instructie toe die de `main` functie aanroept.

```python
main()
```

Na refactorie moet `experimentation/Diabetes Ridge Regression Training.ipynb` de volgende code worden weer geven zonder de prijs:

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

### <a name="refactor-diabetes-ridge-regression-scoring-notebook-into-functions"></a>Refactory diabetes groef, Score notebook van een regressie naar functions

Voer `experimentation/Diabetes Ridge Regression Scoring.ipynb`in de volgende stappen uit:

1. Maak een nieuwe functie met `init`de naam, waarvoor geen para meters worden gebruikt en niets kan worden geretourneerd.
1. Kopieer de code onder de kop ' Model laden ' naar de `init` functie.

De `init` functie moet eruitzien als de volgende code:

```python
def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

Als de `init` functie eenmaal is gemaakt, vervangt u de code onder de kop ' load model ' door één aanroep naar `init` de volgende:

```python
init()
```

Voer `experimentation/Diabetes Ridge Regression Scoring.ipynb`in de volgende stappen uit:

1. Maak een nieuwe functie met `run`de naam, `raw_data` die `request_headers` als para meters accepteert en een woorden lijst met resultaten retourneert als volgt:

    ```python
    {"result": result.tolist()}
    ```

1. Kopieer de code onder de koppen ' gegevens voorbereiden ' en ' Score gegevens ' in de `run` functie.

    De `run` functie moet eruitzien als de volgende code (Vergeet niet om de instructies te verwijderen die `raw_data` de `request_headers`variabelen instellen en, die later worden gebruikt `run` wanneer de functie wordt aangeroepen):

    ```python
    def run(raw_data, request_headers):
        data = json.loads(raw_data)["data"]
        data = numpy.array(data)
        result = model.predict(data)

        return {"result": result.tolist()}
    ```

Als de `run` functie is gemaakt, vervangt u alle code in de koppen ' gegevens voorbereiden ' en ' Score gegevens ' door de volgende code:

```python
raw_data = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(raw_data, request_header)
print("Test result: ", prediction)
```

Met `raw_data` de vorige code `request_header`worden variabelen ingesteld, wordt `run` de functie `raw_data` aangeroepen `request_header`met en en worden de voor spellingen afgedrukt.

Na refactorie moet `experimentation/Diabetes Ridge Regression Scoring.ipynb` de volgende code worden weer geven zonder de prijs:

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

## <a name="combine-related-functions-in-python-files"></a>Gerelateerde functies in Python-bestanden combi neren

Ten derde moeten verwante functies worden samengevoegd in Python-bestanden om het programma gemakkelijker te kunnen hergebruiken. In deze sectie maakt u python-bestanden voor de volgende notitie blokken:

- De Diabetese-regressie-trainings laptop`experimentation/Diabetes Ridge Regression Training.ipynb`()
- De diabetes () van de groef-`experimentation/Diabetes Ridge Regression Scoring.ipynb`regressie ()

### <a name="create-python-file-for-the-diabetes-ridge-regression-training-notebook"></a>Een python-bestand maken voor de Diabetese groef-Oefen notitieblok

Converteer uw notitie blok naar een uitvoerbaar script door de volgende instructie uit te voeren in een opdracht `nbconvert` prompt, waarbij het pakket `experimentation/Diabetes Ridge Regression Training.ipynb`en het pad van worden gebruikt:

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Training.ipynb" –output train
```

Wanneer het notitie blok is geconverteerd naar `train.py`, verwijdert u eventuele ongewenste opmerkingen. Vervang de aanroep aan `main()` aan het einde van het bestand met een voorwaardelijke aanroep als de volgende code:

```python
if __name__ == '__main__':
    main()
```

Het `train.py` bestand moet er ongeveer uitzien als de volgende code:

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

`train.py`kan nu worden aangeroepen vanaf een Terminal door uit te `python train.py`voeren.
De functies van `train.py` kunnen ook worden aangeroepen vanuit andere bestanden.

Het `train_aml.py` bestand dat in de `diabetes_regression/training` map in de MLOpsPython-opslag plaats is gevonden, `train.py` roept de functies aan die zijn gedefinieerd in in de context van een Azure machine learning-experiment. De functies kunnen ook worden aangeroepen in eenheids tests, verderop in deze hand leiding.

### <a name="create-python-file-for-the-diabetes-ridge-regression-scoring-notebook"></a>Een python-bestand maken voor de Diabetese-regressie

Uw notitie blok naar een uitvoerbaar script converteren door de volgende instructie uit te voeren in een opdracht `nbconvert` prompt waarin het pakket en `experimentation/Diabetes Ridge Regression Scoring.ipynb`het pad van worden gebruikt:

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Scoring.ipynb" –output score
```

Wanneer het notitie blok is geconverteerd naar `score.py`, verwijdert u eventuele ongewenste opmerkingen. Het `score.py` bestand moet er ongeveer uitzien als de volgende code:

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

De `model` variabele moet globaal zijn, zodat deze in het script zichtbaar is. Voeg de volgende instructie toe aan het begin van `init` de functie:

```python
global model
```

Nadat u de voor gaande instructie hebt `init` toegevoegd, moet de functie eruitzien als in de volgende code:

```python
def init():
    global model

    # load the model from file into a global object
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

## <a name="create-unit-tests-for-each-python-file"></a>Eenheids tests maken voor elk python-bestand

Vierde, maak eenheids tests voor uw python-functies. Eenheids tests beveiligen code tegen functionele regressies en maken het gemakkelijker om te onderhouden. In deze sectie maakt u eenheids tests voor de functies in `train.py`.

`train.py`bevat meerdere functies, maar er wordt slechts één eenheids test voor de `train_model` functie gemaakt met behulp van het Pytest-Framework in deze zelf studie. Pytest is niet het enige python-test raamwerk, maar het is een van de meest voorkomende. Ga naar [Pytest](https://pytest.org)voor meer informatie.

Een eenheids test bevat meestal drie hoofd acties:

- Object ordenen-benodigde objecten maken en instellen
- Reageren op een object
- Bevestiging wat er wordt verwacht

De eenheids test belt `train_model` met enkele hardcoded gegevens en argumenten en valideert dat `train_model` de verwachte getrainde model wordt gebruikt om een voor spelling te maken en de voor spelling te vergelijken met een verwachte waarde.

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

Nu u weet hoe u kunt converteren van een experiment naar productie code, raadpleegt u de volgende koppelingen voor meer informatie en volgende stappen:

+ [MLOpsPython](https://github.com/microsoft/MLOpsPython/blob/master/docs/custom_model.md): een CI/cd-pijp lijn bouwen om uw eigen model te trainen, evalueren en implementeren met behulp van Azure-pijp lijnen en Azure machine learning
+ [Uitvoeringen en metrische gegevens van Azure ML-experimenten bewaken](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments)
+ [Gegevens van ML-webservice-eind punten bewaken en verzamelen](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights)
