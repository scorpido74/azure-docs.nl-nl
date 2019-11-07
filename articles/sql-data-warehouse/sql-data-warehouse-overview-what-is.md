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
ms.openlocfilehash: 07bf3070e42af8a03ce1fd7ea4445fc76557e8a3
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73645518"
---
# <a name="what-is-azure-synapse-analytics-formerly-sql-dw"></a>Wat is Azure Synapse Analytics (voorheen SQL DW)?

Azure Synapse is een oneindige analyse service waarmee bedrijfs gegevensopslag en Big data-analyses worden gecombineerd. Het biedt u de vrijheid om gegevens op te vragen over uw voor waarden, met behulp van serverloze on-demand of ingerichte resources, op schaal. Azure Synapse brengt deze twee werelden samen met een uniforme ervaring om gegevens op te nemen, voor te bereiden, te beheren en te leveren voor direct BI en machine learning behoeften

Azure Synapse heeft vier onderdelen:
- SQL Analytics: volledige op T-SQL gebaseerde analyses – algemeen beschikbaar
    - SQL-groep (betalen per DWU-ingericht) 
    - SQL on-demand (betalen per TB verwerkte) – (preview-versie)
- Spark: diep geïntegreerde Apache Spark (preview-versie) 
- Gegevens integratie: hybride gegevens integratie (preview-versie)
- Studio: uniforme gebruikers ervaring.  (Preview)

> [!NOTE]
> Om toegang te krijgen tot de preview-functies van Azure Synapse, kunt u [hier](https://aka.ms/synapsepreview)toegang aanvragen. Micro soft zal alle aanvragen sorteren en zo snel mogelijk reageren.

## <a name="sql-analytics-and-sql-pool-in-azure-synapse"></a>SQL-analyse en SQL-groep in azure Synapse

SQL Analytics verwijst naar de functies voor Enter prise data warehousing die algemeen beschikbaar zijn met Azure Synapse. 

SQL-pool vertegenwoordigt een verzameling analytische resources die worden ingericht wanneer u SQL Analytics gebruikt. De grootte van de SQL-groep wordt bepaald door DWU (data warehousing units).

Importeer big data met eenvoudige [poly base](/sql/relational-databases/polybase/polybase-guide?view=sql-server-2017&viewFallbackFrom=azure-sqldw-latest) T-SQL-query's en gebruik vervolgens de kracht van MPP voor het uitvoeren van analyses met hoge prestaties. Wanneer u integreert en analyseert, wordt SQL Analytics de enige versie van waarheid die uw bedrijf kan tellen voor snellere en betrouwbaardere inzichten.  

## <a name="key-component-of-a-big-data-solution"></a>Belang rijk onderdeel van een big data oplossing

Data Warehousing is een belang rijk onderdeel van een end-to-end-oplossing op basis van een Cloud big data.

![Data Warehouse-oplossing](media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

In een cloudgegevensoplossing zijn gegevens opgenomen in big data-archieven uit verschillende bronnen. Nadat de gegevens zijn opgenomen in een big data-archief, worden ze met behulp van Hadoop-, Spark- en machine learning-algoritmen voorbereid en getraind. Wanneer de gegevens gereed zijn voor complexe analyse, gebruikt SQL Analytics poly Base om de big data-archieven op te vragen. Poly base gebruikt standaard T-SQL-query's om de gegevens in SQL Analytics-tabellen te brengen.
 
In SQL Analytics worden gegevens opgeslagen in relationele tabellen met kolom opslag. Deze indeling beperkt de kosten voor gegevensopslag aanzienlijk en verbetert de prestaties van query's. Zodra de gegevens zijn opgeslagen, kunt u op grote schaal analyses uitvoeren. Vergeleken met traditionele databasesystemen duurt het analyseren van query's seconden in plaats van minuten, of uren in plaats van dagen. 

De analyseresultaten kunnen worden verzonden naar wereldwijde rapportagedatabases of toepassingen. Bedrijfsanalisten kunnen vervolgens inzichten verkrijgen om goed gefundeerde zakelijke beslissingen te maken.

## <a name="next-steps"></a>Volgende stappen

- [Azure Synapse-architectuur](/azure/sql-data-warehouse/massively-parallel-processing-mpp-architecture) verkennen
- Snel [een SQL-groep maken](create-data-warehouse-portal.md)
- [Voorbeeld gegevens laden][load sample data].
- [Video's](/azure/sql-data-warehouse/sql-data-warehouse-videos) verkennen

Of Bekijk enkele van deze andere Azure Synapse-resources.  
* [Blogs] zoeken
* Een [Functieverzoeken] indienen
* Blogs over het [Teamblogs met adviezen voor klanten] zoeken
* [Een ondersteuningsticket maken]
* [MSDN-forum] zoeken
* [Stack Overflow-forum] zoeken


<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Een ondersteuningsticket maken]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a data warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[Azure Synapse Analytics solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Teamblogs met adviezen voor klanten]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Functieverzoeken]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN-forum]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSQLDataWarehouse
[Stack Overflow-forum]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for Azure Synapse Analytics]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/
