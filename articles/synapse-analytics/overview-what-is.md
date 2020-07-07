---
title: Wat is Azure Synapse Analytics?
description: Een overzicht van Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 04/15/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 96bb624472aa2053599765d5bfedeb96339d0973
ms.sourcegitcommit: 374d1533ea2f2d9d3f8b6e6a8e65c6a5cd4aea47
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/01/2020
ms.locfileid: "85808043"
---
# <a name="what-is-azure-synapse-analytics-workspaces-preview"></a>Wat is Azure Synapse Analytics (preview van werkruimten)?

[!INCLUDE [preview](includes/note-preview.md)]

De zakelijke analyses van vandaag moeten op grote schaal worden uitgevoerd op elk soort gegevens, ongeacht of het gaat om onbewerkte, precieze of zeer samengestelde gegevens. In het verleden moesten ondernemingen dit soort analyse-oplossingen opbouwen big data en gegevensopslagtechnologieën zoals Spark en SQL samenvoegen. Vervolgens moesten ze worden geïntegreerd in uitgebreide gegevenspijplijnen die werken met gegevens in relationele winkels en data lakes.  

Oplossingen zoals dit zijn moeilijk te bouwen, te configureren, te beveiligen en te onderhouden, waardoor het snel extraheren van intelligent inzicht wordt vertraagd.

**Azure Synapse** is een geïntegreerde analyse-service die sneller inzicht biedt van alle gegevens op elke schaal, in alle datawarehouses en analyse-systemen voor big data. Het combineert het beste van de **SQL**- technologieën die worden gebruikt in zakelijke datawarehousing, **Spark**-technologieën die worden gebruikt in big data-analyses en **Pijplijnen** om verplaatsing van activiteiten en gegevens te organiseren.

Azure Synapse wordt geleverd met een web-eigen **Studio**-gebruikerservaring die één ervaring en model biedt voor beheer, bewaking, codering en beveiliging.

Azure Synapse biedt een onderneming de eenvoudigste en snelste manier om inzicht te krijgen in gegevens van elke willekeurige grootte, met behulp van de analyse waarmee ze het meest bekend zijn. Het integreert diep met **Power BI** en stelt IT-medewerkers in staat om analyse-oplossingen te bouwen die end-to-end werken om bedrijfsinformatie te bieden.

Daarnaast vereenvoudigt Azure Synapse het bouwen van voorspellende modellen en geavanceerde analyses met machine learning via de ingebouwde ondersteuning voor **AzureML**.

## <a name="key-features--benefits"></a>Belangrijkste functies en voordelen

### <a name="industry-leading-sql"></a>Toonaangevende SQL

* **Synapse SQL** is een gedistribueerd querysysteem waarmee ondernemingen datawarehousing en gegevensvirtualisatie-scenario's kunnen implementeren met behulp van standaard T-SQL-ervaringen die bekend zijn bij data engineers. Ook worden de mogelijkheden van SQL uitgebreid om scenario's wat betreft streaming- en machine learning aan te pakken.

* Synapse SQL biedt zowel **serverloze** als **ingerichte** resourcemodellen, waarmee u de opties voor verbruik en facturering kunt aanpassen aan uw behoeften. Voor voorspelbare prestaties en kosten kunt u pools inrichten om verwerkingskracht te reserveren voor gegevens die zijn opgeslagen in SQL-tabellen. Voor niet-geplande of bursty werkbelastingen gebruikt u het altijd beschikbare, serverloze SQL-eindpunt.
* Gebruik ingebouwde **streamingmogelijkheden** om gegevens van gegevensbronnen in de cloud in SQL-tabellen te plaatsen
* Integreer AI met SQL, door **machine learning**-modellen te gebruiken om gegevens te een score te geven met behulp van de functie T-SQL PREDICT

### <a name="industry-standard-apache-spark"></a>Toonaangevende Apache Spark

**Apache Spark voor Azure Synapse** integreert diep en naadloos Apache Spark, de meest populaire open source big data-engine die wordt gebruikt voor gegevensvoorbereiding, data engineering, ETL en machine learning.

* ML-modellen met SparkML-algoritmen en AzureML-integratie voor Apache Spark 2.4 met ingebouwde ondersteuning voor Linux Foundation Delta Lake.
* Vereenvoudigd resourcemodel waarmee u zich geen zorgen hoeft te maken over het beheer van clusters.
* Snel Spark opstarten en agressief automatisch schaal aanpassen.
* Ingebouwde ondersteuning voor .NET voor Spark, waarmee u uw C#-expertise en de bestaande .NET-code in een Spark-toepassing opnieuw kunt gebruiken.

### <a name="interop-of-sql-and-apache-spark-on-your-data-lake"></a>Interop van SQL en Apache Spark op uw Data Lake

Azure Synapse verwijdert de traditionele technologische barrières tussen het gebruik van SQL en Spark. U kunt naadloos combineren en vergelijken op basis van uw behoeften en expertise.

* Met een gedeeld metagegevenssysteem dat compatibel is met de Hive, kunnen tabellen die zijn gedefinieerd op bestanden in de data lake naadloos worden gebruikt door Spark of Hive.
* SQL en Spark kunnen Parquet-, CSV-, TSV- en JSON-bestanden die zijn opgeslagen in de data lake rechtstreeks verkennen en analyseren.
* Snel schaalbare belasting en verwijderen van belasting voor gegevens die worden verzonden tussen SQL-en Spark-databases

### <a name="built-in-orchestration-via-pipelines"></a>Ingebouwde indeling via pijplijnen

Azure Synapse is ingebouwd met dezelfde gegevensintegratie-engine en ervaring als Azure Data Factory, zodat u uitgebreide gegevenspijplijnen kunt maken zonder een afzonderlijke indelingsengine te gebruiken.

* Gegevens verplaatsen tussen Azure Synapse en meer dan 90 on-premises gegevensbronnen
* Notebooks, pijplijnen, Spark-taken, SQL-scripts, opgeslagen procedures indelen
* Code-Free ETL met gegevensstroomactiviteiten

### <a name="unified-management-monitoring-and-security"></a>Uniform beheer, bewaking en beveiliging

Azure Synapse biedt ondernemingen één manier om analyse-resources te beheren, gebruik en activiteiten te bewaken en beveiliging af te dwingen.

* Gebruikers toewijzen aan Rol om de toegang tot analytics-resources te vereenvoudigen
* Nauwkeurig toegangsbeheer voor gegevens en code
* Eén dashboard voor het bewaken van resources, gebruik en gebruikers in SQL en Spark

### <a name="synapse-studio"></a>Synapse Studio

**Synapse Studio** is de websysteem-eigen ervaring die alles verbindt de data engineers, zodat ze op één locatie elke taak kunnen uitvoeren die ze nodig hebben om een volledige oplossing te bouwen.

* Bouw een end-to-end-analyse-oplossing op één locatie: opnemen, verkennen, voorbereiden, organiseren, visualiseren
* Toonaangevende productiviteit voor data engineers die SQL-of Spark-code schrijven: ontwerpen, foutopsporing en prestaties optimaliseren
* Integreren met zakelijke CI/CD-processen

## <a name="next-steps"></a>Volgende stappen

* [Een werkruimte maken](quickstart-create-workspace.md)
* [Synapse Studio gebruiken](quickstart-synapse-studio.md)
* [Een SQL-pool maken](quickstart-create-sql-pool-portal.md)
* [SQL on-demand gebruiken](quickstart-sql-on-demand.md)
* [Een Apache Spark-pool maken](quickstart-create-apache-spark-pool-portal.md)
