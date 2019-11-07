---
title: Analyseren en controleren op gegevens drift op data sets (preview-versie)
titleSuffix: Azure Machine Learning
description: Maak Azure Machine Learning gegevens sets-monitors (preview), Controleer of het data kan worden geschaald en stel waarschuwingen in.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/04/2019
ms.openlocfilehash: 6fa7ee6663aae24451af195de4a8225c7a6b351e
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647151"
---
# <a name="detect-data-drift-preview-on-datasets"></a>Gegevens drift (preview) detecteren in gegevens sets
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u Azure Machine Learning gegevensset-monitors (preview) maakt, monitort voor gegevens drift en statistische wijzigingen in de gegevens sets en het instellen van waarschuwingen.

Met Azure Machine Learning gegevensset monitor kunt u het volgende doen:
* **Analyseer de drift in uw gegevens** om inzicht te krijgen in de manier waarop deze in de loop van de tijd verandert.
* **Model gegevens bewaken** voor verschillen tussen trainingen en gegevens sets.
* **Bewaak nieuwe gegevens** voor verschillen tussen de basis lijn en de doel-DataSet.
* **Profiel functies in gegevens** om bij te houden hoe statistische eigenschappen na verloop van tijd veranderen.
* **Stel waarschuwingen in voor de gegevens drift** voor vroegtijdige waarschuwingen voor potentiële problemen. 

Metrische gegevens en inzichten zijn beschikbaar via de [Azure-toepassing Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) -resource die aan de Azure machine learning service-werk ruimte is gekoppeld.

> [!Important]
> Houd er rekening mee dat het bewaken van gegevens drift met de SDK beschikbaar is in alle edities, terwijl het bewaken van gegevens drift via de studio op internet alleen Enter prise Edition is.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om de monitors voor de gegevensset te maken en te gebruiken:
* Een Azure-abonnement. Als u nog geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree) .
* Een [Azure machine learning-werk ruimte](how-to-manage-workspace.md).
* De [Azure machine learning SDK voor python is geïnstalleerd](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), waaronder het pakket met de azureml-gegevens sets.
* Gestructureerde (tabellaire) gegevens met een tijds tempel die is opgegeven in het bestandspad, de bestands naam of de kolom in de gegevens.

## <a name="what-is-data-drift"></a>Wat is gegevens drift?

In de context van machine learning is gegevens drift de wijziging in model invoer gegevens die leidt tot het model leren van prestaties. Het is een van de belangrijkste redenen om de nauw keurigheid van het model te verslechteren, waardoor gegevens drift helpt bij het detecteren van prestatie problemen van modellen.

Oorzaken van gegevens drift zijn onder andere: 

- Proces wijzigingen in de upstream, zoals een sensor die wordt vervangen, waarbij de maat eenheden worden gewijzigd van inches in centimeters. 
- Problemen met gegevens kwaliteit, zoals een gebroken sensor, worden altijd 0 gelezen.
- Natuurlijke drift in de gegevens, zoals gemiddelde temperatuur wijziging, met de seizoenen.
- Wijziging ten opzichte van functies of covariate SHIFT. 

Met Azure Machine Learning-gegevensset monitors kunt u waarschuwingen instellen die in gegevens sets in de loop der tijd helpen bij het detecteren van gegevensdrift. 

### <a name="dataset-monitors"></a>Monitor voor gegevensset 

U kunt een gegevensset-monitor maken om gegevens drift op nieuwe gegevens in een gegevensset te detecteren en te waarschuwen, historische gegevens voor drift te analyseren en nieuwe gegevens gedurende een bepaalde periode te bekijken. Het algoritme voor gegevens drift biedt een algemene meting van de gegevens wijziging en geeft aan welke functies verantwoordelijk zijn voor nader onderzoek. De monitors van de gegevensset geven een aantal andere metrische gegevens aan door het profileren van nieuwe data in de `timeseries`-gegevensset. Aangepaste waarschuwingen kunnen worden ingesteld op alle metrische gegevens die door de monitor worden gegenereerd via [Azure-toepassing Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). U kunt de monitors van de gegevensset gebruiken om snel gegevens problemen te ondervangen en de tijd te beperken om het probleem op te lossen door waarschijnlijke oorzaken te identificeren.  

In het algemeen zijn er drie primaire scenario's voor het instellen van gegevensset-monitors in Azure Machine Learning.

