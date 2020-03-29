---
title: Scenario's identificeren en het analyseproces plannen - Team Data Science Process | Azure Machine Learning
description: Identificeer scenario's en plan voor gegevensverwerking van geavanceerde analyses door een reeks belangrijke vragen te overwegen.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: b0b811a2b7ed432b7fc5015886b28337ca33424e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76710323"
---
# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>Scenario's en plannen identificeren voor geavanceerde analytische gegevensverwerking

Welke resources zijn nodig om een omgeving te maken die geavanceerde analyseverwerking op een gegevensset kan uitvoeren? In dit artikel worden een reeks vragen gesteld die kunnen helpen bij het identificeren van taken en bronnen die relevant zijn voor uw scenario.

Zie [Wat is het Team Data Science Process (TDSP)](overview.md)voor meer informatie over de volgorde van stappen op hoog niveau voor voorspellende analyses. Voor elke stap zijn specifieke resources nodig voor de taken die relevant zijn voor uw specifieke scenario.

Beantwoord belangrijke vragen in de volgende gebieden om uw scenario te identificeren:

* datalogistiek
* gegevenskenmerken
* gegevenssetkwaliteit
* voorkeurstools en talen

## <a name="logistic-questions-data-locations-and-movement"></a>Logistieke vragen: datalocaties en beweging

De logistieke vragen hebben betrekking op de volgende punten:

* locatie van gegevensbron
* doelbestemming in Azure
* vereisten voor het verplaatsen van de gegevens, inclusief de planning, het bedrag en de betrokken resources

Het kan nodig zijn om de gegevens meerdere keren te verplaatsen tijdens het analyseproces. Een veelvoorkomend scenario is om lokale gegevens te verplaatsen naar een of andere vorm van opslag op Azure en vervolgens naar Machine Learning Studio.

### <a name="what-is-your-data-source"></a>Wat is uw gegevensbron?

Zijn uw gegevens lokaal of in de cloud? Mogelijke locaties zijn:

* een openbaar HTTP-adres
* een lokale of netwerkbestandslocatie
* een SQL Server-database
* een Azure Storage-container

### <a name="what-is-the-azure-destination"></a>Wat is de Azure-bestemming?

Waar moeten uw gegevens zijn voor verwerking of modellering? 

* Azure Blob Storage
* SQL Azure-databases
* SQL Server op virtuele Azure-machine
* HDInsight (Hadoop op Azure) of Hive-tabellen
* Azure Machine Learning
* Monteerbare Azure virtuele harde schijven

### <a name="how-are-you-going-to-move-the-data"></a>Hoe ga je de gegevens verplaatsen?

Zie voor procedures en resources om gegevens in te nemen of te laden in verschillende opslag- en verwerkingsomgevingen:

* [Gegevens voor analysedoeleinden in opslagomgevingen laden](ingest-data.md)
* [Uw trainingsgegevens importeren in Azure Machine Learning Studio (klassiek) uit verschillende gegevensbronnen](../studio/import-data.md)

### <a name="does-the-data-need-to-be-moved-on-a-regular-schedule-or-modified-during-migration"></a>Moeten de gegevens regelmatig worden verplaatst of tijdens de migratie worden gewijzigd?

Overweeg Azure Data Factory (ADF) te gebruiken wanneer gegevens voortdurend moeten worden gemigreerd. ADF kan nuttig zijn voor:

* een hybride scenario waarbij zowel on-premises als cloudresources betrokken zijn
* een scenario waarin de gegevens worden afgehandeld, gewijzigd of gewijzigd door bedrijfslogica in de loop van de migratie

Zie [Gegevens verplaatsen van een on-premises SQL-server naar SQL Azure met Azure Data Factory](move-sql-azure-adf.md)voor meer informatie.

### <a name="how-much-of-the-data-is-to-be-moved-to-azure"></a>Hoeveel van de gegevens moeten worden verplaatst naar Azure?

Grote gegevenssets kunnen de opslagcapaciteit van bepaalde omgevingen overschrijden. Zie bijvoorbeeld de discussie over groottelimieten voor Machine Learning Studio (klassiek) in de volgende sectie. In dergelijke gevallen u tijdens de analyse een voorbeeld van de gegevens gebruiken. Zie [Voorbeeldgegevens in het Team Data Science](sample-data.md)Process voor meer informatie over het down-samplen van een gegevensset in verschillende Azure-omgevingen.

## <a name="data-characteristics-questions-type-format-and-size"></a>Vragen over gegevenskenmerken: tekst, indeling en grootte

