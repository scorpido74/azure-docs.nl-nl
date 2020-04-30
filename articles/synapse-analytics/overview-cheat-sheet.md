---
title: Cheat-blad-Azure Synapse Analytics
description: Naslag Gids voor de gebruiker via Azure Synapse Analytics
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: aa93a816fc11158d928978bdec2dbf42119fa149
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424655"
---
# <a name="azure-synapse-analytics-cheat-sheet"></a>Cheat-blad voor Azure Synapse Analytics

[!INCLUDE [preview](includes/note-preview.md)]

Het Azure Synapse Analytics Cheat-blad bevat een overzicht van de basis concepten van de service en belang rijke opdrachten. Dit artikel is nuttig voor zowel nieuwe kennissen als mensen die belang rijke Azure Synapse-onderwerpen willen markeren.

## <a name="architecture"></a>Architectuur

> [!div class="mx-imgBorder"]
>![Synapse-architectuur](media/overview-cheat-sheet/azure-synapse-architecture-cheat-sheet.png)

## <a name="concepts"></a>Concepten
| Zelfstandig naam woord en werk woord                         | Wat het doet       |
|:---                                 |:---                 |
| **Synapse-werk ruimte (preview-versie)** | Een Beveilig bare samenwerkings grens voor het uitvoeren van cloud-based Enter prise-analyses in Azure. Een werk ruimte wordt in een specifieke regio geïmplementeerd en heeft een bijbehorend ADLSg2-account en-bestands systeem (voor het opslaan van tijdelijke gegevens). Een werk ruimte bevindt zich onder een resource groep. |
| **SQL Analytics**   | Voer analyses uit met Pools of met mogelijkheden op aanvraag.  |
| **SQL-pool**   | met de bijbehorende data bases kunnen 0-naar-N SQL-ingerichte resources worden geïmplementeerd in een werk ruimte. Elke SQL-groep heeft een gekoppelde data base. Een SQL-groep kan worden geschaald, gepauzeerd en hand matig of automatisch worden hervat. Een SQL-pool kan worden geschaald van 100 DWU tot 30.000 DWU.       |
| **SQL op aanvraag (preview-versie)**   | Het systeem voor gedistribueerde gegevens verwerking is gebouwd voor grootschalige gegevens waarmee u T-SQL-query's kunt uitvoeren op gegevens in data Lake. Het is serverloos, zodat u de infra structuur niet hoeft te beheren.       |
|**Apache Spark** | Spark-uitvoerings tijd die wordt gebruikt in een Spark-groep. De huidige versie die wordt ondersteund is Spark 2,4 met python 3.6.1, scala 2.11.12, .NET support voor Apache Spark 0,5 en Delta Lake 0,3.  | 
| **Apache Spark pool (preview-versie)**  | van 0 tot N mousserend ingerichte resources met de bijbehorende data bases kunnen in een werk ruimte worden geïmplementeerd. Een Spark-pool kan automatisch worden onderbroken, hervat en geschaald.  |
| **Spark-toepassing**  |   Het bestaat uit een stuur programma en een reeks procedures voor het uitvoeren van een proces. Een Spark-toepassing wordt uitgevoerd op een Spark-groep.            |
| **Spark-sessie**  |   Uniform ingangs punt van een Spark-toepassing. Het biedt een manier om te communiceren met de verschillende functies van Spark en met een kleiner aantal constructs. Als u een notitie blok wilt uitvoeren, moet u een sessie maken. Een sessie kan worden geconfigureerd om te worden uitgevoerd op een specifiek aantal uitvoerender van een specifieke grootte. De standaard configuratie voor een notitieblok sessie is om twee middel grote runers uit te voeren. |
| **SQL-aanvraag**  |   Een bewerking zoals een query wordt uitgevoerd via SQL-pool of SQL on-demand. |
|**Gegevens integratie**| Biedt de mogelijkheid om gegevens op te nemen tussen verschillende bronnen en activiteiten te organiseren die worden uitgevoerd in een werk ruimte of buiten een werk ruimte.| 
|**Artefacten**| Concept dat alle objecten inkapselt die nodig zijn voor een gebruiker om gegevens bronnen te beheren, te ontwikkelen, te organiseren en te visualiseren.|
|**Notebook**| Interactieve en reactieve gegevens wetenschap en technische interface die ondersteuning bieden voor scala, PySpark, C# en SparkSQL. |
|**Spark-taak definitie**|Interface voor het verzenden van een Spark-taak met assembly jar met de code en de bijbehorende afhankelijkheden.|
|**Gegevensstroom**|  Biedt een volledig visuele ervaring zonder code ring vereist om big data trans formatie uit te voeren. Alle Optima Lise ring en uitvoering worden op serverloze wijze afgehandeld. |
|**SQL-script**| Een set met SQL-opdrachten die zijn opgeslagen in een bestand. Een SQL-script kan een of meer SQL-instructies bevatten. Het kan worden gebruikt om SQL-aanvragen uit te voeren via SQL-groep of SQL on-demand.|
|**Pijp lijn**| Logische groepering van activiteiten die samen een taak uitvoeren.|
|**Activiteit**| Definieert acties die moeten worden uitgevoerd op gegevens zoals het kopiëren van gegevens, het uitvoeren van een notitie blok of een SQL-script.|
|**Trigger**| Voert een pijp lijn uit. Het kan hand matig of automatisch worden uitgevoerd (planning, tumblingvenstertriggers venster of op gebeurtenis gebaseerd).|
|**Gekoppelde service**| Verbindings reeksen waarmee de verbindings gegevens worden gedefinieerd die nodig zijn om de werk ruimte te verbinden met externe resources.|
|**Sets**|  Een benoemde weer gave van gegevens die simpelweg verwijzen naar of verwijzen naar de gegevens die in een activiteit moeten worden gebruikt als invoer en uitvoer. Het hoort bij een gekoppelde service.|

## <a name="next-steps"></a>Volgende stappen

- [Een werkruimte maken](quickstart-create-workspace.md)
- [Synapse Studio gebruiken](quickstart-synapse-studio.md)
- [Een SQL-groep maken](quickstart-create-sql-pool.md)
- [SQL on-demand gebruiken](quickstart-sql-on-demand.md)
- [Een Apache Spark groep maken](quickstart-create-apache-spark-pool.md)

