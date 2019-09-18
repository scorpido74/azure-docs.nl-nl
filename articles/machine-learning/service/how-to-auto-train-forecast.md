---
title: Automatisch een time-series-prognose model trainen
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van Azure Machine Learning voor het trainen van een regressie model voor een time-series met geautomatiseerde machine learning.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: conceptual
ms.date: 06/20/2019
ms.openlocfilehash: c49d8000888d4094ea1df47920c1927747927f5c
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71035046"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Automatisch een time-series-prognose model trainen

In dit artikel leert u hoe u een regressie model voor Time-Series kunt trainen met behulp van geautomatiseerde machine learning in Azure Machine Learning. Het configureren van een prognose model is vergelijkbaar met het instellen van een standaard regressie model met behulp van geautomatiseerde machine learning, maar bepaalde configuratie opties en vooraf verwerkings stappen bestaan voor het werken met gegevens van de tijd reeks. In de volgende voor beelden ziet u hoe u:

* Gegevens voorbereiden voor time series-model lering
* Specifieke time series-para meters in [`AutoMLConfig`](/python/api/azureml-train-automl/azureml.train.automl.automlconfig) een object configureren
* Voor spellingen uitvoeren met gegevens van de tijd reeks

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

U kunt automatische ML gebruiken om technieken en benaderingen te combi neren en een aanbevolen prognose voor de time-series van hoge kwaliteit te krijgen. Een geautomatiseerd experiment in de tijd reeks wordt behandeld als een multidimensionale regressie probleem. De waarden voor de laatste tijd reeksen zijn ' gedraaid ' om aanvullende dimensies te krijgen voor de regressor hierop samen met andere voor spellingen.

Deze methode, in tegens telling tot de methoden van de klassieke tijd reeks, heeft een voor deel van het gebruik van natuurlijk meerdere contextuele variabelen en hun relatie met elkaar tijdens de training. In Real-World-prognose toepassingen kunnen meerdere factoren van invloed zijn op een prognose. Wanneer u bijvoorbeeld de verkoop, de interacties van historische trends, de wissel koers en de prijs van de omzet verlangt, wordt het resultaat van de omzet gezamenlijk gebrand. Een ander voor deel is dat alle recente innovaties in regressie modellen onmiddellijk van toepassing zijn op prognoses.

