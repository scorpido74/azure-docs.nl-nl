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
ms.topic: how-to
ms.date: 03/09/2020
ms.openlocfilehash: 72b0a3074bfdfb6b6038f6c63eb01a7b33d45ea6
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.contentlocale: nl-NL
ms.lasthandoff: 07/05/2020
ms.locfileid: "85959123"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Automatisch een time-series-prognose model trainen
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel vindt u informatie over het configureren en trainen van een regressie model voor het maken van een time-reeks met geautomatiseerde machine learning in de [Azure machine learning PYTHON SDK](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py). 

Voor een lage code kunt u de [zelf studie: prognose vraag met geautomatiseerde machine learning](tutorial-automated-ml-forecast.md) voor een time-series-prognose voor beeld met behulp van geautomatiseerde machine learning in [Azure machine learning Studio](https://ml.azure.com/).

Het configureren van een prognose model is vergelijkbaar met het instellen van een standaard regressie model met behulp van geautomatiseerde machine learning, maar bepaalde configuratie opties en vooraf verwerkings stappen bestaan voor het werken met gegevens van de tijd reeks. 

U kunt bijvoorbeeld [configureren](#config) hoe ver in de toekomst de prognose moet worden uitgebreid (de prognose horizon), evenals lags en meer. Automatische ML leert een enkelvoudig, maar vaak intern vertakkings model voor alle items in de gegevensset en de voor spellingen Horizons. Er zijn meer gegevens beschikbaar voor het schatten van model parameters en generalisatie naar onzichtbaar serie.

In de volgende voor beelden ziet u hoe u:

* Gegevens voorbereiden voor time series-model lering
* Specifieke time series-para meters in een [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) object configureren
* Voor spellingen uitvoeren met gegevens van de tijd reeks

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

In tegens telling tot de methoden van de klassieke tijd reeks, worden in automatische MILLILITERs waarden van de reeks ' gedraaid ' om extra dimensies voor de regressor hierop samen met andere voor spellingen. Deze aanpak omvat meerdere contextuele variabelen en hun relatie met elkaar tijdens de training. Omdat meerdere factoren van invloed kunnen zijn op een prognose, wordt deze methode goed afgestemd op de scenario's met de praktijk. Wanneer u bijvoorbeeld de verkoop, de interacties van historische trends, de wissel koers en de prijs van de omzet verlangt, wordt het resultaat van de omzet gezamenlijk gebrand. 

Functies die zijn geëxtraheerd uit de trainings gegevens spelen een kritieke rol. En, geautomatiseerd ML voert standaard stappen voor voor verwerking uit en genereert extra functies voor de tijdreeks om seizoensgebonden effecten vast te leggen en de voorspellende nauw keurigheid te maximaliseren

## <a name="time-series-and-deep-learning-models"></a>Time-series en diepe leer modellen

Met de grondige training van automatische ML kunt u Univariate-en multidimensionale tijdreeks gegevens voors pellen.

Uitgebreide leer modellen hebben drie ingebouwde mogelijkheden:
1. Ze kunnen leren van wille keurige toewijzingen van invoer naar uitvoer
1. Ze ondersteunen meerdere invoer en uitvoer
1. Ze kunnen automatisch patronen extra heren in invoer gegevens die meer dan lange reeksen overschrijden

Door grotere gegevens te krijgen, kunnen diepe leer modellen, zoals de ForecastTCN van micro soft, de scores van het resulterende model verbeteren. Meer informatie over hoe u [uw experiment kunt configureren voor diep gaande lessen](#configure-a-dnn-enable-forecasting-experiment).

Automatische ML biedt gebruikers zowel systeem eigen time-series als diepe leer modellen als onderdeel van het aanbevelings systeem. 

Modellen| Description | Voordelen
----|----|---
Prophet (preview-versie)|Prophet werkt het beste met een tijd reeks met krachtige seizoensgebonden effecten en verschillende seizoenen historische gegevens. Als u gebruik wilt maken van dit model, installeert u het lokaal met `pip install fbprophet` . | Nauw keurige & snelle, robuuste uitbijters, ontbrekende gegevens en dramatische wijzigingen in uw tijd reeks.
Automatische ARIMA (preview-versie)|AutoRegressive Integrated zwevend gemiddelde (ARIMA) werkt het beste wanneer de gegevens stationair zijn. Dit betekent dat de statistische eigenschappen, zoals het gemiddelde en de variantie, constant zijn in de hele set. Als u bijvoorbeeld een munten spiegelt, is de kans dat u koppen krijgt, 50%, ongeacht of u vandaag, morgen of volgend jaar spiegelt.| Ideaal voor univariate-Series, aangezien de vorige waarden worden gebruikt om de toekomstige waarden te voors pellen.
ForecastTCN (preview-versie)| ForecastTCN is een Neural-netwerk model dat is ontworpen om de meest veeleisende prognose taken uit te voeren, waarbij niet-lineaire lokale en wereld wijde trends in uw gegevens worden vastgelegd, evenals relaties tussen tijd reeksen.|Kan gebruikmaken van complexe trends in uw gegevens en kan eenvoudig worden geschaald naar het grootste aantal gegevens sets.

## <a name="prerequisites"></a>Vereisten

* Een Azure Machine Learning-werkruimte. Zie [een Azure machine learning-werk ruimte maken](how-to-manage-workspace.md)voor het maken van de werk ruimte.
* In dit artikel wordt ervan uitgegaan dat u bekend bent met het instellen van een geautomatiseerd machine learning experiment. Volg de [zelf studie](tutorial-auto-train-models.md) of Lees [hoe](how-to-configure-auto-train.md) u de ontwerp patronen voor automatische machine learning experimenten kunt zien.

## <a name="preparing-data"></a> Gegevens voorbereiden

Het belangrijkste verschil tussen een prognose taak type en een regressie taak type in automatische machine learning, bevat een functie in uw gegevens die een geldige tijd reeks voor stelt. Een reguliere tijd reeks heeft een duidelijk gedefinieerde en consistente frequentie en heeft op elk steekproef punt een waarde in een doorlopende periode. Bekijk de volgende moment opname van een bestand `sample.csv` .

```output
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
```

Deze gegevensset is een eenvoudig voor beeld van dagelijkse verkoop gegevens voor een bedrijf met twee verschillende winkels, A en B. Daarnaast is er een functie waarmee `week_of_year` het model wekelijks kan worden gedetecteerd. Het veld `day_datetime` vertegenwoordigt een schone tijd reeks met een dagelijkse frequentie en het veld `sales_quantity` is de doel kolom voor het uitvoeren van voor spellingen. Lees de gegevens in een Panda data frame en gebruik vervolgens de `to_datetime` functie om ervoor te zorgen dat de tijd reeks een `datetime` type is.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

In dit geval worden de gegevens al oplopend gesorteerd op basis van het veld tijd `day_datetime` . Wanneer u echter een experiment instelt, moet u ervoor zorgen dat de gewenste tijd kolom in oplopende volg orde wordt gesorteerd om een geldige tijd reeks te maken. Ga ervan uit dat de gegevens 1.000 records bevatten en maak een deterministische splitsing in de gegevens voor het maken van trainings-en test gegevens sets. Zoek de naam van de label kolom en stel deze in op label. In dit voor beeld is het label `sales_quantity` . Scheid het veld label vervolgens van `test_data` om de set te vormen `test_target` .

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
```

> [!NOTE]
> Wanneer u een model wilt trainen voor het voors pellen van toekomstige waarden, kunt u ervoor zorgen dat alle functies die in de training worden gebruikt, kunnen worden gebruikt bij het uitvoeren van voor spellingen voor uw beoogde horizon. Wanneer u bijvoorbeeld een vraag prognose maakt, met inbegrip van een functie voor de huidige aandelen koers, kan de nauw keurigheid van de training enorm toenemen. Als u echter van plan bent om te forecasten met een lange horizon, is het wellicht niet mogelijk om toekomstige voorraad waarden te voors pellen die overeenkomen met toekomstige periode punten en de nauw keurigheid van het model kan lijden.

<a name="config"></a>

## <a name="train-and-validation-data"></a>Train-en validatie gegevens
U kunt afzonderlijke treinen en validatie sets rechtstreeks in de `AutoMLConfig` constructor opgeven.

### <a name="rolling-origin-cross-validation"></a>Kruis validatie tussen komst
Voor time series-Voorspellings-cross-validatie (ROCV) wordt gebruikt om tijd reeksen op een tijdelijke consistente manier te splitsen. ROCV verdeelt de reeks in trainings-en validatie gegevens met behulp van een tijd punt op oorsprong. Als u de oorspronkelijke tijd verschuift, worden de vouwen voor kruis validatie gegenereerd.  

![alternatieve tekst](./media/how-to-auto-train-forecast/ROCV.svg)

Deze strategie behoudt de gegevens integriteit van de tijd reeks en elimineert het risico op lekkage van gegevens. ROCV wordt automatisch gebruikt voor het voors pellen van taken door de trainings-en validatie gegevens samen te voegen en het aantal Kruis validatie vouwen in te stellen met `n_cross_validations` . Meer informatie over hoe automatische ML Kruis validatie toepast om [verouderde modellen te voor komen](concept-manage-ml-pitfalls.md#prevent-over-fitting).

```python
automl_config = AutoMLConfig(task='forecasting',
                             n_cross_validations=3,
                             ...
                             **time_series_settings)
```
Meer informatie over de [AutoMLConfig](#configure-and-run-experiment).

## <a name="configure-and-run-experiment"></a>Experiment configureren en uitvoeren

Voor prognose taken maakt automatische machine learning gebruik van vooraf verwerkings-en schattings stappen die specifiek zijn voor tijdreeks gegevens. De volgende stappen voorafgaand aan verwerking worden uitgevoerd:

* Detectie frequentie van tijds reeksen detecteren (bijvoorbeeld elk uur, dagelijks, wekelijks) en nieuwe records maken voor ontbrekende tijd punten om de reeks continu te maken.
* Ontbrekende waarden in het doel (via voorwaartse opvulling) en functie kolommen toegerekend (met behulp van mediaan kolom waarden)
* Op korrels gebaseerde functies maken om vaste effecten in verschillende reeksen in te scha kelen
* Op tijd gebaseerde functies maken voor het leren van seizoensgebonden patronen
* Categorische variabelen coderen naar numerieke aantallen

Het [`AutoMLConfig`](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py) object definieert de instellingen en gegevens die nodig zijn voor een geautomatiseerde machine learning taak. Net als bij een regressie probleem definieert u de standaard opleidings parameters, zoals het taak type, het aantal iteraties, de trainings gegevens en het aantal Kruis validaties. Voor prognose taken zijn er aanvullende para meters die moeten worden ingesteld die van invloed zijn op het experiment. In de volgende tabel worden de para meters en het gebruik ervan toegelicht.

| Parameter &nbsp; naam | Description | Vereist |
|-------|-------|-------|
|`time_column_name`|Wordt gebruikt om de kolom datetime op te geven in de invoer gegevens die worden gebruikt voor het bouwen van de tijd reeks en het uitstellen van de frequentie.|✓|
|`grain_column_names`|Naam (en) die afzonderlijke reeks groepen in de invoer gegevens definiëren. Als korrel niet is gedefinieerd, wordt ervan uitgegaan dat de gegevensset één keer wordt gebruikt.||
|`max_horizon`|Definieert de maximale gewenste prognose horizon in eenheden van de time-series. Eenheden zijn gebaseerd op het tijds interval van uw trainings gegevens, bijvoorbeeld maandelijks, wekelijks dat de Forecaster moet voors pellen.|✓|
|`target_lags`|Het aantal rijen dat de doel waarden moeten worden vertraagd op basis van de frequentie van de gegevens. De vertraging wordt weer gegeven als een lijst of één geheel getal. Er moet een vertraging worden gebruikt wanneer de relatie tussen de onafhankelijke variabelen en de afhankelijke variabele standaard niet overeenkomt met of correleert. Wanneer u bijvoorbeeld de vraag voor een product wilt voors pellen, is de vraag in elke maand mogelijk afhankelijk van de prijs van specifieke producten 3 maanden vóór. In dit voor beeld wilt u mogelijk het doel (de vraag) met drie maanden uitsteld, zodat het model op de juiste relatie wordt getraind.||
|`target_rolling_window_size`|*n* historische Peri Oden die moeten worden gebruikt voor het genereren van prognose waarden, <= grootte van de Trainingsset. Als u dit weglaat, is *n* de volledige grootte van de Trainingsset. Geef deze para meter op als u alleen een bepaalde hoeveelheid geschiedenis wilt beschouwen bij het trainen van het model.||
|`enable_dnn`|DNNs voor het maken van prognoses.||

Raadpleeg de [referentie documentatie](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) voor meer informatie.

Maak de instellingen voor de tijd reeks als een woordenlijst object. Stel de `time_column_name` in op het `day_datetime` veld in de gegevensset. Definieer de `grain_column_names` para meter om ervoor te zorgen dat er **twee afzonderlijke tijdreeks groepen** worden gemaakt voor de gegevens. een voor Store A en B. Tenslotte stelt u de 50 in op om te `max_horizon` voors pellen op de hele testset. Stel een venster voor een prognose in op 10 Peri Oden met `target_rolling_window_size` , en geef een enkele vertraging op voor de doel waarden voor twee Peri Oden vooruit met de `target_lags` para meter. U kunt het beste instellen `max_horizon` op `target_rolling_window_size` `target_lags` ' auto ', waarmee deze waarden automatisch voor u worden gedetecteerd. In het onderstaande voor beeld zijn ' automatische instellingen ' gebruikt voor deze para meters. 

```python
time_series_settings = {
    "time_column_name": "day_datetime",
    "grain_column_names": ["store"],
    "max_horizon": "auto",
    "target_lags": "auto",
    "target_rolling_window_size": "auto",
    "preprocess": True,
}
```

> [!NOTE]
> Geautomatiseerde machine learning-voorverwerkingsstappen (kenmerknormalisatie, het verwerken van ontbrekende gegevens, het converteren van tekst naar numerieke waarden, enzovoort) worden onderdeel van het onderliggende model. Wanneer u het model voor voorspellingen gebruikt, worden dezelfde voorverwerkingsstappen die tijdens de training zijn toegepast, automatisch toegepast op uw invoergegevens.

Als u de `grain_column_names` in het bovenstaande code fragment definieert, maakt AutoML twee afzonderlijke tijdreeks groepen, ook wel bekend als meerdere tijd reeksen. Als er geen korrel is gedefinieerd, wordt ervan uitgegaan dat de gegevensset een enkele time series is. Zie de [energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand)voor meer informatie over één time-serie.

Maak nu een standaard `AutoMLConfig` object, geef het `forecasting` taak type op en verzend het experiment. Wanneer het model is voltooid, haalt u de best mogelijke run-iteratie op.

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

Zie de voor beelden van voor beeld van de [voorbeeld notitieblokken](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) voor gedetailleerde code voorbeelden van een geavanceerde prognose configuratie, waaronder:

* [Kerst detectie en parametrisatie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [Cross-Origin-Kruis validatie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [Configureer bare lags](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [statistische functies van het Rolling venster](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [DNN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)

### <a name="configure-a-dnn-enable-forecasting-experiment"></a>Een DNN-experiment voor het maken van prognoses configureren

> [!NOTE]
> DNN-ondersteuning voor prognoses in geautomatiseerde Machine Learning is een preview-versie en wordt niet ondersteund voor lokale uitvoeringen.

Als u gebruik wilt maken van DNNs voor prognoses, moet u de `enable_dnn` para meter in de AutoMLConfig instellen op True. 

```python
automl_config = AutoMLConfig(task='forecasting',
                             enable_dnn=True,
                             ...
                             **time_series_settings)
```
Meer informatie over [de AutoMLConfig](#configure-and-run-experiment).

U kunt ook de `Enable deep learning` optie in de Studio selecteren.
![alternatieve tekst](./media/how-to-auto-train-forecast/enable_dnn.png)

U kunt het beste een AML Compute-cluster gebruiken met GPU-Sku's en ten minste twee knoop punten als het reken doel. Om voldoende tijd te bieden aan het volt ooien van de DNN-training, wordt aangeraden om het experiment in te stellen op een minimum van een paar uur.
Zie de documentatie over [AML Compute](how-to-set-up-training-targets.md#amlcompute) en [GPU Optimized virtual machine size](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)(Engelstalig) voor meer informatie over de grootte van AML-berekeningen en VM-grootten die GPU bevatten.

Bekijk het voor beeld van de [drank productie prognose](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb) voor een gedetailleerd code voorbeeld met DNNs.

### <a name="target-rolling-window-aggregation"></a>Aggregatie van het doel venster
De beste informatie die een Forecaster kan hebben, is vaak de recente waarde van het doel. Het maken van cumulatieve statistieken van het doel kan de nauw keurigheid van de voor spelling verg Roten. Met aggregaties van het doel venster kunt u een roulerende aggregatie van gegevens waarden toevoegen als onderdelen. Als u Rolling vensters wilt inschakelen, stelt `target_rolling_window_size` u de gewenste grootte in voor het gehele venster. 

Een voor beeld hiervan is te zien bij het voors pellen van energie vraag. U kunt een functie voor een rolling venster van drie dagen toevoegen om te voorzien in thermische wijzigingen van verwarmde ruimten. In het onderstaande voor beeld hebben we dit venster met de grootte drie ingesteld op `target_rolling_window_size=3` de `AutoMLConfig` constructor. In de tabel wordt de functie techniek weer gegeven die zich voordoet wanneer de venster aggregatie wordt toegepast. Kolommen voor minimum, maximum en som worden gegenereerd op een sliding window van drie op basis van de gedefinieerde instellingen. Elke rij heeft een nieuwe berekende functie, in het geval van een tijds tempel voor 8 september 2017 4: am de waarden voor maximum, minimum en som worden berekend met behulp van de vraag waarden voor 8 september 2017 1: am-3: am. In dit venster van drie ploegen worden de gegevens voor de resterende rijen ingevuld.

![alternatieve tekst](./media/how-to-auto-train-forecast/target-roll.svg)

Het genereren en gebruiken van deze aanvullende functies als extra contextuele gegevens helpt de nauw keurigheid van het trein model.

Bekijk een voor beeld van een python-code met behulp van de [samenvoeg functie van het doel venster](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb).

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

De `forecast()` functie moet worden gebruikt in plaats van `predict()` , waardoor de specificaties van wanneer voor spellingen moeten worden gestart. In het volgende voor beeld vervangt u eerst alle waarden in `y_pred` met `NaN` . In dit geval wordt de oorsprong van de prognose aan het einde van de trainings gegevens weer gegeven, omdat deze normaal gesp roken zou zijn bij het gebruik van `predict()` . Als u echter alleen de tweede helft van met hebt `y_pred` vervangen `NaN` , zou de functie de numerieke waarden in de eerste helft ongewijzigd laten, maar de `NaN` waarden in de tweede helft worden geforecastd. De functie retourneert zowel de voorspelde waarden als de uitgelijnde functies.

U kunt ook de `forecast_destination` para meter in de `forecast()` functie gebruiken om waarden te ramen tot een opgegeven datum.

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_pipeline.forecast(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

Bereken RMSE (root mean Error) tussen de `actual_labels` werkelijke waarden en de geraamde waarden in `predict_labels` .

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_labels, predict_labels))
rmse
```

Nu de algehele model nauwkeurigheid is vastgesteld, is de volgende stap het model te gebruiken voor het voors pellen van onbekende toekomstige waarden. Geef een gegevensset op in dezelfde indeling als de testset `test_data` , maar met toekomstige datetimes en de resulterende Voorspellings is de voorspelde waarden voor elke stap van de tijds reeks. Stel dat de laatste tijdreeks records in de gegevensset 12/31/2018 zijn. Voor een prognose van de vraag voor de volgende dag (of zoveel Peri Oden als u nodig hebt om een prognose te maken, <= `max_horizon` ), maakt u een eenmalige reeks record voor elke Store voor 01/01/2019.

```output
day_datetime,store,week_of_year
01/01/2019,A,1
01/01/2019,A,1
```

Herhaal de stappen die nodig zijn om deze toekomstige gegevens naar een data frame te laden en voer vervolgens uit `best_run.predict(test_data)` om toekomstige waarden te voors pellen.

> [!NOTE]
> Waarden kunnen niet worden voor speld voor het aantal Peri Oden dat groter is dan de `max_horizon` . Het model moet opnieuw worden getraind met een grotere horizon om toekomstige waarden buiten de huidige horizon te voors pellen.

## <a name="next-steps"></a>Volgende stappen

* Volg de [zelf studie](tutorial-auto-train-models.md) voor meer informatie over het maken van experimenten met automatische machine learning.
* Bekijk de referentie documentatie [voor de Azure machine learning SDK voor python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) .
