---
title: Machine learning-experimentcode converteren naar productiecode
titleSuffix: Azure Machine Learning
description: Meer informatie over het converteren van experimentele code voor machine learning naar productiecode met behulp van de MLOpsPython-codesjabloon.
author: bjcmit
ms.author: brysmith
ms.service: machine-learning
ms.topic: tutorial
ms.date: 03/13/2020
ms.openlocfilehash: f40c2b5f7134458b3f8cb492652bebf14388634c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79477133"
---
# <a name="tutorial-convert-ml-experimental-code-to-production-code"></a>Zelfstudie: ML-experimentele code converteren naar productiecode

Een machine learning-project vereist experimenten waarbij hypothesen worden getest met flexibele tools zoals Jupyter Notebook met behulp van echte datasets. Zodra het model klaar is voor productie, moet de modelcode in een opslagplaats voor productiecode worden geplaatst. In sommige gevallen moet de modelcode worden geconverteerd naar Python-scripts die in de opslagplaats van de productiecode moeten worden geplaatst. Deze zelfstudie behandelt een aanbevolen benadering voor het exporteren van experimenteercode naar Python-scripts.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Schone niet-essentiële code
> * Refactor Jupyter Notebook code in functies
> * Python-scripts maken voor gerelateerde taken
> * Eenheidstests maken

## <a name="prerequisites"></a>Vereisten

- Genereer de [SJABLOON MLOpsPython](https://github.com/microsoft/MLOpsPython/generate) en gebruik de `experimentation/Diabetes Ridge Regression Training.ipynb` en `experimentation/Diabetes Ridge Regression Scoring.ipynb` notebooks. Deze notitieblokken worden gebruikt als een voorbeeld van het omzetten van experimenten naar productie. U vindt deze [https://github.com/microsoft/MLOpsPython/tree/master/experimentation](https://github.com/microsoft/MLOpsPython/tree/master/experimentation)notebooks op .
- Installeer nbconvert. Volg alleen de installatie-instructies onder sectie __Het installeren van nbconvert__ op de [installatiepagina.](https://nbconvert.readthedocs.io/en/latest/install.html)

## <a name="remove-all-nonessential-code"></a>Alle niet-essentiële code verwijderen

Sommige code geschreven tijdens experimenten is alleen bedoeld voor verkennende doeleinden. Daarom is de eerste stap om experimentele code om te zetten in productiecode is om deze niet-essentiële code te verwijderen. Het verwijderen van niet-essentiële code maakt de code ook beter te onderhouden. In deze sectie verwijdert u code `experimentation/Diabetes Ridge Regression Training.ipynb` uit het notitieblok. De instructies afdrukken `X` van `y` de vorm `features.describe` van en en de cel aanroepen zijn alleen voor data-exploratie en kan worden verwijderd. Na het verwijderen `experimentation/Diabetes Ridge Regression Training.ipynb` van niet-essentiële code, moet eruit zien als de volgende code zonder afwaardering:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib

X, y = load_diabetes(return_X_y=True)

X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
data = {"train": {"X": X_train, "y": y_train},
        "test": {"X": X_test, "y": y_test}}

alpha = 0.5

reg = Ridge(alpha=alpha)
reg.fit(data["train"]["X"], data["train"]["y"])

preds = reg.predict(data["test"]["X"])
print("mse", mean_squared_error(preds, data["test"]["y"]))

model_name = "sklearn_regression_model.pkl"
joblib.dump(value=reg, filename=model_name)
```

## <a name="refactor-code-into-functions"></a>Refactorcode in functies

Ten tweede moet de Jupyter-code worden omgezet in functies. Refactoring code in functies maakt het testen van eenheden gemakkelijker en maakt de code meer onderhoudbaar. In deze sectie moet u refactor:

- De Diabetes Ridge Regression`experimentation/Diabetes Ridge Regression Training.ipynb`Training notebook( )
- De Diabetes Ridge Regression`experimentation/Diabetes Ridge Regression Scoring.ipynb`Scoring notebook( )

### <a name="refactor-diabetes-ridge-regression-training-notebook-into-functions"></a>Refactor Diabetes Ridge Regression Training notebook in functies

Voer `experimentation/Diabetes Ridge Regression Training.ipynb`in de volgende stappen uit:

1. Maak een `train_model`functie genaamd , `data` `alpha` die de parameters neemt en en retourneert een model.
1. Kopieer de code onder de koppen 'Treinmodel op trainingsset' en `train_model` 'Model valideren op validatieset' naar de functie.

De `train_model` functie moet er uitzien als de volgende code:

```python
def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg
```

Zodra `train_model` de functie is gemaakt, vervangt u de code onder de koppen 'Treinmodel op trainingsset' en 'Model valideren op validatieset' door de volgende instructie:

```python
reg = train_model(data, alpha)
```

De vorige instructie `train_model` roept `data` de `alpha` functie aan die de parameters doorgeeft en het model retourneert.

Voer `experimentation/Diabetes Ridge Regression Training.ipynb`in de volgende stappen uit:

1. Maak een nieuwe `main`functie genaamd , die geen parameters neemt en niets retourneert.
1. Kopieer de code onder de koppen 'Gegevens laden', 'Gegevens splitsen in trainings- en validatiesets' en 'Model opslaan' in de `main` functie.
1. Kopieer de nieuw `train_model` gemaakte `main` aanroep naar de functie.

De `main` functie moet er uitzien als de volgende code:

```python
def main():

    model_name = "sklearn_regression_model.pkl"
    alpha = 0.5

    X, y = load_diabetes(return_X_y=True)

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}

    reg = train_model(data, alpha)

    joblib.dump(value=reg, filename=model_name)
```

Zodra `main` de functie is gemaakt, vervangt u alle code onder de koppen 'Gegevens laden', 'Gegevens splitsen in `train_model` trainings- en validatiesets' en 'Model opslaan' samen met de nieuw gemaakte aanroep met de volgende instructie:

```python
main()
```

Na refactoring, `experimentation/Diabetes Ridge Regression Training.ipynb` moet eruit zien als de volgende code zonder de markdown:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib


def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg

def main():

    model_name = "sklearn_regression_model.pkl"
    alpha = 0.5

    X, y = load_diabetes(return_X_y=True)

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}

    reg = train_model(data, alpha)

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

Converteer uw notitieblok naar een uitvoerbaar script door de volgende instructie in een `experimentation/Diabetes Ridge Regression Training.ipynb`opdrachtprompt uit te voeren, waarin het pakket nbconvert en het pad van:

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Training.ipynb" –output train
```

Zodra het notitieblok is `train.py`geconverteerd naar, verwijder alle opmerkingen. Uw `train.py` bestand moet er uitzien als de volgende code:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib


def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg

def main():
    model_name = "sklearn_regression_model.pkl"
    alpha = 0.5

    X, y = load_diabetes(return_X_y=True)

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}

    reg = train_model(data, alpha)

    joblib.dump(value=reg, filename=model_name)