Deze vragen zijn essentieel voor het plannen van uw opslag- en verwerkingsomgevingen. Ze helpen u bij het kiezen van het juiste scenario voor uw gegevenstype en begrijpen eventuele beperkingen.

### <a name="what-are-the-data-types"></a>Wat zijn de gegevenstypen?

* Numerieke
* Categorische gegevens
* Tekenreeksen
* Binair

### <a name="how-is-your-data-formatted"></a>Hoe worden uw gegevens opgemaakt?

* Platte bestanden (CSV) of tab-separated (TSV)
* Gecomprimeerd of niet-gecomprimeerd
* Azure-blobs
* Hadoop Hive tafels
* SQL Server-tabellen

### <a name="how-large-is-your-data"></a>Hoe groot zijn uw gegevens?

* Klein: minder dan 2 GB
* Gemiddeld: meer dan 2 GB en minder dan 10 GB
* Groot: meer dan 10 GB

Neem bijvoorbeeld de Azure Machine Learning Studio (klassieke) omgeving:

* Zie [Sectie Gegevensindelingen en gegevenstypen](../studio/import-data.md#supported-data-formats-and-data-types) voor een lijst met de gegevensindelingen en -typen die worden ondersteund door Azure Machine Learning Studio.
* Zie [Azure Subscription and Service Limits, Quota en Limitations](../../azure-resource-manager/management/azure-subscription-service-limits.md)voor informatie over de beperkingen van andere Azure-services die in het analyseproces worden gebruikt.

## <a name="data-quality-questions-exploration-and-pre-processing"></a>Vragen over de kwaliteit van gegevens: verkenning en voorbewerking

### <a name="what-do-you-know-about-your-data"></a>Wat weet u van uw gegevens?

Begrijp de basiskenmerken van uw gegevens:

* Welke patronen of trends het vertoont
* Welke uitschieters het heeft
* Hoeveel waarden ontbreken

Deze stap is belangrijk om u te helpen:

* Bepalen hoeveel voorverwerking nodig is
* Formuleer hypothesen die de meest geschikte kenmerken of het type analyse suggereren
* Plannen formuleren voor aanvullende gegevensverzameling

Nuttige technieken voor gegevensinspectie zijn beschrijvende statistiekenberekening en visualisatieplots. Zie Gegevens verkennen in het Team Data [Science Process](explore-data.md)voor meer informatie over het verkennen van een gegevensset in verschillende Azure-omgevingen.

### <a name="does-the-data-require-preprocessing-or-cleaning"></a>Moeten de gegevens worden voorbewerking of reiniging nodig?

Mogelijk moet u uw gegevens vooraf verwerken en schoonmaken voordat u de gegevensset effectief gebruiken voor machine learning. Ruwe gegevens zijn vaak luidruchtig en onbetrouwbaar. Het mist misschien waarden. Het gebruik van dergelijke gegevens voor modellering kan misleidende resultaten opleveren. Zie [Taken om gegevens voor te bereiden op verbeterde machine learning voor](prepare-data.md)een beschrijving.

## <a name="tools-and-languages-questions"></a>Vragen over hulpmiddelen en talen

Er zijn veel opties voor talen, ontwikkelomgevingen en tools. Wees je bewust van je behoeften en voorkeuren.

### <a name="what-languages-do-you-prefer-to-use-for-analysis"></a>Welke talen gebruikt u het liefst voor analyse?

* R
* Python
* SQL

### <a name="what-tools-should-you-use-for-data-analysis"></a>Welke tools moet u gebruiken voor data-analyse?

* [Microsoft Azure Powershell](/powershell/azure/overview) - een scripttaal die wordt gebruikt om uw Azure-resources in een scripttaal te beheren
* [Azure Machine Learning Studio](../studio/what-is-ml-studio.md)
* [Revolutie Analytics](https://www.microsoft.com/sql-server/machinelearningserver)
* [RStudio (RStudio)](https://www.rstudio.com)
* [Python Tools for Visual Studio](https://aka.ms/ptvsdocs)
* [Anaconda](https://www.continuum.io/why-anaconda)
* [Jupyter-notebooks](https://jupyter.org/)
* [Microsoft Power BI](https://powerbi.microsoft.com)

## <a name="identify-your-advanced-analytics-scenario"></a>Uw scenario voor geavanceerde analyses identificeren

Nadat u de vragen in de vorige sectie hebt beantwoord, bent u klaar om te bepalen welk scenario het beste bij uw aanvraag past. De voorbeeldscenario's worden beschreven in [scenario's voor geavanceerde analyses in Azure Machine Learning.](plan-sample-scenarios.md)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Wat is Team Data Science Process (TDSP)?](overview.md)
