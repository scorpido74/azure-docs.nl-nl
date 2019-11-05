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
ms.date: 11/04/2019
ms.openlocfilehash: d9a879e92f78275f2366ccfc008068afbe208e5a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497356"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Automatisch een time-series-prognose model trainen
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u een regressie model voor Time-Series kunt trainen met behulp van geautomatiseerde machine learning in Azure Machine Learning. Het configureren van een prognose model is vergelijkbaar met het instellen van een standaard regressie model met behulp van geautomatiseerde machine learning, maar bepaalde configuratie opties en vooraf verwerkings stappen bestaan voor het werken met gegevens van de tijd reeks. In de volgende voor beelden ziet u hoe u:

* Gegevens voorbereiden voor time series-model lering
* Specifieke time series-para meters in een [`AutoMLConfig`](/python/api/azureml-train-automl/azureml.train.automl.automlconfig) -object configureren
* Voor spellingen uitvoeren met gegevens van de tijd reeks

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

U kunt automatische ML gebruiken om technieken en benaderingen te combi neren en een aanbevolen prognose voor de time-series van hoge kwaliteit te krijgen. Een geautomatiseerd experiment in de tijd reeks wordt behandeld als een multidimensionale regressie probleem. De waarden voor de laatste tijd reeksen zijn ' gedraaid ' om aanvullende dimensies te krijgen voor de regressor hierop samen met andere voor spellingen.

Deze methode, in tegens telling tot de methoden van de klassieke tijd reeks, heeft een voor deel van het gebruik van natuurlijk meerdere contextuele variabelen en hun relatie met elkaar tijdens de training. In Real-World-prognose toepassingen kunnen meerdere factoren van invloed zijn op een prognose. Wanneer u bijvoorbeeld de verkoop, de interacties van historische trends, de wissel koers en de prijs van de omzet verlangt, wordt het resultaat van de omzet gezamenlijk gebrand. Een ander voor deel is dat alle recente innovaties in regressie modellen onmiddellijk van toepassing zijn op prognoses.

