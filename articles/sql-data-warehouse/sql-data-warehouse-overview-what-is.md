---
title: Wat is Azure Synapse Analytics (voorheen SQL DW)?
description: Azure Synapse Analytics (voorheen SQL DW) is een oneindige analyse service waarmee bedrijfs gegevensopslag en Big data-analyses worden gecombineerd.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 68d39b4f363794d50fd05c2067502fc55d5d0170
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153244"
---
# <a name="what-is-azure-synapse-analytics-formerly-sql-dw"></a>Wat is Azure Synapse Analytics (voorheen SQL DW)?

Azure Synapse is een oneindige analyseservice die bedrijfsgegevensopslag en big data-analyses combineert. Deze geeft u de vrijheid om op schaal gegevens op te vragen over uw voorwaarden, met behulp van serverloze on-demand of ingerichte resources. Azure Synapse brengt deze twee werelden samen met een uniforme ervaring om gegevens op te nemen, voor te bereiden, te beheren en te leveren voor direct BI en machine learning behoeften

Azure Synapse heeft vier onderdelen:
- SQL Analytics: volledige op T-SQL gebaseerde analyses – algemeen beschikbaar
    - SQL-groep (betalen per DWU-ingericht) 
    - SQL on-demand (betalen per TB verwerkte) – (preview-versie)
- Spark: diep geïntegreerde Apache Spark (preview-versie) 
- Gegevens integratie: hybride gegevens integratie (preview-versie)
- Studio: uniforme gebruikers ervaring.  (Preview-versie)

> [!NOTE]
> Om toegang te krijgen tot de preview-functies van Azure Synapse, kunt u [hier](https://aka.ms/synapsepreview)toegang aanvragen. Micro soft zal alle aanvragen sorteren en zo snel mogelijk reageren.

## <a name="sql-analytics-and-sql-pool-in-azure-synapse"></a>SQL-analyse en SQL-groep in azure Synapse

SQL Analytics verwijst naar de functies voor Enter prise data warehousing die algemeen beschikbaar zijn in azure Synapse. 

SQL-pool vertegenwoordigt een verzameling analytische resources die worden ingericht wanneer u SQL Analytics gebruikt. De grootte van de SQL-groep wordt bepaald door DWU (data warehousing units).

Importeer big data met eenvoudige [poly base](/sql/relational-databases/polybase/polybase-guide?view=sql-server-2017&viewFallbackFrom=azure-sqldw-latest) T-SQL-query's en gebruik vervolgens de kracht van MPP voor het uitvoeren van analyses met hoge prestaties. Wanneer u integreert en analyseert, wordt SQL Analytics de enige versie van waarheid die uw bedrijf kan tellen voor snellere en betrouwbaardere inzichten.  

## <a name="key-component-of-a-big-data-solution"></a>Belang rijk onderdeel van een big data oplossing

Data Warehousing is een belang rijk onderdeel van een end-to-end-oplossing op basis van een Cloud big data.

![Data Warehouse-oplossing](media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

In een cloudgegevensoplossing zijn gegevens opgenomen in big data-archieven uit verschillende bronnen. Nadat de gegevens zijn opgenomen in een big data-archief, worden ze met behulp van Hadoop-, Spark- en machine learning-algoritmen voorbereid en getraind. Wanneer de gegevens gereed zijn voor complexe analyse, gebruikt SQL Analytics poly Base om de big data-archieven op te vragen. Poly base gebruikt standaard T-SQL-query's om de gegevens in SQL Analytics-tabellen te brengen.
 
In SQL Analytics worden gegevens opgeslagen in relationele tabellen met kolom opslag. Deze indeling beperkt de kosten voor gegevensopslag aanzienlijk en verbetert de prestaties van query's. Zodra de gegevens zijn opgeslagen, kunt u op grote schaal analyses uitvoeren. Vergeleken met traditionele databasesystemen duurt het analyseren van query's seconden in plaats van minuten, of uren in plaats van dagen. 

De analyseresultaten kunnen worden verzonden naar wereldwijde rapportagedatabases of toepassingen. Bedrijfsanalisten kunnen vervolgens inzichten verkrijgen om goed gefundeerde zakelijke beslissingen te maken.

## <a name="next-steps"></a>Volgende stappen

- [Azure Synapse-architectuur](massively-parallel-processing-mpp-architecture.md) verkennen
- Snel [een SQL-groep maken](create-data-warehouse-portal.md)
- [Voorbeeld gegevens laden](sql-data-warehouse-load-sample-databases.md).
- [Video's](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse) verkennen

Of Bekijk enkele van deze andere Azure Synapse-resources.  
* [Blogs](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/) zoeken
* Een [functie aanvraag](https://feedback.azure.com/forums/307516-sql-data-warehouse) indienen
* [Een ondersteuningsticket maken](sql-data-warehouse-get-started-create-support-ticket.md)
* [MSDN-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse) zoeken
* [Stack overflow forum](https://stackoverflow.com/questions/tagged/azure-sqldw) zoeken