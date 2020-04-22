---
title: Veelgestelde vragen - Azure Synapse Analytics
description: Veelgestelde vragen voor Azure Synapse Analytics
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 00a98fdeb4b8febbcb9d1183fabffc7298cc9e4c
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770730"
---
# <a name="azure-synapse-analytics-workspace-preview-frequently-asked-questions"></a>Veelgestelde vragen van Azure Synapse Analytics (workspace preview)

In deze handleiding vindt u de meest gestelde vragen voor Synapse Analytics.

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="general"></a>Algemeen

### <a name="q-what-is-azure-synapse-analytics"></a>V: Wat is Azure Synapse Analytics

A: Azure Synapse is een geïntegreerd dataplatform voor BI, AI en continue intelligentie. Het verbindt verschillende analytics runtimes zoals SQL en Spark via een enkel platform dat een uniforme manier biedt om:

- Beveilig uw analysebronnen, waaronder netwerk, beheer toegang tot één aanmelding tot pool-, gegevens- en ontwikkelingsartefacten.
- Eenvoudig gebeurtenissen die plaatsvinden in uw werkruimteactiviteiten op elke laag te controleren en snel te optimaliseren, te reageren en debuggen.
- Beheer uw metadata in verschillende engines. Maak een Spark-tabel en deze is automatisch beschikbaar in uw Azure Synapse-databases.
- Communiceer met de gegevens via een uniforme gebruikerservaring. Synapse Studio brengt Big Data Developers, Data Engineers, DBA's, Data Analysts en Data Scientists op hetzelfde platform.

### <a name="q-how-do-i-get-started-with-azure-synapse-analytics"></a>V: Hoe ga ik aan de slag met Azure Synapse Analytics

