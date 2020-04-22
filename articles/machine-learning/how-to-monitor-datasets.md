---
title: Analyseren en controleren op gegevensdrift op gegevenssets (voorbeeld)
titleSuffix: Azure Machine Learning
description: Azure Machine Learning-gegevenssets maken (voorbeeld), monitor voor gegevensdrift in gegevenssets en stel waarschuwingen in.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/04/2019
ms.openlocfilehash: e49c621d92a8aa604b5f95291c5d80c0141f41dd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682726"
---
# <a name="detect-data-drift-preview-on-datasets"></a>Gegevensdrift (voorbeeld) detecteren op gegevenssets
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u Azure Machine Learning-gegevenssetmonitors (voorbeeld) maakt, controleert op gegevensdrift en statistische wijzigingen in gegevenssets en waarschuwingen instelt.

Met Azure Machine Learning-gegevenssetmonitors u het als volgt gaan:
* **Analyseer drift in uw gegevens** om te begrijpen hoe deze in de loop van de tijd verandert.
* **Modelgegevens controleren** op verschillen tussen trainings- en serveergegevenssets.
* **Monitor nieuwe gegevens** op verschillen tussen een basislijn en doelgegevensset.
* **Profielfuncties in gegevens** om bij te houden hoe statistische eigenschappen in de loop van de tijd veranderen.
* **Waarschuwingen instellen voor gegevensdrift** voor vroegtijdige waarschuwingen voor potentiële problemen. 

Statistieken en inzichten zijn beschikbaar via de [Azure Application Insights-bron](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) die is gekoppeld aan de Azure Machine Learning-werkruimte.

> [!Important]
> Houd er rekening mee dat het bewaken van gegevens drift met de SDK is beschikbaar in alle edities, terwijl het toezicht op gegevens drift door de studio op het web is Enterprise edition alleen.

## <a name="prerequisites"></a>Vereisten