main()
```

Het `train.py` bestand in `diabetes_regression/training` de map in de MLOpsPython-repository `build_id` `model_name`ondersteunt `alpha`opdrachtregelargumenten (namelijk , en ). Ondersteuning voor opdrachtregelargumenten kan aan `train.py` uw bestand worden `alpha` toegevoegd om dynamische modelnamen en -waarden te ondersteunen, maar het is niet nodig dat de code succesvol wordt uitgevoerd.

### <a name="create-python-file-for-the-diabetes-ridge-regression-scoring-notebook"></a>Python-bestand maken voor het notitieblok 'Diabetes Ridge Regression Scoring'.

Verkapt uw notitieblok tot een uitvoerbaar script door de volgende instructie uit te voeren `experimentation/Diabetes Ridge Regression Scoring.ipynb`in een opdrachtprompt die het nbconvert-pakket en het pad van :

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Scoring.ipynb" –output score
```

Zodra het notitieblok is `score.py`geconverteerd naar, verwijder alle opmerkingen. Uw `score.py` bestand moet er uitzien als de volgende code:

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

De `train_model` functie moet worden aangepast om een globaal variabel model te instantiëren, zodat het zichtbaar is in het hele script. Voeg de volgende instructie toe `init` aan het begin van de functie:

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

Ten vierde moeten eenheidstests worden gemaakt voor elk Python-bestand, waardoor code robuuster en gemakkelijker te onderhouden is. In deze sectie maakt u een eenheidstest voor `train.py`een van de functies in .

