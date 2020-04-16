---
title: Spiekblad - Azure Synapse Analytics
description: Referentiegids die door Azure Synapse Analytics loopt
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: aa93a816fc11158d928978bdec2dbf42119fa149
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424655"
---
# <a name="azure-synapse-analytics-cheat-sheet"></a>Azure Synapse Analytics cheat sheet

[!INCLUDE [preview](includes/note-preview.md)]

Het azure Synapse Analytics-spiekbriefje leidt u door de basisconcepten van de service en belangrijke opdrachten. Dit artikel is handig voor zowel nieuwe leerlingen als degenen die hoogtepunten van de essentiële Azure Synapse-onderwerpen willen.

## <a name="architecture"></a>Architectuur

> [!div class="mx-imgBorder"]
>![Synapsenarchitectuur](media/overview-cheat-sheet/azure-synapse-architecture-cheat-sheet.png)

## <a name="concepts"></a>Concepten
| Zelfstandige naamwoorden en werkwoorden                         | Wat het doet       |
|:---                                 |:---                 |
| **Synapse Workspace (voorbeeld)** | Een securable samenwerkingsgrens voor het uitvoeren van cloudgebaseerde bedrijfsanalyses in Azure. Een werkruimte wordt geïmplementeerd in een specifieke regio en heeft een bijbehorend ADLSg2-account- en bestandssysteem (voor het opslaan van tijdelijke gegevens). Een werkruimte bevindt zich onder een resourcegroep. |
| **SQL Analytics**   | Voer analyses uit met pools of met on-demand mogelijkheden.  |
| **SQL-pool**   | 0-to-N SQL-ingerichte resources met bijbehorende databases kunnen worden geïmplementeerd in een werkruimte. Elke SQL-groep heeft een bijbehorende database. Een SQL-pool kan handmatig of automatisch worden geschaald, onderbroken en hervat. Een SQL-pool kan schalen van 100 DWU tot 30.000 DWU.       |
| **SQL on-demand (voorbeeld)**   | Gedistribueerd gegevensverwerkingssysteem dat is gebouwd voor grootschalige gegevens waarmee u T-SQL-query's uitvoeren via gegevens in data lake. Het is serverloos, zodat u geen infrastructuur hoeft te beheren.       |
|**Apache Spark** | Spark run-time gebruikt in een Spark pool. De huidige versie wordt ondersteund spark 2.4 met Python 3.6.1, Scala 2.11.12, .NET-ondersteuning voor Apache Spark 0.5 en Delta Lake 0.3.  | 
| **Apache Spark-pool (preview)**  | 0-to-N Spark-ingerichte resources met de bijbehorende databases kunnen worden geïmplementeerd in een werkruimte. Een Spark-pool kan automatisch worden onderbroken, hervat en geschaald.  |
| **Spark-toepassing**  |   Het bestaat uit een driverproces en een set executorprocessen. Een Spark-toepassing wordt uitgevoerd op een Spark-pool.            |
| **Spark-sessie**  |   Uniform ingangspunt van een vonktoepassing. Het biedt een manier om te communiceren met de verschillende functionaliteiten spark's en met een kleiner aantal constructies. Als u een notitieblok wilt uitvoeren, moet een sessie worden gemaakt. Een sessie kan worden geconfigureerd om uit te voeren op een bepaald aantal uitvoerders van een specifieke grootte. De standaardconfiguratie voor een notebooksessie is het uitvoeren van 2 middelgrote uitvoerders. |
| **SQL-aanvraag**  |   Bewerking zoals een query die wordt uitgevoerd via SQL-pool of SQL on-demand. |
|**Gegevensintegratie**| Geeft de mogelijkheid om gegevens in te nemen tussen verschillende bronnen en activiteiten te orkestreren die worden uitgevoerd in een werkruimte of buiten een werkruimte.| 
|**Artefacten**| Concept dat alle objecten inkapselt die nodig zijn voor een gebruiker om gegevensbronnen te beheren, te ontwikkelen, te orkestreren en te visualiseren.|
|**Notebook**| Interactieve en reactieve Data Science and Engineering-interface die Scala, PySpark, C#en SparkSQL ondersteunt. |
|**Definitie van taak stimuleren**|Interface om een Spark-taak in te dienen door met assemblagepot met de code en de afhankelijkheden ervan.|
|**Gegevensstroom**|  Biedt een volledig visuele ervaring zonder codering die nodig is om big data transformatie te doen. Alle optimalisatie en uitvoering worden op een serverloze manier afgehandeld. |
|**SQL-script**| Set SQL-opdrachten die in een bestand zijn opgeslagen. Een SQL-script kan een of meer SQL-instructies bevatten. Het kan worden gebruikt om SQL-aanvragen uit te voeren via SQL-pool of SQL on-demand.|
|**Pijpleiding**| Logische groepering van activiteiten die samen een taak uitvoeren.|
|**Activiteit**| Hiermee definieert u acties die moeten worden uitgevoerd op gegevens, zoals het kopiëren van gegevens, het uitvoeren van een notitieblok of een SQL-script.|
|**Trigger**| Hiermee wordt een pijplijn uitgevoerd. Het kan handmatig of automatisch worden uitgevoerd (schema, tuimelvenster of gebeurtenisgebaseerd).|
|**Gekoppelde service**| Verbindingstekenreeksen die de verbindingsgegevens definiëren die nodig zijn voor de werkruimte om verbinding te maken met externe bronnen.|
|**Dataset**|  Benoemde weergave van gegevens die eenvoudig punten of verwijzingen naar de gegevens die in een activiteit moeten worden gebruikt als invoer en uitvoer. Het behoort tot een Gekoppelde Service.|

## <a name="next-steps"></a>Volgende stappen

- [Een werkruimte maken](quickstart-create-workspace.md)
- [Synapse Studio gebruiken](quickstart-synapse-studio.md)
- [Een SQL-groep maken](quickstart-create-sql-pool.md)
- [SQL on-demand gebruiken](quickstart-sql-on-demand.md)
- [Een Apache Spark-pool maken](quickstart-create-apache-spark-pool.md)