Als u gegevenssetmonitors wilt maken en ermee wilt werken, hebt u het sein nodig:
* Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure Machine Learning.](https://aka.ms/AMLFree)
* Een [Azure Machine Learning-werkruimte](how-to-manage-workspace.md).
* De [Azure Machine Learning SDK voor Python geïnstalleerd,](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)die de azureml-datasets pakket omvat.
* Gestructureerde (tabelvormige) gegevens met een tijdstempel die is opgegeven in het bestandspad, de bestandsnaam of de kolom in de gegevens.

## <a name="what-is-data-drift"></a>Wat is datadrift?

In de context van machine learning is datadrift de verandering in modelinvoergegevens die leidt tot prestatiedegradatie van het model. Het is een van de belangrijkste redenen model nauwkeurigheid degradeert na verloop van tijd, dus monitoring van gegevens drift helpt detecteren model prestaties problemen.

Oorzaken van gegevensdrift zijn: 

- Upstream proces veranderingen, zoals een sensor wordt vervangen die de eenheden van de meting verandert van centimeter tot centimeters. 
- Problemen met de gegevenskwaliteit, zoals een kapotte sensor die altijd 0 leest.
- Natuurlijke drift in de gegevens, zoals gemiddelde temperatuur veranderen met de seizoenen.
- Verandering in relatie tussen functies, of covariate verschuiving. 

Met Azure Machine Learning-gegevenssetmonitors u waarschuwingen instellen die helpen bij het detecteren van gegevensdrift in gegevenssets in de loop van de tijd. 

### <a name="dataset-monitors"></a>Gegevenssetmonitors 

U een gegevenssetmonitor maken om gegevensdrift op nieuwe gegevens in een gegevensset te detecteren en te waarschuwen, historische gegevens voor drift te analyseren en nieuwe gegevens in de loop van de tijd te profileren. Het algoritme voor gegevensdrift biedt een algemene meting van de verandering in gegevens en geeft aan welke functies verantwoordelijk zijn voor verder onderzoek. Gegevenssetmonitors produceren een aantal andere statistieken door `timeseries` nieuwe gegevens in de gegevensset te profileren. Aangepaste waarschuwingen kunnen worden ingesteld op alle statistieken die door de monitor worden gegenereerd via [Azure Application Insights.](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) Gegevenssetmonitors kunnen worden gebruikt om snel gegevensproblemen op te vangen en de tijd te verkorten om het probleem te debuggen door waarschijnlijke oorzaken te identificeren.  

Conceptueel zijn er drie primaire scenario's voor het instellen van gegevenssetmonitors in Azure Machine Learning.

Scenario | Beschrijving
---|---
Het controleren van de gegevens van een model voor het afwijken van de trainingsgegevens van het model | Resultaten van dit scenario kunnen worden geïnterpreteerd als het bewaken van een proxy voor de nauwkeurigheid van het model, gezien het feit dat modelnauwkeurigheid degradeert als de servergegevens afglijden van de trainingsgegevens.
Een gegevensset voor tijdreeksen controleren op drift van een vorige periode. | Dit scenario is algemener en kan worden gebruikt om gegevenssets te controleren die stroomopwaarts of stroomafwaarts van modelbuilding worden betrokken.  De doelgegevensset moet een tijdstempelkolom hebben, terwijl de basislijngegevensset elke tabelgegevensset kan zijn die functies gemeen heeft met de doelgegevensset.
Het uitvoeren van analyses op gegevens uit het verleden. | Dit scenario kan worden gebruikt om historische gegevens te begrijpen en beslissingen te nemen in instellingen voor gegevenssetmonitors.

## <a name="how-dataset-can-monitor-data"></a>Hoe gegevensset gegevens kan controleren

Met Azure Machine Learning wordt gegevensdrift gecontroleerd via gegevenssets. Om te controleren op gegevensdrift wordt een basislijngegevensset - meestal de trainingsgegevensset voor een model - opgegeven. Een doelgegevensset - meestal modelinvoergegevens - wordt in de loop van de tijd vergeleken met uw basislijngegevensset. Deze vergelijking betekent dat er een tijdstempelkolom moet zijn opgegeven op uw doelgegevensset.

### <a name="set-the-timeseries-trait-in-the-target-dataset"></a>De `timeseries` eigenschap instellen in de doelgegevensset

De doelgegevensset moet `timeseries` de eigenschap hebben ingesteld door de tijdstempelkolom op te geven uit een kolom in de gegevens of een virtuele kolom die is afgeleid van het padpatroon van de bestanden. Dit kan via de Python SDK of Azure Machine Learning studio. Er moet een kolom worden opgegeven die een `timeseries` "fijnkorrelige" tijdstempel vertegenwoordigt, om de eigenschap aan de gegevensset toe te voegen. Als uw gegevens zijn verdeeld in mapstructuur met tijdsinformatie, zoals '{yyyy/MM/dd}', u een virtuele kolom maken door de instelling van het padpatroon en deze instellen als de tijdstempel 'grofkorrelig' om het belang van de functionaliteit van tijdreeksen te verbeteren. 

#### <a name="python-sdk"></a>Python-SDK

De [`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) klassemethode [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) definieert de tijdstempelkolom voor de gegevensset. 

```python 
from azureml.core import Workspace, Dataset, Datastore

# get workspace object
ws = Workspace.from_config()

# get datastore object 
dstore = Datastore.get(ws, 'your datastore name')

# specify datastore paths
dstore_paths = [(dstore, 'weather/*/*/*/*/data.parquet')]

# specify partition format
partition_format = 'weather/{state}/{date:yyyy/MM/dd}/data.parquet'

# create the Tabular dataset with 'state' and 'date' as virtual columns 
dset = Dataset.Tabular.from_parquet_files(path=dstore_paths, partition_format=partition_format)

# assign the timestamp attribute to a real or virtual column in the dataset
dset = dset.with_timestamp_columns('date')

# register the dataset as the target dataset
dset = dset.register(ws, 'target')
```

Zie het [voorbeeldnotitieblok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb) `timeseries` of de [SDK-documentatie](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-)voor gegevenssets voor een volledig voorbeeld van het gebruik van de eigenschap van gegevenssets.

#### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

Als u uw gegevensset maakt met Azure Machine Learning-studio, moet u ervoor zorgen dat het pad naar uw gegevens tijdstempelgegevens bevat, alle submappen met gegevens opnemen en de partitieindeling instellen. 

In het volgende voorbeeld worden alle gegevens onder de submap *NoaaIsdFlorida/2019* genomen en wordt in de partitieindeling het jaar, de maand en de dag van de tijdstempel opgegeven. 

[![Partitieindeling](./media/how-to-monitor-datasets/partition-format.png)](media/how-to-monitor-datasets/partition-format-expand.png)

Geef in de **schema-instellingen** de tijdstempelkolom op van een virtuele of echte kolom in de opgegeven gegevensset:

![Tijdstempel](./media/how-to-monitor-datasets/timestamp.png)

## <a name="dataset-monitor-settings"></a>Instellingen voor gegevenssetmonitor

Zodra u uw gegevensset hebt gemaakt met de opgegeven tijdstempelinstellingen, u uw gegevenssetmonitor configureren.

De verschillende instellingen voor gegevenssetmonitor zijn onderverdeeld in drie groepen: **basisinformatie, monitorinstellingen** en **instellingen voor vulling.**

### <a name="basic-info"></a>Basisinformatie

Deze tabel bevat basisinstellingen die worden gebruikt voor de gegevenssetmonitor.

| Instelling | Beschrijving | Tips | Veranderlijk | 
| ------- | ----------- | ---- | ------- | 
| Naam | Naam van de gegevenssetmonitor. | | Nee |
| Gegevensset Basislijn | Tabeltabelgegevensset die in de loop van de tijd als basislijn wordt gebruikt voor de vergelijking van de doelgegevensset. | De basislijngegevensset moet functies gemeen hebben met de doelgegevensset. Over het algemeen moet de basislijn worden ingesteld op de trainingsgegevensset van een model of een segment van de doelgegevensset. | Nee |
| Doelgegevensset | Tabeltabelgegevensset met tijdstempelkolom opgegeven die wordt geanalyseerd voor gegevensdrift. | De doelgegevensset moet functies gemeen hebben met de `timeseries` basislijngegevensset en moet een gegevensset zijn waaraan nieuwe gegevens worden toegevoegd. Historische gegevens in de doelgegevensset kunnen worden geanalyseerd of nieuwe gegevens kunnen worden gecontroleerd. | Nee | 
| Frequency | De frequentie die wordt gebruikt om de pijplijntaak te plannen en historische gegevens te analyseren als er een backfill wordt uitgevoerd. Opties zijn dagelijks, wekelijks of maandelijks. | Pas deze instelling aan om een vergelijkbare grootte van gegevens op te nemen als de basislijn. | Nee | 
| Functies | Lijst met functies die worden geanalyseerd voor gegevens drift in de tijd. | Ingesteld op de uitvoerfunctie(en) van een model om conceptdrift te meten. Neem geen functies op die van nature in de tijd zweven (maand, jaar, index, enz.). U de monitor voor het aanvullen en bestaande gegevensdrift na het aanpassen van de lijst met functies aanvullen. | Ja | 
| Rekendoel | Azure Machine Learning compute target om de gegevenssetmonitortaken uit te voeren. | | Ja | 

### <a name="monitor-settings"></a>Monitorinstellingen

Deze instellingen zijn voor de geplande gegevenssetmonitorpijplijn, die wordt gemaakt. 

| Instelling | Beschrijving | Tips | Veranderlijk | 
| ------- | ----------- | ---- | ------- |
| Inschakelen | De planning in- of uitschakelen in de pijplijn van gegevenssetmonitor | Schakel de planning uit om historische gegevens te analyseren met de instelling voor vulling. Het kan worden ingeschakeld nadat de gegevenssetmonitor is gemaakt. | Ja | 
| Latentie | Tijd, in uren, duurt het voor gegevens te komen in de dataset. Als het bijvoorbeeld drie dagen duurt voordat gegevens in de SQL DB aankomen, stelt de gegevensset de latentie in op 72. | Kan niet worden gewijzigd nadat de gegevenssetmonitor is gemaakt | Nee | 
| E-mailadressen | E-mailadressen voor waarschuwingen op basis van schending van de drempelwaarde voor gegevensdrift. | E-mails worden verzonden via Azure Monitor. | Ja | 
| Drempelwaarde | Drempelwaarde voor gegevensdriftpercentage voor e-mailwaarschuwingen. | Verdere waarschuwingen en gebeurtenissen kunnen worden ingesteld op veel andere statistieken in de bijbehorende Application Insights-bron van de werkruimte. | Ja | 

### <a name="backfill-settings"></a>Instellingen voor vulling

Deze instellingen zijn voor het uitvoeren van een backfill op gegevens uit het verleden voor gegevensdriftmetrics.

| Instelling | Beschrijving | Tips |
| ------- | ----------- | ---- |
| Begindatum | Begindatum van de backfill-taak. | | 
| Einddatum | Einddatum van de backfill-taak. | De einddatum mag niet meer dan 31*-frequentie-eenheden van de tijd vanaf de begindatum zijn. Op een bestaande gegevenssetmonitor kunnen statistieken worden aangevuld om historische gegevens te analyseren of statistieken te vervangen door bijgewerkte instellingen. |

## <a name="create-dataset-monitors"></a>Gegevenssetmonitors maken 

Maak gegevenssetmonitors om gegevensdrift op een nieuwe gegevensset met Azure Machine Learning-studio of de Python SDK te detecteren en te waarschuwen. 

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

Als u waarschuwingen wilt instellen op uw gegevenssetmonitor, moet de werkruimte met de gegevensset waarvoor u een monitor wilt maken, beschikken over enterprise-editiemogelijkheden. 

Nadat de werkruimtefunctionaliteit is bevestigd, navigeert u naar de startpagina van de studio en selecteert u het tabblad Gegevenssets aan de linkerkant. Selecteer Gegevenssetmonitors.

![Monitorlijst](./media/how-to-monitor-datasets/monitor-list.png)

Klik op de knop **Monitor maken** en ga door de wizard door op **Volgende**te klikken.

![Wizard](./media/how-to-monitor-datasets/wizard.png)

De resulterende gegevenssetmonitor wordt weergegeven in de lijst. Selecteer deze optie om naar de detailpagina van die monitor te gaan.

### <a name="from-python-sdk"></a>Van Python SDK

Zie de [Python SDK-referentiedocumentatie over gegevensdrift](/python/api/azureml-datadrift/azureml.datadrift) voor alle details. 

In het volgende voorbeeld ziet u hoe u een gegevenssetmonitor maakt met de Python SDK

```python
from azureml.core import Workspace, Dataset
from azureml.datadrift import DataDriftDetector
from datetime import datetime

# get the workspace object
ws = Workspace.from_config()

# get the target dataset
dset = Dataset.get_by_name(ws, 'target')

# set the baseline dataset
baseline = target.time_before(datetime(2019, 2, 1))

# set up feature list
features = ['latitude', 'longitude', 'elevation', 'windAngle', 'windSpeed', 'temperature', 'snowDepth', 'stationName', 'countryOrRegion']

# set up data drift detector
monitor = DataDriftDetector.create_from_datasets(ws, 'drift-monitor', baseline, target, 
                                                      compute_target='cpu-cluster', 
                                                      frequency='Week', 
                                                      feature_list=None, 
                                                      drift_threshold=.6, 
                                                      latency=24)

# get data drift detector by name
monitor = DataDriftDetector.get_by_name(ws, 'drift-monitor')

# update data drift detector
monitor = monitor.update(feature_list=features)

# run a backfill for January through May
backfill1 = monitor.backfill(datetime(2019, 1, 1), datetime(2019, 5, 1))

# run a backfill for May through today
backfill1 = monitor.backfill(datetime(2019, 5, 1), datetime.today())

# disable the pipeline schedule for the data drift detector
monitor = monitor.disable_schedule()

# enable the pipeline schedule for the data drift detector
monitor = monitor.enable_schedule()
```

Zie ons [voorbeeldnotitieblok](https://aka.ms/datadrift-notebook)voor `timeseries` een volledig voorbeeld van het instellen van een gegevensset en datadriftdetector.

## <a name="understanding-data-drift-results"></a>Inzicht in gegevensdriftresultaten

De gegevensmonitor produceert twee groepen resultaten: Drift-overzicht en functiedetails. De volgende animatie toont de beschikbare driftmonitordiagrammen op basis van de geselecteerde functie en statistiek. 

![Demovideo](./media/how-to-monitor-datasets/video.gif)

### <a name="drift-overview"></a>Overzicht van drift

De sectie **Drift-overzicht** bevat inzichten op het hoogste niveau in de omvang van gegevensdrift en welke functies verder moeten worden onderzocht. 

| Gegevens | Beschrijving | Tips | 
| ------ | ----------- | ---- | 
| Gegevensdriftmagnitude | Gegeven als een percentage tussen de basislijn en doelgegevensset in de loop van de tijd. Variërend van 0 tot 100 waar 0 identieke gegevenssets aangeeft en 100 aangeeft dat de Azure Machine Learning-gegevensdriftmogelijkheid de twee gegevenssets volledig uit elkaar kan houden. | Ruis in het precieze gemeten percentage wordt verwacht als gevolg van machine learning technieken worden gebruikt om deze omvang te genereren. | 
| Driftbijdrage per functie | De bijdrage van elke functie in de doelgegevensset aan de gemeten driftmagnitude. |  Als gevolg van covariate verschuiving, de onderliggende verdeling van een functie hoeft niet noodzakelijkerwijs te veranderen om relatief hoog functie belang hebben. | 

De volgende afbeelding is een voorbeeld van grafieken die worden weergegeven in het **overzicht Drift** in Azure Machine Learning-studio, als gevolg van een backfill van [NOAA Integrated Surface Data](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/). Gegevens werden bemonsterd naar `stationName contains 'FLORIDA'`, met januari 2019 wordt gebruikt als de basislijn dataset en alle 2019 gegevens gebruikt als het doel.
 
![Overzicht van drift](./media/how-to-monitor-datasets/drift-overview.png)

### <a name="feature-details"></a>Functiedetails

De sectie **Functiedetails** bevat inzicht op functieniveau in de wijziging in de distributie van de geselecteerde functie en andere statistieken in de loop van de tijd. 

De doelgegevensset wordt in de loop van de tijd ook geprofileerd. De statistische afstand tussen de basislijnverdeling van elke functie wordt vergeleken met die van de doelgegevensset in de loop van de tijd, wat conceptueel vergelijkbaar is met de omvang van de gegevensdrift, met de uitzondering dat deze statistische afstand voor een afzonderlijke functie is. Min, max, en gemiddelde zijn ook beschikbaar. 

Als u in de Azure Machine Learning-studio op een gegevenspunt in de grafiek klikt, wordt de verdeling van de weergegeven functie dienovereenkomstig aangepast. Standaard wordt de verdeling van de basislijngegevensset weergegeven en de meest recente run seis verdeling van dezelfde functie. 

Deze statistieken kunnen ook worden opgehaald in `get_metrics()` de `DataDriftDetector` Python SDK via de methode op een object. 

#### <a name="numeric-features"></a>Numerieke kenmerken 

Numerieke functies worden geprofileerd in elke werksetmonitor. In de Azure Machine Learning-studio worden de volgende gegevens weergegeven. Kansdichtheid wordt weergegeven voor de verdeling.

| Gegevens | Beschrijving |  
| ------ | ----------- |  
| Wasserstein afstand | Minimale hoeveelheid werk om de basislijnverdeling om te zetten in de doelverdeling. |
| Gemiddelde waarde | Gemiddelde waarde van de functie. |
| Minimumwaarde | Minimumwaarde van de functie. |
| Maximumwaarde | Maximale waarde van de functie. |

![Functiedetails numeriek](./media/how-to-monitor-datasets/feature-details.png)

#### <a name="categorical-features"></a>Categorische functies 

Numerieke functies worden geprofileerd in elke werksetmonitor. In de Azure Machine Learning-studio worden de volgende gegevens weergegeven. Voor de distributie wordt een histogram getoond.

| Gegevens | Beschrijving |  
| ------ | ----------- |  
| Euclidische afstand | Geometrische afstand tussen basislijn- en doelverdelingen. |
| Unieke waarden | Aantal unieke waarden (kardinaliteit) van de functie. |


![Functiedetails categorisch](./media/how-to-monitor-datasets/feature-details2.png)

## <a name="metrics-alerts-and-events"></a>Statistieken, waarschuwingen en gebeurtenissen

Statistieken kunnen worden opgevraagd in de [Azure Application Insights-bron](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) die is gekoppeld aan uw machine learning-werkruimte. Dat geeft toegang tot alle functies van Application Insights, waaronder het instellen van aangepaste waarschuwingsregels en actiegroepen om een actie te activeren, zoals een e-mail/sms/push/voice- of Azure-functie. Raadpleeg de volledige Application Insights documentatie voor meer informatie. 

Als u aan de slag wilt, navigeert u naar de Azure-portal en selecteert u de **overzichtspagina van** uw werkruimte.  De bijbehorende Application Insights-bron is uiterst rechts:

[![Overzicht van de Azure Portal](./media/how-to-monitor-datasets/ap-overview.png)](media/how-to-monitor-datasets/ap-overview-expanded.png)

Selecteer Logboeken (Analytics) onder Controle in het linkerdeelvenster:

![Overzicht van toepassingsinzichten](./media/how-to-monitor-datasets/ai-overview.png)

De gegevenssetmonitorstatistieken `customMetrics`worden opgeslagen als . U een query schrijven en uitvoeren nadat u een gegevenssetmonitor hebt ingesteld om deze te bekijken:

[![Query voor logboekanalyse](./media/how-to-monitor-datasets/simple-query.png)](media/how-to-monitor-datasets/simple-query-expanded.png)

Nadat u statistieken hebt geïdentificeerd om waarschuwingsregels in te stellen, maakt u een nieuwe waarschuwingsregel:

![Nieuwe waarschuwingsregel](./media/how-to-monitor-datasets/alert-rule.png)

U een bestaande actiegroep gebruiken of een nieuwe groep maken om de actie te definiëren waaraan moet worden ondernomen wanneer aan de ingestelde voorwaarden is voldaan:

![Nieuwe actiegroep](./media/how-to-monitor-datasets/action-group.png)

## <a name="troubleshooting"></a>Problemen oplossen

Beperkingen en bekende problemen:

* Het tijdsbereik van backfill-taken is beperkt tot 31 intervallen van de frequentieinstelling van de monitor. 
* Beperking van 200 functies, tenzij een lijst met functies niet is opgegeven (alle functies worden gebruikt).
* De rekengrootte moet groot genoeg zijn om de gegevens te verwerken. 
* Zorg ervoor dat uw gegevensset gegevens heeft binnen de begin- en einddatum voor een bepaalde monitorrun.
* Gegevenssetmonitors werken alleen op gegevenssets die 50 rijen of meer bevatten. 

Kolommen of functies in de gegevensset worden geclassificeerd als categorisch of numeriek op basis van de voorwaarden in de volgende tabel. Als de functie niet aan deze voorwaarden voldoet - bijvoorbeeld een kolom van typetekenreeks met >100 unieke waarden - wordt de functie uit ons algoritme voor gegevensdrift verwijderd, maar wordt deze nog steeds geprofileerd. 

| Functietype | Gegevenstype | Voorwaarde | Beperkingen | 
| ------------ | --------- | --------- | ----------- |
| Categorische gegevens | string, bool, int, float | Het aantal unieke waarden in de functie is minder dan 100 en minder dan 5% van het aantal rijen. | Null wordt behandeld als een eigen categorie. | 
| Numerieke | int, float | De waarden in de functie zijn van een numeriek gegevenstype en voldoen niet aan de voorwaarde voor een categorische functie. | Functie geschrapt als >15% van de waarden niet ig zijn. | 

## <a name="next-steps"></a>Volgende stappen

* Ga naar de [Azure Machine Learning-studio](https://ml.azure.com) of het [Python-notitieblok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datadrift-tutorial/datadrift-tutorial.ipynb) om een gegevenssetmonitor in te stellen.
* Bekijk hoe u gegevensdrift instelt op [modellen die zijn geïmplementeerd voor Azure Kubernetes Service.](how-to-monitor-data-drift.md)
* Gegevensafdrijftmonitors instellen met [gebeurtenisraster](how-to-use-event-grid.md). 