`train.py`bevat twee `train_model` functies: en `main`. Elke functie heeft een eenheidstest nodig, maar we `train_model` maken slechts één eenheidstest voor de functie met behulp van het Pytest-framework in deze zelfstudie. Pytest is niet het enige Python-testframework, maar het is een van de meest gebruikte. Ga voor meer informatie naar [Pytest.](https://pytest.org)

Een eenheidstest bevat meestal drie hoofdacties:

- Object rangschikken - noodzakelijke objecten maken en instellen
- Handelen op een object
- Beweren wat er wordt verwacht

Een veel `train_model` voorkomende `data` voorwaarde `alpha` voor is wanneer en een waarde worden doorgegeven. Het verwachte resultaat `Ridge.train` is `Ridge.predict` dat de en functies moeten worden aangeroepen. Aangezien machine learning trainingsmethoden vaak niet snel draaien, zal de oproep om `Ridge.train` te worden bespot. Omdat de retourwaarde van `Ridge.train` een bespot object `Ridge.predict`is, zullen we ook bespotten. De eenheidstest `train_model` voor `data` het `alpha` testen van het `Ridge.train` passeren `Ridge.predict` van en een waarde met het verwachte resultaat van en functies worden genoemd met behulp van mocking en de Pytest kader moet eruit zien als de volgende code:

```python
import pytest
from code.training.train import train_model

class TestTrain:

    @staticmethod
    def test_train_model(mocker):
        # Arrange
        test_data = {"train": {"X": [[1, 2, 3]], "y": [0]},
                     "test": {"X": [[4, 5, 6]], "y": [0]}}
        test_alpha = 0.5
        mock_ridge_fit = mocker.patch('Ridge.fit')
        mock_ridge_predict = mocker.patch('Ridge.predict')

        # Act
        train_model(test_data, test_alpha)

        # Assert
        mock_ridge_fit.assert_called()
        mock_ridge_predict.assert_called()
```

## <a name="use-your-own-model-with-mlopspython-code-template"></a>Uw eigen model gebruiken met mlopsPython-codesjabloon

Als u de stappen in deze handleiding hebt gevolgd, hebt u een reeks scripts die correleren met de trein/score/testscripts die beschikbaar zijn in de MLOpsPython-opslagplaats.  Volgens de hierboven genoemde structuur, zullen de volgende stappen doorlopen wat nodig is om deze bestanden te gebruiken voor uw eigen machine learning-project:

1. Volg de mlopsPython [aan de slag](https://github.com/microsoft/MLOpsPython/blob/master/docs/getting_started.md) gids
2. Volg de instructies voor de [bootstrap van](https://github.com/microsoft/MLOpsPython/blob/master/bootstrap/README.md) MLOpsPython om het beginpunt van uw project te maken
3. De trainingscode vervangen
4. De scorecode vervangen
5. De evaluatiecode bijwerken

### <a name="follow-the-getting-started-guide"></a>Volg de handleiding aan de slag
Het volgen van de handleiding [Aan de slag](https://github.com/microsoft/MLOpsPython/blob/master/docs/getting_started.md) is noodzakelijk om de ondersteunende infrastructuur en pijplijnen te hebben om MLOpsPython uit te voeren.

### <a name="follow-the-bootstrap-instructions"></a>Volg de bootstrap instructies

De [Bootstrap van MLOpsPython repository](https://github.com/microsoft/MLOpsPython/blob/master/bootstrap/README.md) gids helpt je om snel de repository voor te bereiden op je project.

**Let op:** Aangezien het script bootstrap de naam van de diabetes_regression map wijzigt naar de `[project name]` projectnaam van uw keuze, verwijzen we naar uw project als wanneer er paden bij betrokken zijn.

### <a name="replace-training-code"></a>Trainingscode vervangen

Het vervangen van de code die wordt gebruikt om het model te trainen en het verwijderen of vervangen van overeenkomstige unittests is vereist om de oplossing te laten functioneren met uw eigen code. Volg de volgende stappen specifiek:

1. Vervang `[project name]/training/train.py`. Dit script traint uw model lokaal of op de Azure ML compute.
1. Testvan trainingseenheden verwijderen of vervangen`[project name]/training/test_train.py`

### <a name="replace-score-code"></a>Scorecode vervangen

Om het model real-time gevolgtrekkingsmogelijkheden te bieden, moet de scorecode worden vervangen. De MLOpsPython-sjabloon gebruikt de scorecode om het model te implementeren om realtime te scoren op ACI-, AKS- of Web-apps. Als je wilt blijven `[project name]/scoring/score.py`scoren, vervang dan .

### <a name="update-evaluation-code"></a>Evaluatiecode bijwerken

De MLOpsPython-sjabloon gebruikt het evaluate_model script om de prestaties van het nieuw getrainde model en het huidige productiemodel te vergelijken op basis van Gemiddelde kwadraatfout. Als de prestaties van het nieuw getrainde model beter zijn dan het huidige productiemodel, gaan de pijplijnen door. Anders worden de pijplijnen geannuleerd. Als u de evaluatie `mse` wilt `[project name]/evaluate/evaluate_model.py` behouden, vervangt u alle instanties van in door de gewenste statistiek.

Als u de evaluatie wilt verwijderen, stelt `RUN_EVALUATION` `.pipelines/[project name]-variables-template.yml` u `false`de variabele DevOps-pijplijn in op .

## <a name="next-steps"></a>Volgende stappen

Nu u begrijpt hoe u converteren van een experiment naar productiecode, gebruikt u de volgende koppelingen om te leren hoe u experimentruns en modellen controleren die als webservices worden geïmplementeerd:

> [!div class="nextstepaction"]
> [Azure ML-experiment uitvoeren en statistieken](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments)
> [bewaken controleren en verzamelen van gegevens van ML-eindpunten voor webservice](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights)
