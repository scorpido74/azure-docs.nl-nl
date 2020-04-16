---
title: Wat is Azure Synapse Analytics?
description: Een overzicht van Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 65f6c6627093358f8cbc66055bb9b16561f7c610
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424662"
---
# <a name="what-is-azure-synapse-analytics-workspaces-preview"></a>Wat is Azure Synapse Analytics (voorbeeld van werkruimten)?

[!INCLUDE [preview](includes/note-preview.md)]

Tegenwoordig vereist enterprise analytics dat op grote schaal wordt gebruikt op elke vorm van gegevens, of het nu rauw, verfijnd of sterk samengesteld is. In het verleden, het bouwen van dit soort analytics-oplossingen vereist ondernemingen aan elkaar te hechten big data en data warehousing technologieën zoals Spark en SQL. Vervolgens moeten ze ze integreren in rijke gegevenspijplijnen die werken in gegevens in relationele winkels en gegevensmeren.  

Oplossingen als deze zijn moeilijk te bouwen, configureren, beveiligen en onderhouden, wat de snelle extractie van intelligent inzicht vertraagt.

**Azure Synapse** is een geïntegreerde analyseservice die de tijd voor inzicht van alle gegevens op elke schaal, in datawarehouses en big data analytics-systemen versnelt. Het brengt het beste van de **SQL-technologieën** die worden gebruikt in enterprise data warehousing, **Spark-technologieën** die worden gebruikt in big data analytics en **Pipelines** samen om activiteiten en dataverkeer te orkestreren. 

Azure Synapse wordt geleverd **Studio** met een web-native Studio-gebruikerservaring die één ervaring en model biedt voor beheer, bewaking, codering en beveiliging.

Azure Synapse biedt een onderneming de eenvoudigste en snelste manier om inzichten te verzamelen over alle gegevens op elke grootte, met behulp van de analyses die ze het meest kennen. Het integreert diep met **Power BI,** waardoor data engineers analytics-oplossingen kunnen bouwen die end-to-end werken om Business Intelligence te bieden. 

Daarnaast faciliteert Azure Synapse voorspellende modelbuilding en geavanceerde analyses met machine learning via de ingebouwde ondersteuning voor **AzureML.**

## <a name="key-features--benefits"></a>Belangrijkste functies & voordelen

### <a name="industry-leading-sql"></a>Toonaangevende SQL

* **Synapse SQL** is een gedistribueerd querysysteem waarmee ondernemingen scenario's voor gegevensopslag en gegevensvirtualisatie kunnen implementeren met behulp van standaard T-SQL-ervaringen die bekend zijn bij dataengineers. Het breidt ook de mogelijkheden van SQL uit om streaming- en machine learning-scenario's aan te pakken.

* Synapse SQL biedt zowel **serverloze** als **ingerichte** resourcemodellen, die verbruiks- en factureringsopties bieden die aan uw behoeften voldoen. Voor voorspelbare prestaties en kosten u de verwerkingskracht reserveren voor gegevens die zijn opgeslagen in SQL-tabellen voor voorspelbare prestaties en kosten. Voor ongeplande of bursty workloads gebruikt u het serverloze, altijd beschikbare SQL-eindpunt.
* Gebruik ingebouwde **streamingmogelijkheden** om gegevens uit cloudgegevensbronnen in SQL-tabellen te plaatsen
* Integreer AI met SQL, door **machine learning-modellen** te gebruiken om gegevens te scoren met de t-SQL PREDICT-functie

### <a name="industry-standard-apache-spark"></a>Industriestandaard Apache Spark

**Synapse Spark** integreert Apache Spark diep en naadloos - de meest populaire open source big data-engine die wordt gebruikt voor gegevensvoorbereiding, data engineering, ETL en machine learning.

* ML-modellen met SparkML-algoritmen en AzureML-integratie voor Apache Spark 2.4 met ingebouwde ondersteuning voor Linux Foundation Delta Lake.
* Vereenvoudigd resourcemodel dat u bevrijdt van het feit dat u zich geen zorgen hoeft te maken over het beheren van clusters.
* Snelle Spark-opstart en agressieve automatisch schalen.
* Ingebouwde ondersteuning voor .NET for Spark waarmee u uw C#-expertise en bestaande .NET-code hergebruiken in een Spark-toepassing.

### <a name="interop-of-sql-and-spark-on-your-data-lake"></a>Interop van SQL en Spark op uw Data Lake

Azure Synapse verwijdert de traditionele technologische barrières tussen het gebruik van SQL en Spark samen. U naadloos mixen en matchen op basis van uw behoeften en expertise.

* Met een gedeeld Metagegevenssysteem voor Hive-compatibele gegevens kunnen tabellen die zijn gedefinieerd op bestanden in het gegevensmeer naadloos worden verbruikt door Spark of Hive.
* SQL en Spark kunnen direct parket-, CSV-, TSV- en JSON-bestanden verkennen en analyseren die in het gegevensmeer zijn opgeslagen.
* Snel schaalbaar laden en lossen voor gegevens die tussen SQL- en Spark-databases gaan

### <a name="built-in-orchestration-via-pipelines"></a>Ingebouwde orkestratie via pijpleidingen

Azure Synapse wordt geleverd met dezelfde Data Integration-engine en ervaringen als Azure Data Factory, zodat u rijke gegevenspijplijnen maken zonder een aparte orchestration-engine te gebruiken.

* Gegevens verplaatsen tussen Synapse en 85+ on-premises gegevensbronnen
* Notebooks, pijplijnen, Spark-taken, SQL-scripts, opgeslagen procedures orkestreren
* Codevrije ETL met gegevensstroomactiviteiten

### <a name="unified-management-monitoring-and-security"></a>Uniform beheer, bewaking en beveiliging

Azure Synapse biedt bedrijven één manier om analysebronnen te beheren, het gebruik en de activiteit te bewaken en beveiliging af te dwingen.

* Gebruikers toewijzen aan Rol om de toegang tot analysebronnen te vereenvoudigen
* Fijnmazige toegangscontrole op gegevens en code
* Eén dashboard om resources, gebruik en gebruikers in SQL en Spark te controleren

### <a name="synapse-studio"></a>Synapse Studio

**Synapse Studio** is de web-native ervaring die alles met elkaar verbindt voor data engineers, waardoor ze op één locatie elke taak kunnen uitvoeren die ze nodig hebben om een complete oplossing te bouwen.

* Bouw een end-to-end analytics-oplossing op één plek: innemen, verkennen, voorbereiden, orkestreren, visualiseren
* Toonaangevende productiviteit voor gegevenstechnici die SQL- of Spark-code schrijven: authoring, debugging en prestatieoptimalisatie
* Integreren met zakelijke CI/CD-processen

## <a name="next-steps"></a>Volgende stappen

* [Een werkruimte maken](quickstart-create-workspace.md)
* [Synapse Studio gebruiken](quickstart-synapse-studio.md)
* [Een SQL-groep maken](quickstart-create-sql-pool.md)
* [SQL on-demand gebruiken](quickstart-sql-on-demand.md)
* [Een Apache Spark-pool maken](quickstart-create-apache-spark-pool.md)
