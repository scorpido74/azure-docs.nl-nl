---
title: Fout opsporing & problemen met ML-pijp lijnen oplossen
titleSuffix: Azure Machine Learning
description: Fouten opsporen in uw Azure Machine Learning-pijp lijnen in python. Leer veelvoorkomende Valk uilen voor het ontwikkelen van pijp lijnen en tips voor het oplossen van fouten in scripts voor en tijdens externe uitvoering. Meer informatie over hoe u Visual Studio code kunt gebruiken om uw machine learning-pijp lijnen interactief op te sporen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: lobrien
ms.author: laobri
ms.date: 08/28/2020
ms.topic: conceptual
ms.custom: troubleshooting, devx-track-python
ms.openlocfilehash: 0f051e5b5711cec9fd8e72ec2b84c18f80430a0a
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018056"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Fouten in Machine Learning-pijplijnen opsporen en oplossen
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u problemen met [machine learning pijp lijnen](concept-ml-pipelines.md) oplost en fouten opspoort in de [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) en [Azure machine learning Designer (preview)](https://docs.microsoft.com/azure/machine-learning/concept-designer). 

## <a name="troubleshooting-tips"></a>Tips voor probleemoplossing

De volgende tabel bevat veelvoorkomende problemen bij het ontwikkelen van pijp lijnen met mogelijke oplossingen.

| Probleem | Mogelijke oplossing |
|--|--|
| Kan geen gegevens door geven aan de `PipelineData` map | Zorg ervoor dat u een map hebt gemaakt in het script dat overeenkomt met de locatie van de gegevens van de stap uitvoer. In de meeste gevallen definieert een invoer argument de uitvoermap en maakt u de map expliciet. Gebruiken `os.makedirs(args.output_dir, exist_ok=True)` om de uitvoermap te maken. Raadpleeg de [zelf studie](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) voor een score voorbeeld script waarin dit ontwerp patroon wordt weer gegeven. |
| Afhankelijkheids fouten | Als er afhankelijkheids fouten in uw externe pijp lijn worden weer gegeven die niet zijn uitgevoerd tijdens het lokaal testen, moet u controleren of de afhankelijkheden en versies van de externe omgeving overeenkomen met die in uw test omgeving. (Zie [omgeving bouwen, in cache plaatsen en opnieuw gebruiken](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse)|
| Dubbel zinnige fouten met Compute-doelen | Probeer de reken doelen te verwijderen en opnieuw te maken. Het opnieuw maken van Compute-doelen is snel en kan tijdelijke problemen oplossen. |
| Pijp lijn waarbij de stappen niet opnieuw worden gebruikt | Het opnieuw gebruiken van de stap is standaard ingeschakeld, maar zorg ervoor dat u deze niet hebt uitgeschakeld in een pijplijn stap. Als opnieuw gebruiken is uitgeschakeld, `allow_reuse` wordt de para meter in de stap ingesteld op `False` . |
| De pijp lijn wordt onnodig opnieuw uitgevoerd | Om ervoor te zorgen dat de stappen alleen worden uitgevoerd wanneer de onderliggende gegevens of scripts worden gewijzigd, moet u de bron code mappen voor elke stap loskoppelen. Als u dezelfde bron directory voor meerdere stappen gebruikt, kan het nodig zijn om niet-uitgevoerde opnieuw uit te voeren. Gebruik de `source_directory` para meter voor een pijplijn stap object om naar uw geïsoleerde map voor die stap te verwijzen en zorg ervoor dat u niet hetzelfde `source_directory` pad gebruikt voor meerdere stappen. |


## <a name="debugging-techniques"></a>Technieken voor fout opsporing

Er zijn drie belang rijke technieken voor het opsporen van fouten in pijp lijnen: 

* Fout opsporing voor afzonderlijke pijplijn stappen op uw lokale computer
* Logboek registratie en Application Insights gebruiken om de oorzaak van het probleem te isoleren en vast te stellen
* Een externe debugger koppelen aan een pijp lijn die wordt uitgevoerd in azure

### <a name="debug-scripts-locally"></a>Scripts lokaal fouten opsporen

Een van de meest voorkomende storingen in een pijp lijn is dat het domein script niet wordt uitgevoerd zoals bedoeld, of dat er runtime-fouten in de externe Compute-context staan die lastig zijn om fouten op te sporen.

Pijp lijnen zelf kunnen niet lokaal worden uitgevoerd, maar als u de scripts in isolatie uitvoert op uw lokale computer, kunt u sneller fouten opsporen omdat u niet hoeft te wachten op het proces voor het bouwen van de berekening en de omgeving. Er is een aantal ontwikkel werkzaamheden vereist om dit te doen:

* Als uw gegevens zich in een Cloud-Data Store bevinden, moet u gegevens downloaden en deze beschikbaar maken voor uw script. Het gebruik van een klein voor beeld van uw gegevens is een goede manier om in runtime te verminderen en snel feedback te krijgen over het gedrag van scripts
* Als u probeert een tussenliggende pijplijn stap te simuleren, moet u mogelijk hand matig de object typen bouwen waarvan het specifieke script in de vorige stap verwacht.
* U moet ook uw eigen omgeving definiëren en de afhankelijkheden repliceren die zijn gedefinieerd in uw externe Compute-omgeving

Zodra u een script hebt ingesteld om uit te voeren op uw lokale omgeving, is het veel eenvoudiger om fouten op te sporen in taken zoals:

* Een aangepaste configuratie voor fout opsporing koppelen
* Uitvoering onderbreken en object status controleren
* Het opvangen van type of logische fouten die pas worden weer gegeven na runtime

> [!TIP] 
> Zodra u kunt controleren of uw script wordt uitgevoerd zoals verwacht, voert u een goede volgende stap uit om het script uit te voeren in een pijp lijn met één stap voordat u deze in een pijp lijn met meerdere stappen probeert uit te voeren.

## <a name="configure-write-to-and-review-pipeline-logs"></a>Pijplijn logboeken configureren, schrijven naar en bekijken

Het lokaal testen van scripts is een uitstekende manier om fouten op te sporen in primaire code fragmenten en complexe logica voordat u begint met het bouwen van een pijp lijn, maar op een bepaald moment moet u waarschijnlijk fouten opsporen in scripts tijdens het uitvoeren van de werkelijke pijp lijn zelf, met name bij het vaststellen van het gedrag dat optreedt tijdens de interactie tussen de stappen van de pijp lijn. We raden u aan om `print()` de instructies in uw stap scripts te gebruiken, zodat u de object status en verwachte waarden tijdens externe uitvoering kunt zien, vergelijkbaar met hoe u fouten opspoort in Java script-code.

### <a name="logging-options-and-behavior"></a>Opties voor logboek registratie en gedrag

De onderstaande tabel bevat informatie over verschillende opties voor fout opsporing voor pijp lijnen. Het is geen limitatieve lijst, omdat er andere opties zijn dan alleen de Azure Machine Learning, python en opentellingen die hier worden weer gegeven.

| Bibliotheek                    | Type   | Voorbeeld                                                          | Doel                                  | Resources                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Machine Learning-SDK | Gegevens | `run.log(name, val)`                                             | Gebruikers interface van Azure Machine Learning Portal             | [Experimenten bijhouden](how-to-track-experiments.md)<br>[de klasse azureml. core. run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=experimental)                                                                                                                                                 |
| Python afdrukken/logboek registratie    | Log    | `print(val)`<br>`logging.info(message)`                          | Logboeken van Stuur Programma's, Azure Machine Learning Designer | [Experimenten bijhouden](how-to-track-experiments.md)<br><br>[Python-logboek registratie](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| OpenCensus Python          | Log    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Application Insights-traceringen                | [Fouten met pijplijnen opsporen in Application Insights](how-to-debug-pipelines-application-insights.md)<br><br>[OpenCensus Azure Monitor Exporters](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[Python-logboek registratie Cookbook](https://docs.python.org/3/howto/logging-cookbook.html) |

#### <a name="logging-options-example"></a>Voor beeld van logboek registratie opties

```python
import logging

from azureml.core.run import Run
from opencensus.ext.azure.log_exporter import AzureLogHandler

run = Run.get_context()

# Azure ML Scalar value logging
run.log("scalar_value", 0.95)

# Python print statement
print("I am a python print statement, I will be sent to the driver logs.")

# Initialize python logger
logger = logging.getLogger(__name__)
logger.setLevel(args.log_level)

# Plain python logging statements
logger.debug("I am a plain debug statement, I will be sent to the driver logs.")
logger.info("I am a plain info statement, I will be sent to the driver logs.")

handler = AzureLogHandler(connection_string='<connection string>')
logger.addHandler(handler)

# Python logging with OpenCensus AzureLogHandler
logger.warning("I am an OpenCensus warning statement, find me in Application Insights!")
logger.error("I am an OpenCensus error statement with custom dimensions", {'step_id': run.id})
``` 

### <a name="finding-and-reading-pipeline-log-files"></a>Logboek bestanden voor de pijp lijn zoeken en lezen

Het logboek bestand `70_driver_log.txt` bevat: 

* Alle afgedrukte instructies tijdens de uitvoering van uw script
* De stack-trace voor het script 

Als u deze en andere logboek bestanden wilt vinden in de portal, klikt u eerst op de pijplijn uitvoering in uw werk ruimte.

![Lijst pagina pijplijn uitvoering](./media/how-to-debug-pipelines/pipelinerun-01.png)

Ga naar de detail pagina van de pijplijn uitvoering.

![Detail pagina pijp lijn uitvoeren](./media/how-to-debug-pipelines/pipelinerun-02.png)

Klik op de module voor de specifieke stap. Ga naar het tabblad **Logboeken** . Andere logboeken bevatten informatie over het proces van het bouwen van de omgevings installatie kopie en stap voorbereidings scripts.

![Tabblad detail pagina voor uitvoering van pijp lijn](./media/how-to-debug-pipelines/pipelinerun-03.png)

> [!TIP]
> Wordt uitgevoerd voor *gepubliceerde pijp lijnen* vindt u op het tabblad **eind punten** in uw werk ruimte. Wordt uitgevoerd voor *niet-gepubliceerde pijp lijnen* vindt u in **experimenten** of **pijp lijnen**.

`ParallelRunStep`Zie [debug en Troubleshoot ParallelRunStep](how-to-debug-parallel-run-step.md)voor meer informatie over logboek registratie en tracering vanuit een.

## <a name="logging-in-azure-machine-learning-designer-preview"></a>Aanmelden Azure Machine Learning Designer (preview-versie)

Voor pijp lijnen die in de ontwerp functie zijn gemaakt, kunt u het **70_driver_log** bestand vinden op de pagina ontwerpen of op de detail pagina van de pijplijn uitvoering.

### <a name="enable-logging-for-real-time-endpoints"></a>Logboek registratie inschakelen voor realtime-eind punten

Voor het oplossen van problemen met realtime-eind punten in de ontwerp functie moet u logboek registratie van toepassingen inschakelen met behulp van de SDK. Met logboek registratie kunt u problemen met model implementatie en-gebruik oplossen. Zie [logboek registratie voor geïmplementeerde modellen](how-to-enable-logging.md#logging-for-deployed-models)voor meer informatie. 

### <a name="get-logs-from-the-authoring-page"></a>Logboeken ophalen via de pagina ontwerpen

Wanneer u een pijplijn uitvoering verzendt en op de bewerkings pagina blijft, kunt u de logboek bestanden vinden die voor elke module worden gegenereerd, wanneer elke module is voltooid.

1. Selecteer een module die op het ontwerp canvas is uitgevoerd.
1. Ga in het rechterdeel venster van de module naar het tabblad  **uitvoer en logboeken** .
1. Vouw het rechterdeel venster uit en selecteer het **70_driver_log.txt** om het bestand in de browser weer te geven. U kunt Logboeken ook lokaal downloaden.

    ![Uitgevouwen uitvoer deel venster in de ontwerp functie](./media/how-to-debug-pipelines/designer-logs.png)

### <a name="get-logs-from-pipeline-runs"></a>Logboeken ophalen van pijplijn uitvoeringen

U kunt ook de logboek bestanden voor specifieke uitvoeringen vinden op de detail pagina pijp lijn uitvoeren, die u kunt vinden in de sectie **pijp lijnen** of **experimenten** van de Studio.

1. Selecteer een pijplijn uitvoering die in de ontwerp functie is gemaakt.

    ![Pagina pijplijn uitvoering](./media/how-to-debug-pipelines/designer-pipelines.png)

1. Selecteer een module in het voorbeeld venster.
1. Ga in het rechterdeel venster van de module naar het tabblad  **uitvoer en logboeken** .
1. Vouw het rechterdeel venster uit om het **70_driver_log.txt** -bestand in de browser weer te geven of selecteer het bestand om de logboeken lokaal te downloaden.

> [!IMPORTANT]
> Als u een pijp lijn wilt bijwerken op de pagina Details van de pijplijn uitvoering, moet u de pijplijn uitvoering **klonen** naar een nieuwe pijp lijn concept. Een pijplijn uitvoering is een moment opname van de pijp lijn. Het is vergelijkbaar met een logboek bestand en kan niet worden gewijzigd. 

## <a name="application-insights"></a>Application Insights
Voor meer informatie over het gebruik van de bibliotheek opentellingen python op deze manier raadpleegt u deze hand leiding: [fouten opsporen en problemen oplossen met machine learning pijp lijnen in Application Insights](how-to-debug-pipelines-application-insights.md)

## <a name="interactive-debugging-with-visual-studio-code"></a>Interactieve fout opsporing met Visual Studio code

In sommige gevallen moet u mogelijk interactief fouten opsporen in de python-code die wordt gebruikt in uw ML-pijp lijn. Door Visual Studio code (VS code) en debugpy te gebruiken, kunt u deze koppelen aan de code zoals deze wordt uitgevoerd in de trainings omgeving. Ga voor meer informatie naar de [richt lijnen voor interactieve fout opsporing in VS code](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines).

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg de SDK-Naslag informatie voor hulp met het kern pakket voor [azureml-pipelines-core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) en het pakket met [azureml-pijp lijnen-stappen](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) .

* Bekijk de lijst met [uitzonde ringen en fout codes voor Designer](algorithm-module-reference/designer-error-codes.md).