Scenario | Beschrijving
---|---
Bewaken van de gegevens van een model voor drift van de trainings gegevens van het model | De resultaten van dit scenario kunnen worden geïnterpreteerd als bewaking van een proxy voor de nauw keurigheid van het model, op voor waarde dat de model nauwkeurigheid afnemen als het leveren van gegevens verhoudingen van de trainings gegevens.
Een time series-gegevensset voor drift bewaken vanuit een vorige tijds periode. | Dit scenario is algemener en kan worden gebruikt voor het bewaken van gegevens sets die betrekking hebben op de upstream of het maken van een model.  De doel gegevensset moet een time stamp-kolom hebben, terwijl de gegevensset van de basis lijn een wille keurige gegevensset kan zijn met functies die gemeen schappelijk zijn met de doel-DataSet.
Analyse uitvoeren op gegevens in het verleden. | Dit kan worden gebruikt om historische gegevens te begrijpen en beslissingen op te nemen in instellingen voor de monitors van de gegevensset.

## <a name="how-dataset-can-monitor-data"></a>Hoe gegevensset gegevens kan bewaken

Met behulp van Azure Machine Learning wordt de gegevens drift bewaakt via data sets. Als u wilt controleren op gegevens drift, een basislijn gegevensset, meestal de trainings gegevensset voor een model, is opgegeven. Een doel gegevensset-doorgaans model invoer gegevens-wordt in de loop van de tijd vergeleken met de gegevensset van de basis lijn. Dit betekent dat er een time stamp-kolom moet worden opgegeven voor de doel-DataSet.

### <a name="set-the-timeseries-trait-in-the-target-dataset"></a>De kenmerken van de `timeseries` in de doel-dataset instellen

Voor de doel-DataSet moet de `timeseries` Trait worden ingesteld door de time stamp-kolom op te geven van een kolom in de gegevens of een virtuele kolom die is afgeleid van het pad patroon van de bestanden. Dit kan worden gedaan met behulp van de python-SDK of Azure Machine Learning Studio. Een kolom met een ' fijne korrel ' tijds tempel moet worden opgegeven om `timeseries` eigenschappen toe te voegen aan de gegevensset. Als uw gegevens zijn gepartitioneerd in mapstructuur met tijd gegevens, zoals {JJJJ/MM/DD}, kunt u een virtuele kolom maken met de patroon instelling pad en instellen als de tijds tempel "grof korrels" om het belang van de tijd reeks functionaliteit te verbeteren. 

#### <a name="python-sdk"></a>Python SDK

