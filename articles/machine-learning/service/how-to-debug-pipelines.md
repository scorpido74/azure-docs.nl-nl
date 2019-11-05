---
title: Fouten opsporen en problemen oplossen machine learning pijp lijnen
titleSuffix: Azure Machine Learning
description: Fouten opsporen en problemen oplossen machine learning pijp lijnen in de Azure Machine Learning SDK voor python. Leer veelvoorkomende Valk uilen voor het ontwikkelen van pijp lijnen en tips voor het oplossen van fouten in scripts voor en tijdens externe uitvoering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye
ms.author: trbye
author: trevorbye
ms.date: 10/03/2019
ms.openlocfilehash: fc19e864f00489d3ebc0162705af864785af0811
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497069"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Fouten opsporen en problemen oplossen machine learning pijp lijnen
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u fouten opspoort en oplost [machine learning pijp lijnen](concept-ml-pipelines.md) in de [Azure machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

In de volgende secties vindt u een overzicht van algemene Valk uilen bij het bouwen van pijp lijnen en verschillende strategieën voor het opsporen van fouten in uw code die wordt uitgevoerd in een pijp lijn. Gebruik de volgende tips wanneer u problemen ondervindt bij het verkrijgen van een pijp lijn die naar verwachting kan worden uitgevoerd. 

## <a name="testing-scripts-locally"></a>Scripts lokaal testen

Een van de meest voorkomende fouten in een pijp lijn is dat een bijgevoegd script (script voor het opschonen van gegevens, het Score script, enzovoort) niet wordt uitgevoerd zoals bedoeld, of dat er runtime-fouten in de externe Compute-context staan die moeilijk te vinden zijn in uw werk ruimte in de Azure-machine Learning Studio. 

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

## <a name="debugging-scripts-from-remote-context"></a>Fouten opsporen in scripts in externe context

Het lokaal testen van scripts is een uitstekende manier om fouten op te sporen in primaire code fragmenten en complexe logica voordat u begint met het bouwen van een pijp lijn, maar op een bepaald moment moet u waarschijnlijk fouten opsporen in scripts tijdens de daad werkelijke uitvoering van de pijp lijn, met name bij het vaststellen van het gedrag dat optreedt tijdens de interactie tussen pijplijn stappen. We raden u aan om `print()`-instructies in uw stap scripts kwijt te gebruiken, zodat u de object status en verwachte waarden kunt zien tijdens externe uitvoering, vergelijkbaar met hoe u fouten opspoort in Java script-code.

Het logboek bestand `70_driver_log.txt` bevat: 

* Alle afgedrukte instructies tijdens de uitvoering van uw script
* De stack-trace voor het script 

Als u deze en andere logboek bestanden wilt vinden in de portal, klikt u eerst op de pijp lijn in uw werk ruimte.

![Pagina pijp lijnen in Portal](./media/how-to-debug-pipelines/pipeline-1.png)

Navigeer naar de bovenliggende uitvoering van de pijp lijn.

![Bovenliggende pijp lijnen voor uitvoering](./media/how-to-debug-pipelines/pipeline-2.png)

Klik op de uitvoeringsrun-ID voor de specifieke stap.

![Pagina pijp lijnen in Portal](./media/how-to-debug-pipelines/pipeline-3.png)

Ga naar het tabblad **Logboeken** . Andere logboeken bevatten informatie over het proces van het bouwen van de omgevings installatie kopie en stap voorbereidings scripts.

![Pagina pijp lijnen in Portal](./media/how-to-debug-pipelines/pipeline-4.png)

> [!TIP]
> Wordt uitgevoerd voor *gepubliceerde pijp lijnen* vindt u op het tabblad **pijp lijnen** in uw werk ruimte in de portal. Wordt uitgevoerd voor *niet-gepubliceerde pijp lijnen* vindt u in **experimenten**.

## <a name="troubleshooting-tips"></a>Tips voor probleemoplossing

De volgende tabel bevat veelvoorkomende problemen bij het ontwikkelen van pijp lijnen met mogelijke oplossingen.

| Probleem | Mogelijke oplossing |
|--|--|
| Kan geen gegevens door geven aan `PipelineData` Directory | Zorg ervoor dat u een map hebt gemaakt in het script dat overeenkomt met de locatie van de gegevens van de stap uitvoer. In de meeste gevallen definieert een invoer argument de uitvoermap en maakt u de map expliciet. Gebruik `os.makedirs(args.output_dir, exist_ok=True)` om de uitvoermap te maken. Raadpleeg de [zelf studie](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) voor een score voorbeeld script waarin dit ontwerp patroon wordt weer gegeven. |
| Afhankelijkheids fouten | Als u scripts lokaal hebt ontwikkeld en getest, maar u afhankelijkheids problemen ondervindt bij het uitvoeren van een externe Compute in de pijp lijn, moet u ervoor zorgen dat afhankelijkheden en versies van de reken omgeving overeenkomen met uw test omgeving. |
| Dubbel zinnige fouten met Compute-doelen | Het verwijderen en opnieuw maken van Compute-doelen kan bepaalde problemen met reken doelen oplossen. |
| Pijp lijn waarbij de stappen niet opnieuw worden gebruikt | Het opnieuw gebruiken van de stap is standaard ingeschakeld, maar zorg ervoor dat u deze niet hebt uitgeschakeld in een pijplijn stap. Als opnieuw gebruiken is uitgeschakeld, wordt de para meter `allow_reuse` in de stap ingesteld op `False`. |
| De pijp lijn wordt onnodig opnieuw uitgevoerd | Om ervoor te zorgen dat de stappen alleen opnieuw worden uitgevoerd wanneer de onderliggende gegevens of scripts worden gewijzigd, moet u de mappen voor elke stap loskoppelen. Als u dezelfde bron directory voor meerdere stappen gebruikt, kan het nodig zijn om niet-uitgevoerde opnieuw uit te voeren. Gebruik de para meter `source_directory` voor een pijplijn stap object om naar uw geïsoleerde map voor die stap te verwijzen en zorg ervoor dat u niet hetzelfde `source_directory` pad gebruikt voor meerdere stappen. |

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg de SDK-Naslag informatie voor hulp met het kern pakket voor [azureml-pipelines-core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) en het pakket met [azureml-pijp lijnen-stappen](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) .

* Volg de [Geavanceerde zelf studie](tutorial-pipeline-batch-scoring-classification.md) over het gebruik van pijp lijnen voor batch scores.