U kunt [configureren](#config) hoe ver in de toekomst de prognose moet worden uitgebreid (de prognose horizon), evenals lags en meer. Automatische ML leert een enkelvoudig, maar vaak intern vertakkings model voor alle items in de gegevensset en de voor spellingen Horizons. Er zijn meer gegevens beschikbaar voor het schatten van model parameters en generalisatie naar onzichtbaar serie.

Functies die zijn geëxtraheerd uit de trainings gegevens spelen een kritieke rol. En, automatische MILLILITERs voert standaard stappen voor voor verwerking uit en genereert extra functies voor de tijdreeks om seizoensgebonden effecten vast te leggen en de voorspellende nauw keurigheid te maximaliseren.

## <a name="prerequisites"></a>Vereisten

* Een Azure Machine Learning-werkruimte. Zie [een Azure machine learning-werk ruimte maken](how-to-manage-workspace.md)voor het maken van de werk ruimte.
* In dit artikel wordt ervan uitgegaan dat u bekend bent met het instellen van een geautomatiseerd machine learning experiment. Volg de [zelf studie](tutorial-auto-train-models.md) of Lees [hoe](how-to-configure-auto-train.md) u de ontwerp patronen voor automatische machine learning experimenten kunt zien.

## <a name="preparing-data"></a>Gegevens voorbereiden

Het belangrijkste verschil tussen een prognose taak type en een regressie taak type in automatische machine learning, bevat een functie in uw gegevens die een geldige tijd reeks voor stelt. Een reguliere tijd reeks heeft een duidelijk gedefinieerde en consistente frequentie en heeft op elk steekproef punt een waarde in een doorlopende periode. Bekijk de volgende moment opname van een `sample.csv`bestand.

    day_datetime,store,sales_quantity,week_of_year
    9/3/2018,A,2000,36
    9/3/2018,B,600,36
    9/4/2018,A,2300,36
    9/4/2018,B,550,36
    9/5/2018,A,2100,36
    9/5/2018,B,650,36
    9/6/2018,A,2400,36
    9/6/2018,B,700,36
    9/7/2018,A,2450,36
    9/7/2018,B,650,36

Deze gegevensset is een eenvoudig voor beeld van dagelijkse verkoop gegevens voor een bedrijf met twee verschillende winkels, a en B. Daarnaast is er een functie `week_of_year` waarmee het model wekelijks kan worden gedetecteerd. Het veld `day_datetime` vertegenwoordigt een schone tijd reeks met een dagelijkse frequentie en het veld `sales_quantity` is de doel kolom voor het uitvoeren van voor spellingen. Lees de gegevens in een Panda data frame en gebruik vervolgens de `to_datetime` functie om ervoor te zorgen dat de tijd reeks een `datetime` type is.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

In dit geval worden de gegevens al oplopend gesorteerd op basis van `day_datetime`het veld tijd. Wanneer u echter een experiment instelt, moet u ervoor zorgen dat de gewenste tijd kolom in oplopende volg orde wordt gesorteerd om een geldige tijd reeks te maken. Ga ervan uit dat de gegevens 1.000 records bevatten en maak een deterministische splitsing in de gegevens voor het maken van trainings-en test gegevens sets. Scheid vervolgens het doel veld `sales_quantity` om de voorspellings-en test sets te maken.

```python
X_train = data.iloc[:950]
X_test = data.iloc[-50:]

y_train = X_train.pop("sales_quantity").values
y_test = X_test.pop("sales_quantity").values
```

> [!NOTE]
> Wanneer u een model wilt trainen voor het voors pellen van toekomstige waarden, kunt u ervoor zorgen dat alle functies die in de training worden gebruikt, kunnen worden gebruikt bij het uitvoeren van voor spellingen voor uw beoogde horizon. Wanneer u bijvoorbeeld een vraag prognose maakt, met inbegrip van een functie voor de huidige aandelen koers, kan de nauw keurigheid van de training enorm toenemen. Als u echter van plan bent om te forecasten met een lange horizon, is het wellicht niet mogelijk om toekomstige voorraad waarden te voors pellen die overeenkomen met toekomstige periode punten en de nauw keurigheid van het model kan lijden.

<a name="config"></a>
## <a name="configure-and-run-experiment"></a>Experiment configureren en uitvoeren

Voor prognose taken maakt automatische machine learning gebruik van vooraf verwerkings-en schattings stappen die specifiek zijn voor tijdreeks gegevens. De volgende stappen voorafgaand aan verwerking worden uitgevoerd:

* Detectie frequentie van tijds reeksen detecteren (bijvoorbeeld elk uur, dagelijks, wekelijks) en nieuwe records maken voor ontbrekende tijd punten om de reeks continu te maken.
* Ontbrekende waarden in het doel (via voorwaartse opvulling) en functie kolommen toegerekend (met behulp van mediaan kolom waarden)
* Op korrels gebaseerde functies maken om vaste effecten in verschillende reeksen in te scha kelen
* Op tijd gebaseerde functies maken voor het leren van seizoensgebonden patronen
* Categorische variabelen coderen naar numerieke aantallen

Het `AutoMLConfig` object definieert de instellingen en gegevens die nodig zijn voor een geautomatiseerde machine learning taak. Net als bij een regressie probleem definieert u de standaard opleidings parameters, zoals het taak type, het aantal iteraties, de trainings gegevens en het aantal Kruis validaties. Voor prognose taken zijn er aanvullende para meters die moeten worden ingesteld die van invloed zijn op het experiment. In de volgende tabel worden de para meters en het gebruik ervan toegelicht.

| Param | Description | Vereist |
|-------|-------|-------|
|`time_column_name`|Wordt gebruikt om de kolom datetime op te geven in de invoer gegevens die worden gebruikt voor het bouwen van de tijd reeks en het uitstellen van de frequentie.|✓|
|`grain_column_names`|Naam (en) die afzonderlijke reeks groepen in de invoer gegevens definiëren. Als korrel niet is gedefinieerd, wordt ervan uitgegaan dat de gegevensset één keer wordt gebruikt.||
|`max_horizon`|Definieert de maximale gewenste prognose horizon in eenheden van de time-series. Eenheden zijn gebaseerd op het tijds interval van uw trainings gegevens, bijvoorbeeld maandelijks, wekelijks dat de Forecaster moet voors pellen.|✓|
|`target_lags`|*n* Peri Oden voor het door sturen van doel waarden voordat de training wordt gemodelleerd.||
|`target_rolling_window_size`|*n* historische Peri Oden die moeten worden gebruikt voor het genereren van prognose waarden, < = grootte van de Trainingsset. Als u dit weglaat, is *n* de volledige grootte van de Trainingsset.||

Maak de instellingen voor de tijd reeks als een woordenlijst object. Stel de `time_column_name` in op `day_datetime` het veld in de gegevensset. Definieer de `grain_column_names` para meter om ervoor te zorgen dat er **twee afzonderlijke tijdreeks groepen** worden gemaakt voor de gegevens. een voor Store A en B. Tenslotte `max_horizon` stelt u de 50 in op om te voors pellen op de hele testset. Stel een venster voor een prognose in op `target_rolling_window_size`10 Peri Oden met en vertraging van de doel waarden 2 `target_lags` Peri Oden met de para meter.

```python
time_series_settings = {
    "time_column_name": "day_datetime",
    "grain_column_names": ["store"],
    "max_horizon": 50,
    "target_lags": 2,
    "target_rolling_window_size": 10,
    "preprocess": True,
}
```

> [!NOTE]
> Automatische machine learning vooraf verwerkte stappen (functie normalisatie, het verwerken van ontbrekende gegevens, het converteren van tekst naar numerieke waarde, enzovoort) worden onderdeel van het onderliggende model. Wanneer u het model gebruikt voor voor spellingen, worden dezelfde vooraf verwerkings stappen die tijdens de training worden toegepast, automatisch toegepast op uw invoer gegevens.

Maak nu een standaard `AutoMLConfig` object, geef het `forecasting` taak type op en verzend het experiment. Wanneer het model is voltooid, haalt u de best mogelijke run-iteratie op.

```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig
import logging

automl_config = AutoMLConfig(task='forecasting',
                             primary_metric='normalized_root_mean_squared_error',
                             iterations=10,
                             X=X_train,
                             y=y_train,
                             n_cross_validations=5,
                             enable_ensembling=False,
                             verbosity=logging.INFO,
                             **time_series_settings)

ws = Workspace.from_config()
experiment = Experiment(ws, "forecasting_example")
local_run = experiment.submit(automl_config, show_output=True)
best_run, fitted_model = local_run.get_output()
```

Raadpleeg de [Energy demand notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb) voor gedetailleerde code voorbeelden van de geavanceerde prognose configuratie, waaronder:

* Kerst detectie en parametrisatie
* Cross-Origin-Kruis validatie
* Configureer bare lags
* statistische functies van het Rolling venster

### <a name="view-feature-engineering-summary"></a>Samen vatting van feature engineering weer geven

Voor de taak typen van de tijds reeks in automatische machine learning, kunt u de details van het onderdeel technisch proces bekijken. De volgende code toont elke onbewerkte functie samen met de volgende kenmerken:

* Onbewerkte functie naam
* Aantal functies van de functie die zijn gevormd door deze onbewerkte functionaliteit
* Type gedetecteerd
* Of de functie is verwijderd
* Lijst met functie transformaties voor de onbewerkte functie

```python
fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
```

## <a name="forecasting-with-best-model"></a>Prognose met het beste model

Gebruik de beste model iteratie om waarden voor de test gegevensset te prognoseen.

```python
y_predict = fitted_model.predict(X_test)
y_actual = y_test.flatten()
```

U kunt ook de functie gebruiken in `forecast()` plaats van `predict()`, waarmee de specificaties van wanneer voor spellingen moeten worden gestart. In het volgende voor beeld vervangt u eerst alle waarden in `y_pred` met `NaN`. In dit geval wordt de oorsprong van de prognose aan het einde van de trainings gegevens weer gegeven, omdat deze normaal `predict()`gesp roken zou zijn bij het gebruik van. Als u echter alleen de tweede helft van `y_pred` met `NaN`hebt vervangen, zou de functie de numerieke waarden in de eerste helft ongewijzigd laten, maar de `NaN` waarden in de tweede helft worden geforecastd. De functie retourneert zowel de voorspelde waarden als de uitgelijnde functies.

U kunt ook de `forecast_destination` para meter in de `forecast()` functie gebruiken om waarden te ramen tot een opgegeven datum.

```python
y_query = y_test.copy().astype(np.float)
y_query.fill(np.nan)
y_fcst, X_trans = fitted_pipeline.forecast(
    X_test, y_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

Bereken RMSE (root mean Error) tussen de `y_test` werkelijke waarden en de geraamde waarden in. `y_pred`

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

Nu de algehele model nauwkeurigheid is vastgesteld, is de volgende stap het model te gebruiken voor het voors pellen van onbekende toekomstige waarden. Geef een gegevensset op in dezelfde indeling als de testset `X_test` , maar met toekomstige datetimes en de resulterende Voorspellings is de voorspelde waarden voor elke stap van de tijds reeks. Stel dat de laatste tijdreeks records in de gegevensset 12/31/2018 zijn. Voor een prognose van de vraag voor de volgende dag (of zoveel Peri Oden als u nodig hebt om `max_horizon`een prognose te maken, < =), maakt u een eenmalige reeks record voor elke Store voor 01/01/2019.

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

Herhaal de stappen die nodig zijn om deze toekomstige gegevens naar een data frame te laden `best_run.predict(X_test)` en voer vervolgens uit om toekomstige waarden te voors pellen.

> [!NOTE]
> Waarden kunnen niet worden voor speld voor het aantal Peri Oden dat `max_horizon`groter is dan de. Het model moet opnieuw worden getraind met een grotere horizon om toekomstige waarden buiten de huidige horizon te voors pellen.

## <a name="next-steps"></a>Volgende stappen

* Volg de [zelf studie](tutorial-auto-train-models.md) voor meer informatie over het maken van experimenten met automatische machine learning.
* Bekijk de referentie documentatie [voor de Azure machine learning SDK voor python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) .