U kunt [configureren](#config) hoe ver in de toekomst de prognose moet worden uitgebreid (de prognose horizon), evenals lags en meer. Automatische ML leert een enkelvoudig, maar vaak intern vertakkings model voor alle items in de gegevensset en de voor spellingen Horizons. Er zijn meer gegevens beschikbaar voor het schatten van model parameters en generalisatie naar onzichtbaar serie.

Functies die zijn geëxtraheerd uit de trainings gegevens spelen een kritieke rol. En, automatische MILLILITERs voert standaard stappen voor voor verwerking uit en genereert extra functies voor de tijdreeks om seizoensgebonden effecten vast te leggen en de voorspellende nauw keurigheid te maximaliseren.

## <a name="time-series-and-deep-learning-models"></a>Time-series en diepe leer modellen


Automatische ML biedt gebruikers zowel systeem eigen time-series als diepe leer modellen als onderdeel van het aanbevelings systeem. Deze informatie is onder andere:
+ Prophet
+ Automatische ARIMA
+ ForecastTCN

Met de grondige training van automatische ML kunt u Univariate-en multidimensionale tijdreeks gegevens voors pellen.

Uitgebreide leer modellen hebben drie ingebouwde capbailities:
1. Ze kunnen leren van wille keurige toewijzingen van invoer naar uitvoer
1. Ze ondersteunen meerdere invoer en uitvoer
1. Ze kunnen automatisch patronen extra heren in invoer gegevens die meer dan lange reeksen overschrijden

Door grotere gegevens te krijgen, kunnen diepe leer modellen, zoals gedurende ' ForecastTCN ', de scores van het resulterende model verbeteren. 

Systeem eigen time series-informatie wordt ook verschaft als onderdeel van automatische MILLILITERs. Prophet werkt het beste met een tijd reeks met krachtige seizoensgebonden effecten en verschillende seizoenen historische gegevens. Prophet is nauw keurig & snelle, robuuste uitbijters, ontbrekende gegevens en dramatische wijzigingen in uw tijd reeks. 

AutoRegressive Integrated zwevend gemiddelde (ARIMA) is een populaire statistische methode voor time series-prognoses. Deze methode voor het maken van prognoses wordt vaak gebruikt in scenario's voor de korte termijn prognose waarbij gegevens het bewijs van trends, zoals cycli, kunnen onvoorspelbaar en moeilijk te model leren. Automatische ARIMA transformeert uw gegevens naar stationaire gegevens om consistente, betrouw bare resultaten te ontvangen.

## <a name="prerequisites"></a>Vereisten

* Een Azure Machine Learning-werkruimte. Zie [een Azure machine learning-werk ruimte maken](how-to-manage-workspace.md)voor het maken van de werk ruimte.
* In dit artikel wordt ervan uitgegaan dat u bekend bent met het instellen van een geautomatiseerd machine learning experiment. Volg de [zelf studie](tutorial-auto-train-models.md) of Lees [hoe](how-to-configure-auto-train.md) u de ontwerp patronen voor automatische machine learning experimenten kunt zien.

## <a name="preparing-data"></a>Gegevens voorbereiden

Het belangrijkste verschil tussen een prognose taak type en een regressie taak type in automatische machine learning, bevat een functie in uw gegevens die een geldige tijd reeks voor stelt. Een reguliere tijd reeks heeft een duidelijk gedefinieerde en consistente frequentie en heeft op elk steekproef punt een waarde in een doorlopende periode. Bekijk de volgende moment opname van een bestands `sample.csv`.

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

Deze gegevensset is een eenvoudig voor beeld van dagelijkse verkoop gegevens voor een bedrijf met twee verschillende winkels, A en B. Daarnaast is er een functie voor `week_of_year` waarmee het model wekelijks kan worden gedetecteerd. Het veld `day_datetime` vertegenwoordigt een schone tijd reeks met een dagelijkse frequentie en het veld `sales_quantity` is de doel kolom voor het uitvoeren van voor spellingen. Lees de gegevens in een Panda data frame en gebruik vervolgens de functie `to_datetime` om ervoor te zorgen dat de tijd reeks een `datetime` type is.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

In dit geval worden de gegevens al oplopend gesorteerd op basis van het tijd veld `day_datetime`. Wanneer u echter een experiment instelt, moet u ervoor zorgen dat de gewenste tijd kolom in oplopende volg orde wordt gesorteerd om een geldige tijd reeks te maken. Ga ervan uit dat de gegevens 1.000 records bevatten en maak een deterministische splitsing in de gegevens voor het maken van trainings-en test gegevens sets. Zoek de naam van de label kolom en stel deze in op label. In dit voor beeld wordt het label `sales_quantity`. Scheid het veld label vervolgens van `test_data` om de `test_target`set te maken.

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
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

Het `AutoMLConfig`-object definieert de instellingen en gegevens die nodig zijn voor een geautomatiseerde machine learning taak. Net als bij een regressie probleem definieert u de standaard opleidings parameters, zoals het taak type, het aantal iteraties, de trainings gegevens en het aantal Kruis validaties. Voor prognose taken zijn er aanvullende para meters die moeten worden ingesteld die van invloed zijn op het experiment. In de volgende tabel worden de para meters en het gebruik ervan toegelicht.

| Param | Beschrijving | Vereist |
|-------|-------|-------|
|`time_column_name`|Wordt gebruikt om de kolom datetime op te geven in de invoer gegevens die worden gebruikt voor het bouwen van de tijd reeks en het uitstellen van de frequentie.|✓|
|`grain_column_names`|Naam (en) die afzonderlijke reeks groepen in de invoer gegevens definiëren. Als korrel niet is gedefinieerd, wordt ervan uitgegaan dat de gegevensset één keer wordt gebruikt.||
|`max_horizon`|Definieert de maximale gewenste prognose horizon in eenheden van de time-series. Eenheden zijn gebaseerd op het tijds interval van uw trainings gegevens, bijvoorbeeld maandelijks, wekelijks dat de Forecaster moet voors pellen.|✓|
|`target_lags`|Het aantal rijen dat de doel waarden moeten worden vertraagd op basis van de frequentie van de gegevens. Dit wordt weer gegeven als een lijst of één geheel getal. Er moet een vertraging worden gebruikt wanneer de relatie tussen de onafhankelijke variabelen en de afhankelijke variabele niet standaard overeenkomen of correleren. Wanneer u bijvoorbeeld de vraag voor een product wilt voors pellen, is de vraag in elke maand mogelijk afhankelijk van de prijs van specifieke producten 3 maanden vóór. In dit voor beeld wilt u mogelijk het doel (de vraag) met drie maanden uitsteld, zodat het model op de juiste relatie wordt getraind.||
|`target_rolling_window_size`|*n* historische Peri Oden die moeten worden gebruikt voor het genereren van prognose waarden, < = grootte van de Trainingsset. Als u dit weglaat, is *n* de volledige grootte van de Trainingsset. Geef deze para meter op als u alleen een bepaalde hoeveelheid geschiedenis wilt beschouwen bij het trainen van het model.||
|`enable_dnn`|DNNs voor het maken van prognoses.||

Raadpleeg de [referentie documentatie](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py) voor meer informatie.

Maak de instellingen voor de tijd reeks als een woordenlijst object. Stel de `time_column_name` in op het `day_datetime` veld in de gegevensset. Definieer de para meter `grain_column_names` om ervoor te zorgen dat **twee afzonderlijke time-series groepen** voor de gegevens worden gemaakt. een voor Store A en B. tot slot stelt u de `max_horizon` in op 50 om te voors pellen op de hele testset. Stel een prognose venster in op 10 Peri Oden met `target_rolling_window_size`en geef een enkele vertraging op voor de doel waarden voor twee Peri Oden vóór de para meter `target_lags`.

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

Als u de `grain_column_names` in het bovenstaande code fragment definieert, maakt AutoML twee afzonderlijke tijdreeks groepen, ook wel bekend als meerdere tijd reeksen. Als er geen korrel is gedefinieerd, wordt ervan uitgegaan dat de gegevensset een enkele time series is. Zie de [energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand)voor meer informatie over eenmalige time-series.

Maak nu een standaard `AutoMLConfig`-object, geef het `forecasting` taak type op en verzend het experiment. Wanneer het model is voltooid, haalt u de best mogelijke run-iteratie op.

```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig
import logging

automl_config = AutoMLConfig(task='forecasting',
                             primary_metric='normalized_root_mean_squared_error',
                             experiment_timeout_minutes=15,
                             enable_early_stopping=True,
                             training_data=train_data,
                             label_column_name=label,
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

### <a name="configure-a-dnn-enable-forecasting-experiment"></a>Een DNN-experiment voor het maken van prognoses configureren

> [!NOTE]
> DNN-ondersteuning voor prognoses in geautomatiseerde Machine Learning is een preview-versie.

Als u gebruik wilt maken van DNNs voor prognoses, moet u de para meter `enable_dnn` in de AutoMLConfig instellen op True. 

Voor het gebruik van DNNs kunt u het beste een AML Compute-cluster gebruiken met GPU-Sku's en ten minste twee knoop punten als het reken doel. Raadpleeg de [AML Compute-documentatie](https://docs.microsoft.com/en-us/azure/machine-learning/service/how-to-set-up-training-targets#amlcompute) voor meer informatie. Zie [grootten van GPU geoptimaliseerde virtuele machines](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/sizes-gpu) voor meer informatie over de VM-grootten die gpu's bevatten.

Om voldoende tijd te bieden aan het volt ooien van de DNN-training, wordt aangeraden de time-out voor het experiment in te stellen op ten minste een aantal uren.

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
predict_labels = fitted_model.predict(test_data)
actual_labels = test_labels.flatten()
```

U kunt ook de functie `forecast()` gebruiken in plaats van `predict()`, zodat de specificaties van voor spellingen kunnen worden gestart. In het volgende voor beeld vervangt u eerst alle waarden in `y_pred` door `NaN`. In dit geval wordt de oorsprong van de prognose aan het einde van de trainings gegevens weer gegeven, omdat deze normaal gesp roken zou zijn bij het gebruik van `predict()`. Als u echter alleen de tweede helft van `y_pred` met `NaN`hebt vervangen, zou de functie de numerieke waarden in de eerste helft ongewijzigd laten, maar de `NaN` waarden in de tweede helft worden geforecastd. De functie retourneert zowel de voorspelde waarden als de uitgelijnde functies.

U kunt ook de para meter `forecast_destination` gebruiken in de functie `forecast()` om waarden te ramen tot een opgegeven datum.

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_pipeline.forecast(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

Bereken RMSE (root mean Error) tussen de `actual_labels` werkelijke waarden en de geraamde waarden in `predict_labels`.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_lables, predict_labels))
rmse
```

Nu de algehele model nauwkeurigheid is vastgesteld, is de volgende stap het model te gebruiken voor het voors pellen van onbekende toekomstige waarden. Geef een gegevensset op in dezelfde indeling als de testset `test_data`, maar met toekomstige datum-en tijd waarden en de resulterende Voorspellings is de voorspelde waarde voor elke stap van de tijds reeks. Stel dat de laatste tijdreeks records in de gegevensset 12/31/2018 zijn. Voor een prognose van de vraag voor de volgende dag (of zoveel Peri Oden als u nodig hebt om een prognose te maken, < = `max_horizon`), maakt u één tijdreeks record voor elke Store voor 01/01/2019.

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

Herhaal de stappen die nodig zijn om deze toekomstige gegevens naar een data frame te laden en voer `best_run.predict(test_data)` uit om toekomstige waarden te voors pellen.

> [!NOTE]
> Waarden kunnen niet worden voor speld voor het aantal Peri Oden dat groter is dan de `max_horizon`. Het model moet opnieuw worden getraind met een grotere horizon om toekomstige waarden buiten de huidige horizon te voors pellen.

## <a name="next-steps"></a>Volgende stappen

* Volg de [zelf studie](tutorial-auto-train-models.md) voor meer informatie over het maken van experimenten met automatische machine learning.
* Bekijk de referentie documentatie [voor de Azure machine learning SDK voor python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) .
