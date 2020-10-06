---
title: Wat is Azure Synapse Analytics?
description: Een overzicht van Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 09/12/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: c4338152579170bf809577262992f0db9a1a95ff
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/15/2020
ms.locfileid: "90524943"
---
# <a name="what-is-azure-synapse-analytics-workspaces-preview"></a>Wat is Azure Synapse Analytics (preview van werkruimten)?

[!INCLUDE [preview](includes/note-preview.md)]

Zakelijke analyses moeten op grote schaal worden uitgevoerd op elk soort gegevens, ongeacht of het gaat om onbewerkte, precieze of zeer samengestelde gegevens. Hiervoor moeten ondernemingen technologieën voor big data en datawarehousing, zoals Spark en SQL, samenvoegen in pijplijnen met rijke gegevens, die werken met de gegevens in relationele opslag en data lakes. Oplossingen zoals deze zijn moeilijk te bouwen, beveiligen en onderhouden. De complexiteit zorgt voor vertraging in de levering van de inzichten die ondernemingen nodig hebben.

**Azure Synapse** is een geïntegreerde analyse-service die sneller inzicht biedt in alle datawarehouses en analysesystemen voor big data. In de kern combineert Azure Synapse het beste van de **SQL**-technologieën die worden gebruikt in zakelijke datawarehousing, **Spark**-technologieën die worden gebruikt voor big data, en **Pijplijnen** voor gegevensintegratie en ETL/ELT. Synapse heeft een webgebaseerde **Studio** die één plek biedt voor beheer, bewaking, codering en beveiliging. Synapse bevat een diepgaande integratie met andere Azure-services, zoals **PowerBI**, **CosmosDB** en **AzureML**.

## <a name="key-features--benefits"></a>Belangrijkste functies en voordelen

### <a name="industry-leading-sql"></a>Toonaangevende SQL

* **Synapse SQL** is een gedistribueerd querysysteem waarmee ondernemingen datawarehousing en gegevensvirtualisatie-scenario's kunnen implementeren met behulp van standaard T-SQL-ervaringen die bekend zijn bij data engineers. Ook worden de mogelijkheden van SQL uitgebreid om scenario's wat betreft streaming- en machine learning aan te pakken.

* Synapse SQL biedt zowel **serverloze** als **toegewezen** resourcemodellen, waarmee u de opties voor verbruik en facturering kunt aanpassen aan uw behoeften. Voor voorspelbare prestaties en kosten kunt u toegewezen SQL-pools maken om verwerkingskracht te reserveren voor gegevens die zijn opgeslagen in SQL-tabellen. Voor niet-geplande of bursty werkbelastingen gebruikt u het altijd beschikbare, serverloze SQL-eindpunt.
* Gebruik ingebouwde **streamingmogelijkheden** om gegevens van gegevensbronnen in de cloud in SQL-tabellen te plaatsen
* Integreer AI met SQL, door **machine learning**-modellen te gebruiken om gegevens een score te geven met behulp van de [functie T-SQL PREDICT](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest)

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

### <a name="built-in-data-integration-via-pipelines"></a>Ingebouwde gegevensintegratie via pijplijnen

Azure Synapse is ingebouwd met dezelfde gegevensintegratie-engine en ervaring als Azure Data Factory, zodat u uitgebreide ETL-pijplijnen op schaal kunt maken zonder Synapse Analytics te verlaten.

* Gegevens opnemen uit meer dan 90 gegevensbronnen
* Code-Free ETL met gegevensstroomactiviteiten
* Notebooks, Spark-taken, opgeslagen procedures, SQL-scripts en meer indelen

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

* [Aan de slag met Azure Synapse Analytics](get-started.md)
* [Een werkruimte maken](quickstart-create-workspace.md)
* [SQL on-demand gebruiken](quickstart-sql-on-demand.md)