De methode [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-) van de klasse [`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-) definieert de time stamp-kolom voor de gegevensset. 

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

Voor een volledig voor beeld van het gebruik van de `timeseries` eigenschappen van gegevens sets raadpleegt u het [voor beeld-notebook](https://aka.ms/azureml-tsd-notebook) of de [SDK-documentatie voor gegevens sets](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-).

#### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku-inline.md)]

Als u uw gegevensset maakt met behulp van Azure Machine Learning Studio, zorgt u ervoor dat het pad naar uw gegevens tijds tempel informatie bevat, alle submappen met gegevens bevatten en de partitie-indeling instellen. 

In het volgende voor beeld worden alle gegevens in de submap *NoaaIsdFlorida/2019* gemaakt en de partitie-indeling geeft het jaar, de maand en de dag van het tijds tempel. 

[Partitie-indeling ![](media/how-to-monitor-datasets/partition-format.png)](media/how-to-monitor-datasets/partition-format-expand.png)

Geef in de **schema** -instellingen de time stamp-kolom op uit een virtuele of Real-kolom in de opgegeven gegevensset:

![Tijdstempel](media/how-to-monitor-datasets/timestamp.png)

## <a name="dataset-monitor-settings"></a>Monitor instellingen voor de gegevensset

Wanneer u uw gegevensset met de opgegeven tijds tempel instellingen hebt gemaakt, kunt u uw gegevensset-monitor configureren.

De verschillende instellingen voor de controle van de gegevensset zijn onderverdeeld in drie groepen: **basis informatie, controle-instellingen** en **backfill-instellingen**.

### <a name="basic-info"></a>Basis informatie

Deze tabel bevat de basis instellingen die worden gebruikt voor de monitor van de gegevensset.

| Instelling | Beschrijving | Tips | Veranderlijk | 
| ------- | ----------- | ---- | ------- | 
| Naam | De naam van de monitor van de gegevensset. | | Nee |
| Gegevensset voor basis lijn | Tabellaire gegevensset die wordt gebruikt als de basis lijn voor de vergelijking van de doel gegevensset gedurende een bepaalde periode. | De gegevensset van de basis lijn moet algemene kenmerken hebben met de doel-DataSet. Over het algemeen moet de basis lijn worden ingesteld op de trainings gegevensset van een model of een segment van de doel gegevensset. | Nee |
| Doel gegevensset | Tabellaire gegevensset met de time stamp-kolom die wordt geanalyseerd voor gegevens drift | De doel gegevensset moet algemene kenmerken hebben met de gegevensset van de basis lijn en moet een `timeseries`-gegevensset zijn waaraan nieuwe gegevens worden toegevoegd. Historische gegevens in de doel-gegevensset kunnen worden geanalyseerd of nieuwe gegevens kunnen worden bewaakt. | Nee | 
| Frequency | Dit is de frequentie die wordt gebruikt om de pijplijn taak te plannen en historische gegevens te analyseren als er een backfill wordt uitgevoerd. Opties zijn dagelijks, wekelijks of maandelijks. | Stel deze instelling zo in dat een vergelijk bare grootte van de gegevens wordt toegevoegd aan de basis lijn. | Nee | 
| Functies | Lijst met functies die gedurende een bepaalde periode worden geanalyseerd voor gegevens drift | Ingesteld op de uitvoer functie (s) van een model om concept drift te meten. Neem geen functies op die in de loop van de tijd (maand, jaar, index enz.) natuurlijk worden overgenomen. U kunt backfill en een bestaande gegevensdrijf monitor na het aanpassen van de lijst met functies. | Ja | 
| Rekendoel | Azure Machine Learning Compute-doel voor het uitvoeren van de controle taken voor de gegevensset. | | Ja | 

### <a name="monitor-settings"></a>Monitor instellingen

Deze instellingen zijn voor de bewaakte pijp lijn van de geplande gegevensset die wordt gemaakt. 

| Instelling | Beschrijving | Tips | Veranderlijk | 
| ------- | ----------- | ---- | ------- |
| Inschakelen | Het schema in-of uitschakelen voor de monitor pijplijn van de gegevensset | Schakel deze optie uit om historische gegevens te analyseren met de instelling backfill. De functie kan worden ingeschakeld nadat de monitor voor de gegevensset is gemaakt. | Ja | 
| Latentie | Tijd, in uren, duurt het om gegevens in de gegevensset te ontvangen. Als het bijvoorbeeld drie dagen duurt voordat de gegevens in de gegevensset van de SQL-Data Base zijn Inge kapseld, stelt u de latentie in op 72. | Kan niet worden gewijzigd nadat de monitor voor de gegevensset is gemaakt | Nee | 
| E-mail adressen | E-mail adressen voor waarschuwingen op basis van schending van de drempel waarde voor het percentage gegevens drift. | E-mail berichten worden verzonden via Azure Monitor. | Ja | 
| Spreek | Drempel waarde voor het percentage gegevens drift voor e-mail waarschuwingen. | Verdere waarschuwingen en gebeurtenissen kunnen worden ingesteld op veel andere metrische gegevens in de gekoppelde Application Insights resource van de werk ruimte. | Ja | 

### <a name="backfill-settings"></a>Backfill-instellingen

Deze instellingen zijn voor het uitvoeren van een backfill in vroegere gegevens voor metrische gegevens over data waarde.

| Instelling | Beschrijving | Tips |
| ------- | ----------- | ---- |
| Begindatum | Begin datum van de taak backfill. | | 
| Eind datum | Eind datum van de taak backfill. | Dit mag niet meer zijn dan 31 * frequentie eenheden van de tijd vanaf de begin datum. In een bestaande gegevensset-monitor kunnen metrische gegevens worden alsnog om historische data te analyseren of metrische waarden te vervangen door bijgewerkte instellingen. |

## <a name="create-dataset-monitors"></a>Gegevensset-monitors maken 

Maak gegevensset-monitors om gegevens drift te detecteren en te waarschuwen voor een nieuwe gegevensset met Azure Machine Learning Studio of de python-SDK. 

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku-inline.md)]

Als u waarschuwingen wilt instellen op uw gegevenssetcontrole, moet de werk ruimte die de gegevensset bevat waarvoor u een monitor wilt maken, beschikken over de mogelijkheden van ENTER prise Edition. 

Nadat de werkruimte functionaliteit is bevestigd, gaat u naar de start pagina van de studio en selecteert u het tabblad gegevens sets aan de linkerkant. Selecteer de monitors voor de gegevensset.

![Monitor lijst](media/how-to-monitor-datasets/monitor-list.png)

Klik op de knop **monitor maken** en ga door met de wizard door op **volgende**te klikken.

![Wizard](media/how-to-monitor-datasets/wizard.png)

De resulterende gegevensset-monitor wordt weer gegeven in de lijst. Selecteer deze optie om naar de pagina met details van de monitor te gaan.

### <a name="from-python-sdk"></a>Van python-SDK

Raadpleeg de [documentatie van PYTHON SDK voor meer informatie over gegevens drift](https://aka.ms/datadriftapi) . 

Hier volgt een voor beeld van het maken van een DataSet-monitor met behulp van de python-SDK

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

Zie ons [voor beeld](https://aka.ms/datadrift-notebook)van een notebook voor een volledig voor beeld van het instellen van een `timeseries` gegevensset en gegevens drift detectie.

## <a name="understanding-data-drift-results"></a>Informatie over de resultaten van gegevens drift

De gegevens monitor produceert twee groepen resultaten: overzicht van drift en Details van de functie. In de volgende animatie ziet u de beschik bare drift-monitor diagrammen op basis van de geselecteerde functie en metrische gegevens. 

![Demovideo](media/how-to-monitor-datasets/video.gif)

### <a name="drift-overview"></a>Overzicht van drift

De sectie **overzicht van drift** bevat inzichten op het hoogste niveau in de grootte van de gegevens drift en de functies die verder moeten worden onderzocht. 

| Gegevens | Beschrijving | Tips | 
| ------ | ----------- | ---- | 
| Grootte van gegevens drift | Gegeven als een percentage tussen de basis lijn en de doel gegevensset gedurende een bepaalde periode. Variërend van 0 tot 100 waarbij 0 overeenkomt met identieke gegevens sets en 100 geeft aan dat de Azure Machine Learning data drift de twee gegevens sets volledig kan onderscheiden. | Er wordt een nauw keuriger percentage gemeten als gevolg van machine learning technieken die worden gebruikt voor het genereren van deze omvang. | 
| Drift-bijdrage per functie | De bijdrage van elke functie in de doel gegevensset naar de gemeten grootte van de drift. |  Vanwege covariate-verschuiving hoeft de onderliggende verdeling van een functie niet noodzakelijkerwijs te worden aangepast aan een relatief hoog belang rijk onderdeel. | 

De volgende afbeelding is een voor beeld van grafieken die worden weer gegeven in de **overzichts** resultaten van de Drift in azure machine learning Studio, wat resulteert uit een backfill van [NOAA geïntegreerde Surface-gegevens](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/). Er zijn gegevens naar `stationName contains 'FLORIDA'`gesampled, waarbij januari 2019 wordt gebruikt als de basislijn gegevensset en alle 2019-gegevens die als doel worden gebruikt.
 
![Overzicht van drift](media/how-to-monitor-datasets/drift-overview.png)

### <a name="feature-details"></a>Details van functie

De sectie **Details van functie** bevat inzichten op functie niveau in de wijziging in de distributie van de geselecteerde functie, evenals andere statistieken, na verloop van tijd. 

De doel gegevensset is ook in de loop van de tijd profileren. De statistische afstand tussen de basislijn distributie van elke functie wordt vergeleken met de tijds duur van de doel gegevensset, die conceptueel overeenkomt met de grootte van de gegevens drift, met uitzonde ring van de waarde voor een afzonderlijke functie. Min, Max en gemiddelde zijn ook beschikbaar. 

Als u in de Azure Machine Learning Studio op een gegevens punt in de grafiek klikt, wordt de verdeling van de weer gegeven functie dienovereenkomstig aangepast. Standaard worden de distributie van de basislijn gegevensset en de distributie van de meest recente uitvoering van hetzelfde onderdeel weer gegeven. 

Deze metrische gegevens kunnen ook worden opgehaald in de python-SDK via de methode `get_metrics()` op een `DataDriftDetector`-object. 

#### <a name="numeric-features"></a>Numerieke functies 

Numerieke functies worden in elke controle van de gegevensset profileeerd. Het volgende wordt weer gegeven in de Azure Machine Learning Studio. De kansdichtheids wordt weer gegeven voor de distributie.

| Gegevens | Beschrijving |  
| ------ | ----------- |  
| Wasserstein afstand | Minimale hoeveelheid werk voor het transformeren van basislijn distributie in de doel verdeling. |
| Gemiddelde waarde | De gemiddelde waarde van de functie. |
| Minimum waarde | De minimum waarde van de functie. |
| Maximum waarde | De maximum waarde van de functie. |

![Numerieke functie gegevens](media/how-to-monitor-datasets/feature-details.png)

#### <a name="categorical-features"></a>Categorische-functies 

Numerieke functies worden in elke controle van de gegevensset profileeerd. Het volgende wordt weer gegeven in de Azure Machine Learning Studio. Er wordt een histogram weer gegeven voor de distributie.

| Gegevens | Beschrijving |  
| ------ | ----------- |  
| Euclidian afstand | Geometrische afstand tussen basis lijn-en doel distributies. |
| Unieke waarden | Het aantal unieke waarden (kardinaliteit) van de functie. |


![Details van functie categorische](media/how-to-monitor-datasets/feature-details2.png)

## <a name="metrics-alerts-and-events"></a>Metrische gegevens, waarschuwingen en gebeurtenissen

U kunt de metrische gegevens opvragen in de [Azure-toepassing Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) -resource die aan uw machine learning-werk ruimte is gekoppeld. Hiermee krijgt u toegang tot alle functies van Application Insights, met inbegrip van aangepaste waarschuwings regels en actie groepen voor het activeren van een actie, zoals een E-mail/SMS/push/Voice-of Azure-functie. Raadpleeg de volledige Application Insights-documentatie voor meer informatie. 

Om aan de slag te gaan, gaat u naar de Azure Portal en selecteert u de **overzichts** pagina van uw werk ruimte.  De gekoppelde Application Insights resource bevindt zich uiterst rechts:

[overzicht van ![Azure Portal](media/how-to-monitor-datasets/ap-overview.png)](media/how-to-monitor-datasets/ap-overview-expanded.png)

Selecteer Logboeken (analyse) onder bewaking in het linkerdeel venster:

![Overzicht van Application Insights](media/how-to-monitor-datasets/ai-overview.png)

De metrische gegevens van de gegevensset-monitor worden opgeslagen als `customMetrics`. U kunt een eenvoudige query schrijven en uitvoeren na het instellen van een monitor voor het weer geven van een gegevensset:

[![log Analytics-query](media/how-to-monitor-datasets/simple-query.png)](media/how-to-monitor-datasets/simple-query-expanded.png)

Nadat u metrische gegevens hebt geïdentificeerd om waarschuwings regels in te stellen, maakt u een nieuwe waarschuwings regel:

![Nieuwe waarschuwings regel](media/how-to-monitor-datasets/alert-rule.png)

U kunt een bestaande actie groep gebruiken of een nieuwe maken om de actie te definiëren die moet worden uitgevoerd wanneer aan de voor waarden van de set wordt voldaan:

![Nieuwe actie groep](media/how-to-monitor-datasets/action-group.png)

## <a name="troubleshooting"></a>Problemen oplossen

Beperkingen en bekende problemen:

* Het tijds bereik van backfill-taken is beperkt tot 31 intervallen van de frequentie-instelling van de monitor. 
* Beperking van 200-functies, tenzij er geen functie lijst is opgegeven (alle gebruikte functies).
* De reken grootte moet groot genoeg zijn om de gegevens te kunnen verwerken. 
* Zorg ervoor dat uw gegevensset gegevens bevat binnen de begin-en eind datum voor een bepaalde monitor uitvoering.

Kolommen, of functies, worden in de gegevensset geclassificeerd als categorische of numeriek op basis van de voor waarden in de volgende tabel. Als de functie niet aan deze voor waarden voldoet, bijvoorbeeld een kolom van het type teken reeks met > 100 unieke waarden, wordt de functie verwijderd uit onze data drift-algoritme, maar is nog steeds profileeerd. 

| Onderdeel type | Gegevenstype | Voorwaarde | Beperkingen | 
| ------------ | --------- | --------- | ----------- |
| Categorische gegevens | teken reeks, BOOL, int, float | Het aantal unieke waarden in de functie is kleiner dan 100 en minder dan 5% van het aantal rijen. | NULL wordt beschouwd als een eigen categorie. | 
| Cijfer | int, float | Van een numeriek gegevens type en voldoet niet aan de voor waarden voor een categorische-functie. | De functie is verwijderd als > 15% van de waarden null zijn. | 

## <a name="next-steps"></a>Volgende stappen

* Kop naar de [Azure machine learning Studio](https://ml.azure.com) of de [python-notebook](https://aka.ms/datadrift-notebook) voor het instellen van een monitor voor de gegevensset.
* Zie gegevens drift instellen voor [modellen die zijn geïmplementeerd in azure Kubernetes service](how-to-monitor-data-drift.md).
* Stel de gegevensset-drijf monitors in met [Event grid](how-to-use-event-grid.md). 