A: Als u Azure Synapse Analytics wilt gaan gebruiken, maakt u een [Synapse-werkruimte](https://portal.azure.com) (deze is gratis!) en maakt u de resources die u wilt onder die werkruimte. U een van onze quickstart-zelfstudies volgen, zoals [Een Synapse SQL-pool maken](quickstart-create-sql-pool.md) of [Een werkruimte maken,](quickstart-create-workspace.md)die u door eenvoudige use case zal leiden. 

U ook voorbeeldnotitieblokken en SQL-scripts vinden in onze [repository.](https://github.com/Azure/azure-synapse-analytics/tree/master/samples) Als u verbinding wilt maken met een openbare gegevensset, maakt u een nieuwe gekoppelde service met de volgende kenmerken:

- azure_storage_account_name = "azureopendatastorage"
- azure_storage_sas_token = "" (schrijf **""**)

### <a name="q-what-are-the-main-components-of-azure-synapse-analytics"></a>V: Wat zijn de belangrijkste onderdelen van Azure Synapse Analytics

A: Azure Synapse heeft de volgende mogelijkheden:

- Analytics-mogelijkheden worden aangeboden via SQL Pool of SQL on-demand (preview) (Serverless).
- Apache Spark-pool (preview) met volledige ondersteuning voor Scala, Python, SparkSQL en C #
- Data Flow biedt een codevrije big data transformatie-ervaring
- Data-integratie & Orchestration om uw gegevens te integreren en al uw codeontwikkeling te operationaliseren
- Studio om toegang te krijgen tot al deze mogelijkheden via één webgebruikersinterface

### <a name="q-how-does-azure-synapse-analytics-relate-to-azure-sql-data-warehouse"></a>V: Hoe verhoudt Azure Synapse Analytics zich tot Azure SQL Data Warehouse

A: Azure Synapse Analytics is een evolutie van Azure SQL Data Warehouse naar een analytics-platform, dat SQL-pool als datawarehouse-oplossing bevat. Dit platform combineert gegevensverkenning, opname, transformatie, voorbereiding en een analyselaag voor het serveren.

## <a name="use-cases"></a>Gebruiksvoorbeelden

### <a name="q-what-is-a-good-use-case-for-synapse-sql-pool"></a>V: Wat is een goede use case voor Synapse SQL pool

A: SQL-pool is het hart van uw behoeften op het gebied van gegevensmagazijnen. Het is de toonaangevende oplossing voor gegevensmagazijnen in [prijs/prestatie.](https://azure.microsoft.com/services/sql-data-warehouse/compare/) SQL-pool is de toonaangevende clouddatawarehouseoplossing, omdat u:

- een grote en gemengde verscheidenheid aan workloads zonder impact in prestaties dankzij hoge gelijktijdigheid en werklastisolatie
- beveilig uw gegevens eenvoudig via geavanceerde functies, variërend van netwerkbeveiliging tot fijnkorrelige toegang
- profiteren van een breed scala aan ecosystemen

### <a name="q-what-is-a-good-use-case-for-spark-in-synapse"></a>V: Wat is een goede use case voor Spark in Synapse

A: Ons eerste doel is om een geweldige Data Engineering ervaring te bieden voor het transformeren van gegevens over het meer in batch of stream. De strakke en eenvoudige integratie met onze data orchestration maakt de operationalisering van uw ontwikkeling eenvoudig.

Een andere use case voor Spark is voor een Data Scientist om:

- een functie extraheren
- gegevens verkennen
- een model trainen

### <a name="q-what-is-a-good-use-case-for-sql-on-demand-in-synapse"></a>V: Wat is een goede use case voor SQL on-demand in Synapse

A: SQL on-demand is een queryservice over de gegevens in uw gegevensmeer. Hiermee u de toegang tot al uw gegevens democratiseren door een bekende T-SQL-syntaxis te bieden om gegevens op te vragen, zonder dat u gegevens hoeft te kopiëren of laden in een gespecialiseerde winkel.

Voorbeelden van gebruiksvoorbeelden zijn de volgende:

- basisdetectie en -verkenning - biedt data-analisten, opkomende gegevenswetenschappers en dataengineers een eenvoudig pad naar het eerste inzicht in gegevens die in hun datalake leven met schema op gelezen T-SQL-query's
- logische datawarehouse - data-analisten kunnen volledige expressiviteit van T-SQL-taal uitvoeren om de gegevens die zich in Azure Storage bevinden rechtstreeks op te vragen en te analyseren en vertrouwde BI-hulpprogramma's (bijvoorbeeld Azure Analyses Services, Power BI Premium, enz.) te gebruiken om dashboards te vernieuwen door Starlight Query-query's opnieuw uit te voeren
- "single query" ETL - stelt data engineers in staat om op Azure Storage gebaseerde gegevens van het ene formaat naar het andere te transformeren, filter, aggregaat, enz.

### <a name="q-what-is-a-good-use-case-for-data-flow-in-synapse"></a>V: Wat is een goede use case voor dataflow in Synapse

A: Met gegevensstroom kunnen gegevenstechnici grafische logica voor gegevenstransformatie ontwikkelen zonder code te schrijven. De resulterende gegevensstromen worden uitgevoerd als activiteiten binnen Data Integration & Orchestration. Gegevensstroomactiviteiten kunnen worden geoperationaliseerd via bestaande plannings-, controle-, stroom- en bewakingsmogelijkheden.

## <a name="security-and-access"></a>Beveiliging en toegang

A: End-to-end single sign-on experience is een belangrijk authenticatieproces in Synapse Analytics. Het beheren en doorgeven van de identiteit via een volledige AAD-integratie is een must.

### <a name="q-how-do-i-get-access-to-files-and-folders-in-the-adlsg2"></a>V: Hoe krijg ik toegang tot bestanden en mappen in de ADLSg2

A: Toegang tot bestanden en mappen wordt momenteel beheerd via ADLSg2. Zie [Toegangsbeheer voor opslaggegevens voor Data Lake](../storage/blobs/data-lake-storage-access-control.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)voor meer informatie .

### <a name="q-can-i-use-third-party-business-intelligence-tools-to-access-azure-synapse-analytics"></a>V: Kan ik business intelligence-tools van derden gebruiken om toegang te krijgen tot Azure Synapse Analytics

A: Ja, u uw zakelijke toepassingen van derden, zoals Tableau en Power BI, gebruiken om verbinding te maken met SQL-pool en SQL on-demand. Spark ondersteunt IntelliJ.

## <a name="next-steps"></a>Volgende stappen

- [Een werkruimte maken](quickstart-create-workspace.md)
- [Synapse Studio gebruiken](quickstart-synapse-studio.md)
- [Een SQL-groep maken](quickstart-create-sql-pool.md)
- [SQL on-demand gebruiken](quickstart-sql-on-demand.md)
- [Een Apache Spark-pool maken](quickstart-create-apache-spark-pool.md) 
