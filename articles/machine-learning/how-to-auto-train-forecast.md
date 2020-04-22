---
title: Auto-trein een time-series voorspelling model
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van Azure Machine Learning om een regressiemodel voor tijdreeksen te trainen met behulp van geautomatiseerde machine learning.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 9f80156f61ad82e5563f1c38764c81297f5979f2
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767319"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Auto-trein een time-series voorspelling model
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u een regressiemodel voor tijdreeksen forecasting traint met behulp van geautomatiseerde machine learning in Azure Machine Learning. Het configureren van een prognosemodel is vergelijkbaar met het instellen van een standaardregressiemodel met behulp van geautomatiseerde machine learning, maar er bestaan bepaalde configuratieopties en pre-processing-stappen voor het werken met tijdreeksgegevens. In de volgende voorbeelden ziet u hoe u:

* Gegevens voorbereiden op tijdreeksmodellering
* Specifieke tijdreeksparameters in [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) een object configureren
* Voorspellingen uitvoeren met tijdreeksgegevens

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

U geautomatiseerde ML gebruiken om technieken en benaderingen te combineren en een aanbevolen tijdreeksprognose van hoge kwaliteit te krijgen. Een geautomatiseerd tijdserieexperiment wordt behandeld als een multivariate regressieprobleem. Waarden uit het verleden worden "gedraaid" om extra dimensies te worden voor de regressor samen met andere voorspellers.

Deze aanpak, in tegenstelling tot klassieke tijdreeksen methoden, heeft een voordeel van het natuurlijk opnemen van meerdere contextuele variabelen en hun relatie met elkaar tijdens de training. In real-world forecasting toepassingen kunnen meerdere factoren een prognose beïnvloeden. Bijvoorbeeld bij het voorspellen van de verkoop, interacties van historische trends, wisselkoers en prijs vormen allemaal gezamenlijk het verkoopresultaat. Een ander voordeel is dat alle recente innovaties in regressiemodellen onmiddellijk van toepassing zijn op prognoses.

