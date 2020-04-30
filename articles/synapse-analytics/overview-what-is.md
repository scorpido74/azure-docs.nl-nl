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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424662"
---
# <a name="what-is-azure-synapse-analytics-workspaces-preview"></a>Wat is de preview-versie van Azure Synapse Analytics (werk ruimten)?

[!INCLUDE [preview](includes/note-preview.md)]

Vandaag voor Enter prise Analytics moet op grote schaal worden uitgevoerd op elk soort gegevens, ongeacht of het gaat om onbewerkte, verfijnend of met hoge Beschik baarheid. In het verleden bouwen we dit soort analyse oplossingen in de vereiste ondernemingen voor het samen vallen van big data en gegevens opslag technologieën zoals Spark en SQL. Vervolgens moeten ze worden geïntegreerd in uitgebreide gegevens pijplijnen die werken met gegevens in relationele winkels en gegevensmeren.  

Oplossingen zoals dit zijn moeilijk te bouwen, te configureren, te beveiligen en te onderhouden, waardoor het snel uitpakken van intelligent inzicht wordt vertraagd.

**Azure Synapse** is een geïntegreerde analyse service die tijd voor inzicht versnelt van alle gegevens op elke schaal, in data warehouses en Big Data Analytics-systemen. Het biedt samen het beste van de **SQL** -technologieën die worden gebruikt in Bedrijfs gegevens-entrepots, **Spark** -technologieën die worden gebruikt in Big Data Analytics en **pijp lijnen** om activiteiten en gegevens verplaatsing te organiseren. 

Azure Synapse wordt geleverd met een web-eigen **Studio** -gebruikers ervaring die één ervaring en model biedt voor beheer, bewaking, code ring en beveiliging.

Azure Synapse biedt een onderneming de eenvoudigste en snelste manier om inzicht te krijgen in gegevens op elke wille keurige grootte, met behulp van de analyse waarmee ze het meest bekend zijn. Het integreert diep met **Power bi** zodat data engineers analyse oplossingen kunnen bouwen die end-to-end werken om bedrijfs informatie te bieden. 

Daarnaast vereenvoudigt Azure Synapse het voorspellende model gebouw en geavanceerde analyses met machine learning via de ingebouwde ondersteuning voor **AzureML**.

## <a name="key-features--benefits"></a>Belangrijkste functies & voor delen

### <a name="industry-leading-sql"></a>Toonaangevende SQL

* **Synapse SQL** is een gedistribueerd query systeem waarmee ondernemingen Data Warehouse-en data Virtualization-scenario's kunnen implementeren met behulp van standaard-T-SQL-ervaringen die bekend zijn bij data engineers. Ook worden de mogelijkheden van SQL uitgebreid om streaming-en machine learning scenario's te verhelpen.

* Synapse SQL biedt zowel **serverloze** als **ingerichte** resource modellen, die verbruiks-en facturerings opties bieden die aan uw behoeften voldoen. Voor voorspel bare prestaties en kosten kunt u Pools inrichten om verwerkings kracht te reserveren voor gegevens die zijn opgeslagen in SQL-tabellen. Voor niet-geplande of burstieve workloads gebruikt u het altijd beschik bare SQL-eind punt zonder server.
* Gebruik ingebouwde streaming-mogelijkheden voor het **produceren** van gegevens uit Cloud gegevens bronnen in SQL-tabellen
* Integreer AI met SQL, door **machine learning** modellen te gebruiken om gegevens te scoren met behulp van de functie voor T-SQL-VOORs pellen

### <a name="industry-standard-apache-spark"></a>Industrie standaard Apache Spark

**Synapse Spark** diep en naadloze integreert Apache Spark--de meest populaire open source-Big Data Engine die wordt gebruikt voor het voorbereiden van gegevens, data engineering, ETL en machine learning.

* ML modellen met SparkML-algoritmen en AzureML-integratie voor Apache Spark 2,4 met ingebouwde ondersteuning voor Linux Foundation Delta Lake.
* Vereenvoudigd resource model waarin u zich geen zorgen hoeft te maken over het beheer van clusters.
* Snel Spark opstarten en agressief automatisch schalen.
* Ingebouwde ondersteuning voor .NET voor Spark, waarmee u uw C#-expertise en bestaande .NET-code in een Spark-toepassing opnieuw kunt gebruiken.

### <a name="interop-of-sql-and-spark-on-your-data-lake"></a>Interop van SQL en Spark op uw Data Lake

Azure Synapse verwijdert de traditionele technologische barrières tussen het gebruik van SQL en Spark. U kunt naadloos combi neren en vergelijken op basis van uw behoeften en expertise.

* Met een gedeeld meta gegevens systeem dat compatibel is met de Hive, kunnen tabellen die zijn gedefinieerd op bestanden in de data Lake naadloos worden gebruikt door Spark of Hive.
* SQL en Spark kunnen Parquet-, CSV-, TSV-en JSON-bestanden die zijn opgeslagen in data Lake rechtstreeks verkennen en analyseren.
* Snel schaal bare belasting en verwijderen voor gegevens die worden verzonden tussen SQL-en Spark-data bases

### <a name="built-in-orchestration-via-pipelines"></a>Ingebouwde integratie via pijp lijnen

Azure Synapse is ingebouwd met dezelfde gegevens integratie-engine en ervaring als Azure Data Factory, zodat u uitgebreide gegevens pijplijnen kunt maken zonder een afzonderlijke Orchestration-engine te gebruiken.

* Gegevens verplaatsen tussen Synapse en 85 + on-premises gegevens bronnen
* Orchestrator-notebooks, pijp lijnen, Spark-taken, SQL-scripts, opgeslagen procedures
* Code-Free ETL met gegevens stroom activiteiten

### <a name="unified-management-monitoring-and-security"></a>Uniform beheer, bewaking en beveiliging

Azure Synapse biedt ondernemingen één manier om analyse bronnen te beheren, gebruik en activiteiten te bewaken en beveiliging af te dwingen.

* Gebruikers toewijzen aan rol om de toegang tot Analytics-resources te vereenvoudigen
* Nauw keurig toegangs beheer voor gegevens en code
* Eén dash board voor het bewaken van resources, gebruik en gebruikers in SQL en Spark

### <a name="synapse-studio"></a>Synapse Studio

**Synapse Studio** is de websysteem eigen ervaring die alles samen met gegevens technici verbindt, waardoor ze op één locatie elke taak kunnen uitvoeren die ze nodig hebben om een volledige oplossing te bouwen.

* Bouw een end-to-end-analyse oplossing op één locatie: opnemen, verkennen, voorbereiden, organiseren, visualiseren
* Toonaangevende productiviteit voor data engineers die SQL-of Spark-code schrijven: ontwerpen, fout opsporing en prestaties optimaliseren
* Integreren met Enter prise CI/CD-processen

## <a name="next-steps"></a>Volgende stappen

* [Een werkruimte maken](quickstart-create-workspace.md)
* [Synapse Studio gebruiken](quickstart-synapse-studio.md)
* [Een SQL-groep maken](quickstart-create-sql-pool.md)
* [SQL on-demand gebruiken](quickstart-sql-on-demand.md)
* [Een Apache Spark groep maken](quickstart-create-apache-spark-pool.md)
