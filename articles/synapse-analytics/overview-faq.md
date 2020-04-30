---
title: Veelgestelde vragen-Azure Synapse Analytics
description: Veelgestelde vragen over Azure Synapse Analytics
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 00a98fdeb4b8febbcb9d1183fabffc7298cc9e4c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81770730"
---
# <a name="azure-synapse-analytics-workspace-preview-frequently-asked-questions"></a>Veelgestelde vragen over Azure Synapse Analytics (werk ruimte preview)

In deze hand leiding vindt u de veelgestelde vragen over Synapse Analytics.

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="general"></a>Algemeen

### <a name="q-what-is-azure-synapse-analytics"></a>V: wat is Azure Synapse Analytics

A: Azure Synapse is een geïntegreerd gegevens platform voor BI, AI en continue Intelligence. Er worden verschillende analyse runtimes, zoals SQL en Spark, verbonden via één platform dat een uniforme manier biedt om:

- Beveilig uw analyse bronnen, met inbegrip van het netwerk, het beheren van de toegang tot eenmalige aanmelding voor de groeps-, gegevens-en ontwikkelings artefacten.
- U kunt eenvoudig gebeurtenissen in uw werkruimte activiteiten controleren en snel optimaliseren, reageren en fouten opsporen die zich op elke laag voordoen.
- Uw meta gegevens voor alle engines beheren. Maak een Spark-tabel en deze is automatisch beschikbaar in uw Azure Synapse-data bases.
- Interactie met de gegevens via een uniforme gebruikers ervaring. Synapse Studio brengt Big Data-ontwikkel aars, gegevens technici, Dba's, gegevens analisten en gegevens wetenschappers op hetzelfde platform.

### <a name="q-how-do-i-get-started-with-azure-synapse-analytics"></a>V: Hoe kan ik aan de slag met Azure Synapse Analytics

