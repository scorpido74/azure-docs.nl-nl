---
title: 'Cheatsheet: Azure Synapse Analytics (preview van werkruimten)'
description: Naslaggids waarmee de gebruiker Azure Synapse Analytics leert kennen
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 3141f8044a4a257de8022ff789b12d5d3e6e7a90
ms.sourcegitcommit: 374d1533ea2f2d9d3f8b6e6a8e65c6a5cd4aea47
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/01/2020
ms.locfileid: "85807023"
---
# <a name="azure-synapse-analytics-cheat-sheet"></a>Cheatsheet voor Azure Synapse Analytics

[!INCLUDE [preview](includes/note-preview.md)]

Het cheatsheet voor Azure Synapse Analytics bevat een overzicht van de basisconcepten en belangrijke opdrachten van de service. Dit artikel is nuttig voor zowel nieuwe gebruikers als mensen die meer over belangrijke Azure Synapse-onderwerpen willen weten.

## <a name="architecture"></a>Architectuur

> [!div class="mx-imgBorder"]
>![Synapse-architectuur](media/overview-cheat-sheet/azure-synapse-architecture-cheat-sheet.png)

## <a name="terminology"></a>Terminologie
| Termijn                         | Definitie      |
|:---                                 |:---                 |
| **Synapse-werkruimte** | Een beveiligbare samenwerkingsgrens voor het uitvoeren van zakelijke cloudanalyses in Azure. Een werkruimte wordt geïmplementeerd in een specifieke regio en heeft een gekoppeld ADLS Gen2-account en -bestandssysteem (voor het opslaan van tijdelijke gegevens). Een werkruimte bevindt zich onder een resourcegroep. |
| **Synapse SQL**   | Hiermee kunt u analyses uitvoeren met pools of met on-demand functies.  |
| **SQL-pool**   | 0-to-N in SQL ingerichte resources en de bijbehorende databases kunnen in een werkruimte worden geïmplementeerd. Elke SQL-pool heeft een gekoppelde database. Een SQL-pool kan handmatig of automatisch worden geschaald, onderbroken en hervat. Een SQL-pool kan worden geschaald van 100 DWU's tot 30.000 DWU's.       |
| **SQL on-demand**   | Het verwerkingssysteem voor gedistribueerde gegevens dat is gebouwd voor grote volumes gegevens waarmee u T-SQL-query's kunt uitvoeren op gegevens in data lake. Het is serverloos, zodat u de infrastructuur niet hoeft te beheren.       |
|**Apache Spark for Synapse** | Spark-runtime die wordt gebruikt in een Spark-pool. De huidige versie die wordt ondersteund is Spark 2.4 met python 3.6.1, scala 2.11.12, .NET-ondersteuning voor Apache Spark 0.5 en Delta Lake 0.3.  | 
| **Apache Spark-pool**  | 0-to-N in Spark ingerichte resources en de bijbehorende databases kunnen in een werkruimte worden geïmplementeerd. Een Spark-pool kan automatisch worden onderbroken, hervat en geschaald.  |
| **Spark-toepassing**  |   Het bestaat uit een stuurprogrammaproces en een reeks uitvoeringsprocedures. Een Spark-toepassing wordt uitgevoerd in een Spark-pool.            |
| **Spark-sessie**  |   Uniform invoerpunt van een Spark-toepassing. Een sessie biedt een manier om te communiceren met de verschillende functies van Spark en met een kleiner aantal constructs. Als u een notebook wilt uitvoeren, moet u een sessie maken. Een sessie kan worden geconfigureerd om te worden uitgevoerd op een specifiek aantal uitvoerders van een specifiek formaat. De standaardconfiguratie voor een notebook-sessie is om op twee middelgrote uitvoerders te worden uitgevoerd. |
| **SQL-aanvraag**  |   Een bewerking zoals een query die wordt uitgevoerd via een SQL-pool of SQL on-demand. |
|**Gegevensintegratie**| Biedt de mogelijkheid om gegevens op te nemen in verschillende bronnen en activiteiten te organiseren die worden uitgevoerd binnen of buiten een werkruimte.| 
|**Artefacten**| Een concept dat alle objecten omvat die een gebruiker nodig heeft om gegevensbronnen te beheren, te ontwikkelen, te organiseren en te visualiseren.|
|**Notebook**| Interactieve en reactieve interface voor gegevenswetenschap en engineering die ondersteuning biedt voor Scala, PySpark, C# en SparkSQL. |
|**Spark-taakdefinitie**|Interface voor het verzenden van een Spark-taak met een assembly-JAR die de code en de bijbehorende afhankelijkheden bevat.|
|**Gegevensstroom**|  Biedt een volledig visuele ervaring zonder codering om big data-transformatie uit te voeren. Alle optimalisatie- en uitvoeringstaken worden op serverloze wijze afgehandeld. |
|**SQL-script**| Een set met SQL-opdrachten die zijn opgeslagen in een bestand. Een SQL-script kan een of meer SQL-instructies bevatten. Het kan worden gebruikt om SQL-aanvragen uit te voeren via een SQL-pool of SQL on-demand.|
|**Pijplijn**| Een logische groep activiteiten die samen een taak uitvoeren.|
|**Activiteit**| Staat voor acties die moeten worden uitgevoerd op gegevens, zoals het kopiëren van gegevens of het uitvoeren van een notebook of een SQL-script.|
|**Trigger**| Voert een pijplijn uit. Deze kan handmatig of automatisch worden uitgevoerd (planning, tumblingvenster of op gebeurtenis gebaseerd).|
|**Gekoppelde service**| Verbindingsreeksen waarmee de verbindingsgegevens worden gedefinieerd die nodig zijn om de werkruimte aan externe resources te koppelen.|
|**Gegevensset**|  Een weergave van gegevens met een naam die simpelweg verwijst naar de gegevens die in een activiteit moeten worden gebruikt als invoer en uitvoer. Deze hoort bij een gekoppelde service.|

## <a name="next-steps"></a>Volgende stappen

- [Een werkruimte maken](quickstart-create-workspace.md)
- [Synapse Studio gebruiken](quickstart-synapse-studio.md)
- [Een SQL-pool maken](quickstart-create-sql-pool-portal.md)
- [Een Apache Spark-pool maken](quickstart-create-apache-spark-pool-portal.md)
- [SQL on-demand gebruiken](quickstart-sql-on-demand.md)

