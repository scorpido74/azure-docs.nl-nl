---
title: machine learning experiment code converteren naar productie code
titleSuffix: Azure Machine Learning
description: Meer informatie over het converteren van machine learning experimentele code naar productie code met behulp van de MLOpsPython-code sjabloon.
author: bjcmit
ms.author: brysmith
ms.service: machine-learning
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: 7f5e24261fd5d006004a51186e22f6bfe1b8ab32
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589178"
---
# <a name="tutorial-convert-ml-experimental-code-to-production-code"></a>Zelf studie: ML experimentele code converteren naar productie code

Een machine learning project vereist experimenten waarbij hypo Thesen worden getest met Agile-hulpprogram ma's zoals Jupyter Notebook met echte gegevens sets. Zodra het model klaar is voor productie, moet de model code in een opslag plaats voor productie code worden geplaatst. In sommige gevallen moet de model code worden geconverteerd naar python-scripts die in de productie code opslagplaats moeten worden geplaatst. In deze zelf studie wordt een aanbevolen benadering beschreven voor het exporteren van experimenten code naar python-scripts.  

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Niet-essentiële code wissen
> * Refactorion-Jupyter Notebook code in functions
> * Python-scripts maken voor verwante taken
> * Eenheids tests maken

## <a name="prerequisites"></a>Vereisten