A: als u Azure Synapse Analytics wilt gaan gebruiken, maakt u een [Synapse-werk ruimte](https://portal.azure.com) (dit is gratis!) en maakt u de resources die u wilt maken onder die werk ruimte. U kunt een van onze zelf studies volgen, zoals [het maken van een Synapse SQL-groep](quickstart-create-sql-pool.md) of [het maken van een werk ruimte](quickstart-create-workspace.md), zodat u een eenvoudige use-case doorloopt. 

U kunt ook voor beelden van notitie blokken en SQL-scripts vinden in onze [opslag plaats](https://github.com/Azure/azure-synapse-analytics/tree/master/samples). Als u verbinding moet maken met een open bare gegevensset, maakt u een nieuwe gekoppelde service met de volgende kenmerken:

- azure_storage_account_name = "azureopendatastorage"
- azure_storage_sas_token = "" (schrijven **""**)

### <a name="q-what-are-the-main-components-of-azure-synapse-analytics"></a>V: wat zijn de belangrijkste onderdelen van Azure Synapse Analytics

A: Azure Synapse heeft de volgende mogelijkheden:

- Analyse mogelijkheden worden aangeboden via SQL-groep of SQL on-demand (preview) (Serverloze).
- Apache Spark pool (preview) met volledige ondersteuning voor scala, Python, SparkSQL en C #
- Data Flow biedt een code-Free big data Transformation-ervaring
- Gegevens integratie & indeling om uw gegevens te integreren en al uw code ontwikkeling operationeel maken
- Studio om toegang te krijgen tot al deze mogelijkheden via één webinterface

### <a name="q-how-does-azure-synapse-analytics-relate-to-azure-sql-data-warehouse"></a>V: wat is de relatie tussen Azure Synapse Analytics en Azure SQL Data Warehouse

A: Azure Synapse Analytics is een evolutie van Azure SQL Data Warehouse in een analyse platform, waaronder SQL-groep als de Data Warehouse-oplossing. Dit platform combineert gegevens exploratie, opname, trans formatie, voor bereiding en een voor analyse-laag.

## <a name="use-cases"></a>Gebruiksvoorbeelden

### <a name="q-what-is-a-good-use-case-for-synapse-sql-pool"></a>V: wat is een goede use-case voor Synapse SQL-pool

A: de SQL-groep is de kern van uw data warehouse-behoeften. Het is de toonaangevende data warehouse-oplossing in [prijs-prestatie verhouding](https://azure.microsoft.com/services/sql-data-warehouse/compare/). SQL pool is de toonaangevende oplossing voor Cloud Data Warehouse, omdat u het volgende kunt doen:

- een grote en gemengde werk belasting hebben zonder invloed op de prestaties dankzij een hoge gelijktijdigheids-en werkbelasting isolatie
- Beveilig uw gegevens eenvoudig via geavanceerde functies, variërend van netwerk beveiliging tot nauw keurige toegang
- Profiteer van een breed scala aan eco-systemen

### <a name="q-what-is-a-good-use-case-for-spark-in-synapse"></a>V: wat is een goede use-case voor Spark in Synapse

A: onze eerste doel stelling is het bieden van een fantastische data engineering-ervaring voor het transformeren van gegevens over de meer in batch of stream. Dankzij de naadloze en eenvoudige integratie met onze gegevens indeling wordt de uitoefening van uw ontwikkelings werk duidelijk.

Een ander use-case voor Spark is voor een gegevens wetenschapper:

- een functie uitpakken
- gegevens verkennen
- een model trainen

### <a name="q-what-is-a-good-use-case-for-sql-on-demand-in-synapse"></a>V: wat is een goed gebruiks voorbeeld voor SQL op aanvraag in Synapse

A: SQL on-demand is een query service over de gegevens in uw data Lake. Hiermee kunt u toegang tot al uw gegevens democratisch door een bekende T-SQL-syntaxis te bieden voor het opvragen van gegevens, zonder dat u gegevens naar een gespecialiseerde Store hoeft te kopiëren of te laden.

Voor beelden van use-cases zijn:

- basis detectie en-ontdekking: biedt gegevens analisten, opkomende gegevens wetenschappers en gegevens technici met een eenvoudig pad naar het eerste inzicht in gegevens in hun data Lake met schema op Lees-T-SQL-query's
- logische data warehouse: gegevens analisten kunnen een volledige duidelijkheid van de T-SQL-taal uitvoeren om de gegevens die zich in Azure Storage bevinden, rechtstreeks te doorzoeken en te analyseren en vertrouwde BI-hulpprogram ma's (zoals Azure-analyses Services, Power BI Premium enz.) te gebruiken om Dash boards te vernieuwen door Starlight query query's opnieuw uit te voeren
- "ETL van één query" Hiermee kunnen gegevens technici Azure Storage op basis van gegevens van de ene indeling naar een andere omzetten, filteren, aggregeren, enzovoort, op zeer parallelle verwerkings wijze de query resultaten persistent maken voor Azure Storage en deze onmiddellijk beschikbaar stellen voor verdere verwerking in Starlight-query of andere interessante Services

### <a name="q-what-is-a-good-use-case-for-data-flow-in-synapse"></a>V: wat is een goed gebruiks voorbeeld voor de gegevens stroom in Synapse

A: met gegevens stroom kunnen data engineers grafische gegevens transformatie logica ontwikkelen zonder code te schrijven. De resulterende gegevens stromen worden uitgevoerd als activiteiten binnen de gegevens integratie & indeling. Activiteiten voor gegevens stromen kunnen worden operationeel via bestaande mogelijkheden voor planning, controle, stroom en controle.

## <a name="security-and-access"></a>Beveiliging en toegang

A: end-to-end-ervaring voor eenmalige aanmelding is een belang rijk verificatie proces in Synapse Analytics. Het beheren en door geven van de identiteit door middel van een volledige AAD-integratie is een moet.

### <a name="q-how-do-i-get-access-to-files-and-folders-in-the-adlsg2"></a>V: Hoe kan ik toegang krijgen tot bestanden en mappen in de ADLSg2

A: de toegang tot bestanden en mappen wordt momenteel beheerd via ADLSg2. Zie [Data Lake Storage Access Control](../storage/blobs/data-lake-storage-access-control.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)(Engelstalig) voor meer informatie.

### <a name="q-can-i-use-third-party-business-intelligence-tools-to-access-azure-synapse-analytics"></a>V: kan ik business intelligence-hulpprogram ma's van derden gebruiken om toegang te krijgen tot Azure Synapse Analytics

A: Ja, u kunt uw zakelijke toepassingen van derden, zoals tableau en Power BI, gebruiken om verbinding te maken met de SQL-groep en SQL op aanvraag. Spark ondersteunt IntelliJ.

## <a name="next-steps"></a>Volgende stappen

- [Een werkruimte maken](quickstart-create-workspace.md)
- [Synapse Studio gebruiken](quickstart-synapse-studio.md)
- [Een SQL-groep maken](quickstart-create-sql-pool.md)
- [SQL on-demand gebruiken](quickstart-sql-on-demand.md)
- [Een Apache Spark groep maken](quickstart-create-apache-spark-pool.md) 