U [configureren](#config) hoe ver in de toekomst de prognose moet worden uitgebreid (de prognosehorizon), evenals vertragingen en meer. Geautomatiseerde ML leert een enkel, maar vaak intern vertakt model voor alle items in de dataset en voorspelling horizonten. Meer gegevens zijn dus beschikbaar om modelparameters te schatten en generalisatie naar onzichtbare reeksen wordt mogelijk.

Functies die uit de trainingsgegevens worden gehaald, spelen een cruciale rol. En geautomatiseerde ML voert standaard pre-processing stappen uit en genereert extra tijd-serie functies om seizoensgebonden effecten vast te leggen en voorspellende nauwkeurigheid te maximaliseren.

## <a name="time-series-and-deep-learning-models"></a>Tijdreeksen en deep learning-modellen


Automated ML biedt gebruikers zowel native time-series als deep learning-modellen als onderdeel van het aanbevelingssysteem. Deze leerlingen zijn onder andere:
+ Profeet (preview)
+ Auto-ARIMA (preview)
+ ForecastTCN (voorbeeld)

Geautomatiseerde ML's deep learning maakt het mogelijk om univariate- en multivariate tijdreeksgegevens te voorspellen.

Deep learning-modellen hebben drie intrinsieke mogelijkheden:
1. Ze kunnen leren van willekeurige mappings van ingangen tot outputs
1. Ze ondersteunen meerdere ingangen en uitgangen
1. Ze kunnen automatisch patronen extraheren in invoergegevens die zich uitstrekken over lange reeksen

Gezien grotere gegevens kunnen deep learning-modellen, zoals ForecastTCN van Microsoft, de scores van het resulterende model verbeteren. Meer informatie over het [configureren van uw experiment voor deep learning.](#configure-a-dnn-enable-forecasting-experiment)

Native time series studenten worden ook verstrekt als onderdeel van geautomatiseerde ML. Prophet werkt het beste met tijdreeksen die sterke seizoenseffecten en verschillende seizoenen van historische gegevens hebben. Prophet is nauwkeurig & snel, robuust voor uitschieters, ontbrekende gegevens en dramatische veranderingen in uw tijdreeks. 

AutoRegressive Integrated Moving Average (ARIMA) is een populaire statistische methode voor tijdreeksen voorspelling. Deze techniek van prognoses wordt vaak gebruikt in kortetermijnvoorspellingsscenario's waarbij gegevens aanwijzingen tonen van trends zoals cycli, die onvoorspelbaar en moeilijk te modelleren of te voorspellen kunnen zijn. Auto-ARIMA zet uw gegevens om in stationaire gegevens om consistente, betrouwbare resultaten te ontvangen.

## <a name="prerequisites"></a>Vereisten

* Een Azure Machine Learning-werkruimte. Zie Een Azure [Machine Learning-werkruimte maken](how-to-manage-workspace.md)als u de werkruimte wilt maken.
* In dit artikel wordt uitgegaan van basisbekendheid bij het opzetten van een geautomatiseerd machine learning-experiment. Volg de [zelfstudie](tutorial-auto-train-models.md) of [how-to](how-to-configure-auto-train.md) om de basisgeautomatiseerde machine learning-experimentontwerppatronen te bekijken.

## <a name="preparing-data"></a> Gegevens voorbereiden

Het belangrijkste verschil tussen een voorspellingsregressietaaktype en het type regressietaak binnen geautomatiseerde machine learning is het opnemen van een functie in uw gegevens die een geldige tijdreeks vertegenwoordigt. Een reguliere tijdreeks heeft een goed gedefinieerde en consistente frequentie en heeft een waarde op elk monsterpunt in een continue tijdspanne. Houd rekening met de `sample.csv`volgende momentopname van een bestand .

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

Deze gegevensset is een eenvoudig voorbeeld van dagelijkse verkoopgegevens voor een bedrijf dat twee verschillende `week_of_year` winkels heeft, A en B. Daarnaast is er een functie voor die het model in staat stelt om wekelijkse seizoensgebondenheid te detecteren. Het `day_datetime` veld vertegenwoordigt een schone tijdreeks met `sales_quantity` dagelijkse frequentie en het veld is de doelkolom voor het uitvoeren van voorspellingen. Lees de gegevens in een Pandas-gegevensframe en gebruik `to_datetime` de `datetime` functie om ervoor te zorgen dat de tijdreeks een type is.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

In dit geval worden de gegevens al oplopend gesorteerd op het tijdsveld `day_datetime`. Controleer er echter bij het instellen van een experiment op dat de gewenste tijdkolom in oplopende volgorde wordt gesorteerd om een geldige tijdreeks te bouwen. Stel dat de gegevens 1.000 records bevatten en een deterministische splitsing in de gegevens maken om trainings- en testgegevenssets te maken. Identificeer de naam van de labelkolom en stel deze in op label. In dit voorbeeld wordt `sales_quantity`het label . Scheid vervolgens het `test_data` labelveld `test_target` van de set.

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
```

> [!NOTE]
> Wanneer u een model traint voor het voorspellen van toekomstige waarden, moet u ervoor zorgen dat alle functies die in de training worden gebruikt, kunnen worden gebruikt bij het uitvoeren van voorspellingen voor uw beoogde horizon. Bijvoorbeeld, bij het maken van een vraagprognose, met inbegrip van een functie voor de huidige aandelenkoers kan enorm verhogen training nauwkeurigheid. Als u echter van plan bent te voorspellen met een lange horizon, u mogelijk niet nauwkeurig toekomstige voorraadwaarden voorspellen die overeenkomen met toekomstige tijdreekspunten en kan de nauwkeurigheid van het model eronder lijden.

<a name="config"></a>
## <a name="configure-and-run-experiment"></a>Experiment configureren en uitvoeren

Voor het voorspellen van taken maakt geautomatiseerde machine learning gebruik van pre-processing- en schattingsstappen die specifiek zijn voor tijdreeksgegevens. De volgende pre-processing stappen worden uitgevoerd:

* Detecteer de frequentie van de tijdreeksen (bijvoorbeeld per uur, dagelijks, wekelijks) en maak nieuwe records voor afwezige tijdpunten om de reeks continu te maken.
* Ontbrekende waarden in het doel (via forward-fill) en functiekolommen (met behulp van mediane kolomwaarden) aanklagen
* Op basis van granen gebaseerde functies maken om vaste effecten in verschillende reeksen mogelijk te maken
* Creëer op tijd gebaseerde functies om te helpen bij het leren van seizoenspatronen
* Categorische variabelen coderen tot numerieke hoeveelheden

Het [`AutoMLConfig`](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py) object definieert de instellingen en gegevens die nodig zijn voor een geautomatiseerde machine learning-taak. Net als bij een regressieprobleem definieert u standaardtrainingsparameters zoals taaktype, aantal iteraties, trainingsgegevens en het aantal kruisvalidaties. Voor prognosetaken zijn er extra parameters die moeten worden ingesteld die van invloed zijn op het experiment. In de volgende tabel wordt elke parameter en het gebruik ervan uitgelegd.

| Parameternaam&nbsp; | Beschrijving | Vereist |
|-------|-------|-------|
|`time_column_name`|Wordt gebruikt om de kolom datetime op te geven in de invoergegevens die worden gebruikt voor het bouwen van de tijdreeks en het afleiden van de frequentie.|✓|
|`grain_column_names`|Naam(en) het definiëren van afzonderlijke reeksgroepen in de invoergegevens. Als korrel niet is gedefinieerd, wordt aangenomen dat de gegevensset één tijdreeks is.||
|`max_horizon`|Hiermee definieert u de maximaal gewenste prognosehorizon in eenheden van de frequentie van de tijdreeksen. Eenheden zijn gebaseerd op het tijdsinterval van uw trainingsgegevens, bijvoorbeeld maandelijks, wekelijks dat de voorspeller moet voorspellen.|✓|
|`target_lags`|Aantal rijen om de doelwaarden te laten liggen op basis van de frequentie van de gegevens. De vertraging wordt weergegeven als een lijst of een geheel getal. Vertraging moet worden gebruikt wanneer de relatie tussen de onafhankelijke variabelen en de afhankelijke variabele niet standaard overeenkomt met of correleert. Wanneer u bijvoorbeeld de vraag naar een product probeert te voorspellen, kan de vraag in elke maand afhangen van de prijs van specifieke grondstoffen 3 maanden eerder. In dit voorbeeld u het doel (vraag) negatief laten liggen met 3 maanden, zodat het model wordt getraind op de juiste relatie.||
|`target_rolling_window_size`|*n* historische perioden die moeten worden gebruikt om voorspelde waarden te genereren, <= trainingssetgrootte. Als weggelaten, *n* is de volledige trainingsset grootte. Geef deze parameter op wanneer u alleen een bepaalde hoeveelheid geschiedenis wilt overwegen bij het trainen van het model.||
|`enable_dnn`|DNN's voor voorspellen inschakelen.||

Zie de [referentiedocumentatie](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) voor meer informatie.

Maak de instellingen van de tijdreeks als woordenboekobject. Stel `time_column_name` het `day_datetime` veld in op het veld in de gegevensset. Definieer `grain_column_names` de parameter om ervoor te zorgen dat **er twee afzonderlijke tijdreeksgroepen** voor de gegevens worden gemaakt; een voor winkel A en B. `max_horizon` Ten slotte, stel de 50 om te voorspellen voor de gehele test set. Stel een prognosevenster in `target_rolling_window_size`op 10 perioden met en geef één `target_lags` vertraging op van de doelwaarden gedurende twee perioden vooruit met de parameter. Het wordt aanbevolen `max_horizon`om `target_rolling_window_size` `target_lags` in te stellen, en om "auto" die automatisch detecteert deze waarden voor u. In het onderstaande voorbeeld zijn 'automatische' instellingen gebruikt voor deze parameters. 

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
> Geautomatiseerde stappen voor machine learning voor verwerking (functienormalisatie, omgaan met ontbrekende gegevens, converteren van tekst naar numeriek, enz.) worden onderdeel van het onderliggende model. Bij het gebruik van het model voor voorspellingen worden de tijdens de training toegepaste preverwerkingsstappen automatisch toegepast op uw invoergegevens.

Door het `grain_column_names` bovenstaande codefragment te definiëren, maakt AutoML twee afzonderlijke tijdreeksgroepen, ook wel meerdere tijdreeksen genoemd. Als er geen korrel is gedefinieerd, gaat AutoML ervan uit dat de gegevensset één tijdreeks is. Zie de [energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand)voor meer informatie over enkele tijdreeksen.

Maak nu `AutoMLConfig` een standaardobject, `forecasting` geef het taaktype op en verzend het experiment. Nadat het model is voltooid, haalt u de beste uitvoeringiteratie op.

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

Zie de [voorbeeldnotitieblokken](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) voor prognoses voor gedetailleerde codevoorbeelden van geavanceerde prognoseconfiguraties, waaronder:

* [vakantiedetectie en featurization](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [crossvalidatie van rolling-origine](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [configureerbare vertragingen](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [functies voor rollend vensteraggregaten](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [Dnn](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)

### <a name="configure-a-dnn-enable-forecasting-experiment"></a>Een DNN-experiment voor prognoses configureren

> [!NOTE]
> DNN-ondersteuning voor prognoses in Automated Machine Learning wordt in Preview weergegeven en wordt niet ondersteund voor lokale uitvoeringen.

Om DNN's te gebruiken voor prognoses, `enable_dnn` moet u de parameter in de AutoMLConfig op true instellen. 

```python
automl_config = AutoMLConfig(task='forecasting',
                             enable_dnn=True,
                             ...
                             **time_series_settings)
```
Meer informatie over [de AutoMLConfig](#configure-and-run-experiment).

U ook de `Enable deep learning` optie in de studio selecteren.
![alternatieve tekst](./media/how-to-auto-train-forecast/enable_dnn.png)

We raden u aan een AML Compute-cluster te gebruiken met GPU-SKU's en ten minste twee knooppunten als rekendoel. Om voldoende tijd te hebben om de DNN-training te voltooien, raden we u aan de time-out van het experiment in te stellen op een minimum van een paar uur.
Zie de [Documentatie AML Compute-gegevens](how-to-set-up-training-targets.md#amlcompute) en [GPU-geoptimaliseerde documentatie voor virtuele machineformaten](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)voor meer informatie over AML-compute- en VM-formaten die GPU's bevatten.

Bekijk het [drankenproductieprognosenotitieblok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb) voor een gedetailleerd codevoorbeeld met behulp van DNN's.

### <a name="view-feature-engineering-summary"></a>Overzicht van functie-engineering weergeven

Voor taaktypen in tijdreeksen in geautomatiseerde machine learning u details bekijken van het functieengineeringproces. De volgende code toont elke ruwe functie samen met de volgende kenmerken:

* Naam van raw-functie
* Aantal ontworpen functies gevormd uit deze ruwe functie
* Type gedetecteerd
* Of de functie is verwijderd
* Lijst met functietransformaties voor de raw-functie

```python
fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
```

## <a name="forecasting-with-best-model"></a>Prognoses met het beste model

Gebruik de beste modeliteratie om waarden voor de testgegevensset te voorspellen.

De `forecast()` functie moet worden `predict()`gebruikt in plaats van, dit zal specificaties van wanneer voorspellingen moeten beginnen. In het volgende voorbeeld vervangt u `y_pred` `NaN`eerst alle waarden in door . De verwachte oorsprong zal in dit geval aan het einde van `predict()`de opleidingsgegevens liggen, zoals het normaal gesproken zou zijn bij het gebruik . Als u echter alleen de `y_pred` tweede `NaN`helft van vervangen door , zou de functie laat `NaN` de numerieke waarden in de eerste helft ongewijzigd, maar de voorspelling van de waarden in de tweede helft. De functie retourneert zowel de voorspelde waarden als de uitgelijnde functies.

U de `forecast_destination` parameter `forecast()` in de functie ook gebruiken om waarden tot een bepaalde datum te voorspellen.

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_pipeline.forecast(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

Bereken RMSE (hoofdgemiddelde kwadraatfout) tussen de `actual_labels` werkelijke `predict_labels`waarden en de voorspelde waarden in .

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_labels, predict_labels))
rmse
```

Nu de algehele modelnauwkeurigheid is bepaald, is de meest realistische volgende stap om het model te gebruiken om onbekende toekomstige waarden te voorspellen. Lever een gegevensset in dezelfde indeling `test_data` als de testset, maar met toekomstige datumtijden, en de resulterende voorspellingsset is de voorspelde waarden voor elke stap in de tijdreeks. Stel dat de laatste tijdreeksrecords in de gegevensset voor 12/31/2018 waren. Als u de vraag voor de volgende dag wilt voorspellen `max_horizon`(of zoveel perioden als u moet voorspellen, <= ), maakt u voor 01/01/2019 één tijdreeksrecord voor elke winkel.

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

Herhaal de noodzakelijke stappen om deze toekomstige gegevens `best_run.predict(test_data)` naar een gegevensframe te laden en voer deze uit om toekomstige waarden te voorspellen.

> [!NOTE]
> Waarden kunnen niet worden voorspeld voor `max_horizon`een aantal perioden die groter zijn dan de . Het model moet worden omgeschoold met een grotere horizon om toekomstige waarden te voorspellen buiten de huidige horizon.

## <a name="next-steps"></a>Volgende stappen

* Volg de [zelfstudie](tutorial-auto-train-models.md) om te leren hoe u experimenten maken met geautomatiseerde machine learning.
* Bekijk de [naslagdocumentatie van Azure Machine Learning SDK voor Python.](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