- Genereer de [MLOpsPython-sjabloon](https://github.com/microsoft/MLOpsPython/generate) en gebruik de `experimentation/Diabetes Ridge Regression Training.ipynb`-en `experimentation/Diabetes Ridge Regression Scoring.ipynb`-notitie blokken. Deze notitie blokken worden gebruikt als voor beeld van het converteren van experimenten naar productie.
- Installeer nbconvert. Volg alleen de installatie-instructies onder sectie __Nbconvert installeren__ op de pagina [installatie](https://nbconvert.readthedocs.io/en/latest/install.html) .

## <a name="remove-all-nonessential-code"></a>Alle niet-essentiële code verwijderen

Sommige code die is geschreven tijdens het experiment, is alleen bedoeld voor experimentele doel einden. Daarom is de eerste stap om experimentele code te converteren naar productie code, om deze niet-essentiële code te verwijderen. Als u niet-essentiële code verwijdert, wordt de code ook eenvoudiger te onderhouden. In deze sectie verwijdert u code uit het `experimentation/Diabetes Ridge Regression Training.ipynb`-notitie blok. De instructies voor het afdrukken van de vorm van `X` en `y` en de cel waarmee `features.describe` worden aangeroepen, zijn alleen bedoeld voor het verkennen van gegevens en kunnen worden verwijderd. Na het verwijderen van de niet-essentiële code moet `experimentation/Diabetes Ridge Regression Training.ipynb` er als volgt uitzien:

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

## <a name="refactor-code-into-functions"></a>Refactorion-code in functions

Ten tweede moet de Jupyter-code worden gefactord in-functies. Het herstructureren van code in functions maakt het gemakkelijker om de eenheid te testen en maakt het eenvoudiger om de code te onderhouden. In deze sectie doet u het volgende:
- De diabetes (`experimentation/Diabetes Ridge Regression Training.ipynb`)
- De diabetes (`experimentation/Diabetes Ridge Regression Scoring.ipynb`) van de geplooide groef-regressie

### <a name="refactor-diabetes-ridge-regression-training-notebook-into-functions"></a>Refactory, diabetes groef regressie-laptop in functions
In `experimentation/Diabetes Ridge Regression Training.ipynb`voert u de volgende stappen uit:

1. Maak een functie met de naam `train_model`, waarbij de para meters `data` en `alpha` worden gebruikt en een model wordt geretourneerd. 
1. Kopieer de code onder de koppen "Train model on training set" en "model op Validatieset valideren" in de functie `train_model`.

De functie `train_model` moet eruitzien als de volgende code:

```python
def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg
```

Zodra de functie `train_model` is gemaakt, vervangt u de code onder de koppen ' Train model on training set ' en ' validate model on Validation set ' door de volgende instructie:

```python
reg = train_model(data, alpha)
```

De vorige instructie roept de `train_model`-functie aan waarmee de `data` en `alpha` para meters worden door gegeven en het model wordt geretourneerd.

In `experimentation/Diabetes Ridge Regression Training.ipynb`voert u de volgende stappen uit:

1. Maak een nieuwe functie met de naam `main`, waarvoor geen para meters worden gebruikt en er niets wordt geretourneerd.
1. Kopieer de code onder de koppen ' gegevens laden ', ' gegevens in trainings-en validatie sets splitsen ' en ' model opslaan ' in de functie `main`.
1. Kopieer de zojuist gemaakte oproep naar `train_model` naar de `main`-functie.

De functie `main` moet eruitzien als de volgende code:

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

Zodra de functie `main` is gemaakt, vervangt u alle code onder de koppen gegevens laden, gegevens splitsen in trainings-en validatie sets en model opslaan samen met de zojuist gemaakte oproep naar `train_model` met de volgende instructie:

```python
main()
```

Na het herstructureren moet `experimentation/Diabetes Ridge Regression Training.ipynb` eruitzien als de volgende code zonder de prijs:

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

### <a name="refactor-diabetes-ridge-regression-scoring-notebook-into-functions"></a>Refactory diabetes groef, Score notebook van een regressie naar functions
In `experimentation/Diabetes Ridge Regression Scoring.ipynb`voert u de volgende stappen uit:

1. Maak een nieuwe functie met de naam `init`, waarvoor geen para meters worden gebruikt en niets kan worden geretourneerd.
1. Kopieer de code onder de kop ' Model laden ' naar de functie `init`.

De functie `init` moet eruitzien als de volgende code:

```python
def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

Zodra de functie `init` is gemaakt, vervangt u de code onder de kop ' load model ' door één aanroep naar `init` als volgt:

```python
init()
```

In `experimentation/Diabetes Ridge Regression Scoring.ipynb`voert u de volgende stappen uit:

1. Maak een nieuwe functie met de naam `run`, die `raw_data` en `request_headers` als para meters gebruikt en een woorden lijst met resultaten als volgt retourneert:

    ```python
    {"result": result.tolist()}
    ```

1. Kopieer de code onder de koppen ' gegevens voorbereiden ' en ' Score gegevens ' in de functie `run`.

    De functie `run` moet eruitzien als de volgende code (Vergeet niet om de instructies te verwijderen waarmee de variabelen `raw_data` en `request_headers`worden ingesteld, die later worden gebruikt wanneer de functie `run` wordt aangeroepen):

    ```python
    def run(raw_data, request_headers):
        data = json.loads(raw_data)["data"]
        data = numpy.array(data)
        result = model.predict(data)

        return {"result": result.tolist()}
    ```

Zodra de functie `run` is gemaakt, vervangt u alle code in de koppen ' gegevens voorbereiden ' en ' Score gegevens ' door de volgende code:

```python
raw_data = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(raw_data, request_header)
print("Test result: ", prediction)
```
Met de vorige code worden variabelen `raw_data` en `request_header`ingesteld, wordt de functie `run` aangeroepen met `raw_data` en `request_header`en worden de voor spellingen afgedrukt.

Na het herstructureren moet `experimentation/Diabetes Ridge Regression Scoring.ipynb` eruitzien als de volgende code zonder de prijs:

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
- De diabetes (`experimentation/Diabetes Ridge Regression Training.ipynb`)
- De diabetes (`experimentation/Diabetes Ridge Regression Scoring.ipynb`) van de geplooide groef-regressie

### <a name="create-python-file-for-the-diabetes-ridge-regression-training-notebook"></a>Een python-bestand maken voor de Diabetese groef-Oefen notitieblok
Converteer uw notitie blok naar een uitvoerbaar script door de volgende instructie uit te voeren in een opdracht prompt, die gebruikmaakt van het nbconvert-pakket en het pad van `experimentation/Diabetes Ridge Regression Training.ipynb`:

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Training.ipynb" –output train
```

Nadat het notitie blok naar `train.py`is geconverteerd, verwijdert u alle opmerkingen. Uw `train.py`-bestand moet er ongeveer uitzien als de volgende code:

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

Het `train.py` bestand in de `diabetes_regression/training` Directory in de MLOpsPython-opslag plaats ondersteunt opdracht regel argumenten (te weten `build_id`, `model_name`en `alpha`). Ondersteuning voor opdracht regel argumenten kan worden toegevoegd aan uw `train.py`-bestand ter ondersteuning van dynamische model namen en `alpha` waarden, maar het is niet nodig om de code uit te voeren.

### <a name="create-python-file-for-the-diabetes-ridge-regression-scoring-notebook"></a>Een python-bestand maken voor de Diabetese-regressie
Zet uw notitie blok om naar een uitvoerbaar script door de volgende instructie uit te voeren in een opdracht prompt die gebruikmaakt van het nbconvert-pakket en het pad van `experimentation/Diabetes Ridge Regression Scoring.ipynb`:

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Scoring.ipynb" –output score
```

Nadat het notitie blok naar `score.py`is geconverteerd, verwijdert u alle opmerkingen. Uw `score.py`-bestand moet er ongeveer uitzien als de volgende code:

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

De functie `train_model` moet worden gewijzigd om een globaal variabelen model te instantiëren zodat het zichtbaar is in het hele script. Voeg de volgende instructie toe aan het begin van de functie `init`:

```python
global model
```

Nadat u de voor gaande-instructie hebt toegevoegd, moet de functie `init` eruitzien als de volgende code:

```python
def init():
    global model

    # load the model from file into a global object
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

## <a name="create-unit-tests-for-each-python-file"></a>Eenheids tests maken voor elk python-bestand
Ten vierde moeten er eenheids tests worden gemaakt voor elk python-bestand, waardoor code robuuster en eenvoudiger te onderhouden is. In deze sectie maakt u een eenheids test voor een van de functies in `train.py`.

`train.py` bevat twee functies: `train_model` en `main`. Voor elke functie is een eenheids test vereist, maar er wordt slechts één eenheids test voor de functie `train_model` gemaakt met behulp van het Pytest-Framework in deze zelf studie.  Pytest is niet het enige python-test raamwerk, maar het is een van de meest voorkomende. Ga naar [Pytest](https://pytest.org)voor meer informatie.

Een eenheids test bevat meestal drie hoofd acties:
- Object ordenen-benodigde objecten maken en instellen
- Reageren op een object
- Bevestiging wat er wordt verwacht

Een gemeen schappelijke voor waarde voor `train_model` is wanneer `data` en een `alpha` waarde worden door gegeven. Het verwachte resultaat is dat de functies `Ridge.train` en `Ridge.predict` moeten worden aangeroepen. Omdat machine learning trainings methoden vaak niet snel worden uitgevoerd, wordt de oproep naar `Ridge.train` gesimuleerd. Omdat de geretourneerde waarde van `Ridge.train` een gesimuleerd object is, trekken we ook `Ridge.predict`. De eenheids test voor het `train_model` testen van `data` en een `alpha` waarde met het verwachte resultaat van `Ridge.train` en `Ridge.predict` functies die worden aangeroepen met behulp van decoderen en het Pytest-Framework moet er als volgt uitzien:

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

## <a name="use-your-own-model-with-mlopspython-code-template"></a>Uw eigen model gebruiken met MLOpsPython-code sjabloon
Als u de stappen in deze hand leiding hebt volgen, hebt u een reeks scripts die betrekking hebben op de beschik bare Train/Score/test-scripts in de MLOpsPython-opslag plaats.  Op basis van de hierboven genoemde structuur worden de volgende stappen door lopen wat u nodig hebt om deze bestanden te gebruiken voor uw eigen machine learning project:  

1.  Volg de aan de slag-hand leiding
2.  De trainings code vervangen
3.  De Score code vervangen
4.  De evaluatie code bijwerken

### <a name="follow-the-getting-started-guide"></a>Volg de aan de slag-hand leiding
Als u de hand leiding aan de slag hebt, moet u de ondersteunende infra structuur en pijp lijnen ondersteunen om MLOpsPython uit te voeren.  We raden u aan de MLOpsPython-code te implementeren als-is voordat u uw eigen code invoert om ervoor te zorgen dat de structuur en pijp lijn goed werken.  Het is ook handig om vertrouwd te raken met de code structuur van de opslag plaats.

### <a name="replace-training-code"></a>Trainings code vervangen
Het vervangen van de code die wordt gebruikt om het model te trainen en het verwijderen of vervangen van bijbehorende eenheids tests is vereist om de oplossing te laten werken met uw eigen code.  Volg deze stappen specifiek:

1. Vervang `diabetes_regression\training\train.py`. Dit script traint uw model lokaal of op Azure ML compute.
1. Testen of vervangen van trainings eenheden gevonden in `tests/unit/code_test.py`

### <a name="replace-score-code"></a>Score code vervangen
Voor het model om realtime-Afleidings mogelijkheden te bieden, moet de Score code worden vervangen. De MLOpsPython-sjabloon gebruikt de Score code om het model te implementeren voor het uitvoeren van real-time scores op ACI-, AKS-of web-apps.  Als u score wilt blijven gebruiken, moet u `diabetes_regression/scoring/score.py`vervangen.

### <a name="update-evaluation-code"></a>Evaluatie code bijwerken
De MLOpsPython-sjabloon gebruikt het evaluate_model script om de prestaties van het nieuwe getrainde model en het huidige productie model te vergelijken op basis van de gemiddelde fout. Als de prestaties van het pas getrainde model beter zijn dan het huidige productie model, worden de pijp lijnen voortgezet. Anders worden de pijp lijnen gestopt. Als u de evaluatie wilt blijven gebruiken, vervangt u alle exemplaren van `mse` in `diabetes_regression/evaluate/evaluate_model.py` door de gewenste metrische gegevens. 

Als u de evaluatie wilt verwijderen, stelt u de DevOps pijplijn variabele `RUN_EVALUATION` in `.pipelines\diabetes_regression-variables` in `false`.

## <a name="next-steps"></a>Volgende stappen

Nu u weet hoe u kunt converteren van een experiment naar productie code, gebruikt u de volgende koppelingen voor meer informatie over het controleren van experimentele uitvoeringen en modellen die als webservices zijn geïmplementeerd:

> [!div class="nextstepaction"]
> Het [experiment van Azure ml-experimenten en de metrische](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments) gegevens
> [bewaken en verzamelen van data van ml-webservice-eind punten](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights)
