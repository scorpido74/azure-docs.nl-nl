---
title: Wat is Azure Synapse Analytics (voorheen SQL DW)?
description: Azure Synapse Analytics (voorheen SQL DW) is een oneindige analyse service waarmee bedrijfs gegevensopslag en Big data-analyses worden gecombineerd.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 27e6c7beb8c88a29b90b30e0c77e35e493f2480a
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690910"
---
# <a name="what-is-azure-synapse-analytics-formerly-sql-dw"></a>Wat is Azure Synapse Analytics (voorheen SQL DW)?

> [!NOTE] 
> Probeer de nieuwste functies van Azure Synapse, zoals werk ruimten, Spark, SQL op aanvraag en de geïntegreerde Synapse Studio-ervaring door [toegang aan te vragen voor Azure Synapse (voor beeld van werk ruimten)](https://aka.ms/synapsepreview). 
>
>Verken de [documentatie voor Azure Synapse (voor beelden)](../overview-what-is.md).

Azure Synapse is een analyse service waarmee bedrijfs gegevensopslag en Big data-analyses worden gecombineerd. Deze geeft u de vrijheid om op schaal gegevens op te vragen over uw voorwaarden, met behulp van serverloze on-demand of ingerichte resources. Azure Synapse brengt deze twee werelden samen met een uniforme ervaring om gegevens op te nemen, voor te bereiden, te beheren en te verwerken voor direct BI en machine learning behoeften.

Azure Synapse heeft vier onderdelen:

- Synapse SQL: volledige op T-SQL gebaseerde analyses – algemeen beschikbaar
  - SQL-groep (betalen per DWU-ingericht)
  - SQL on-demand (betalen per TB verwerkte) – (preview-versie)
- Spark: diep geïntegreerde Apache Spark (preview-versie)
- Synapse-pijp lijnen: hybride gegevens integratie (preview-versie)
- Studio: uniforme gebruikers ervaring.  (Preview)



## <a name="synapse-sql-pool-in-azure-synapse"></a>Synapse SQL-groep in azure Synapse

Synapse SQL pool verwijst naar de functies voor Enter prise data warehousing die algemeen beschikbaar zijn in azure Synapse.

SQL-pool vertegenwoordigt een verzameling analytische resources die worden ingericht wanneer Synapse SQL wordt gebruikt. De grootte van de SQL-groep wordt bepaald door DWU (data warehousing units).

Importeer big data met eenvoudige [poly base](/sql/relational-databases/polybase/polybase-guide?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) T-SQL-query's en gebruik vervolgens de kracht van MPP voor het uitvoeren van analyses met hoge prestaties. Als u integreert en analyseert, wordt de Synapse-SQL-pool de enige versie van waarheid die uw bedrijf kan tellen voor snellere en betrouwbaardere inzichten.  

## <a name="key-component-of-a-big-data-solution"></a>Belang rijk onderdeel van een big data oplossing

Data Warehousing is een belang rijk onderdeel van een end-to-end-oplossing op basis van een Cloud big data.

![Data Warehouse-oplossing](./media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png)

In een cloudgegevensoplossing zijn gegevens opgenomen in big data-archieven uit verschillende bronnen. Nadat de gegevens zijn opgenomen in een big data-archief, worden ze met behulp van Hadoop-, Spark- en machine learning-algoritmen voorbereid en getraind. Wanneer de gegevens gereed zijn voor complexe analyse, gebruikt Synapse SQL-pool poly Base om de big data-archieven op te vragen. Poly base gebruikt standaard T-SQL-query's om de gegevens in Synapse SQL-pool tabellen te brengen.

Met de SQL-groep Synapse worden gegevens opgeslagen in relationele tabellen met kolom opslag. Deze indeling beperkt de kosten voor gegevensopslag aanzienlijk en verbetert de prestaties van query's. Nadat de gegevens zijn opgeslagen, kunt u analyses op grote schaal uitvoeren. Vergeleken met traditionele databasesystemen duurt het analyseren van query's seconden in plaats van minuten, of uren in plaats van dagen.

De analyseresultaten kunnen worden verzonden naar wereldwijde rapportagedatabases of toepassingen. Bedrijfsanalisten kunnen vervolgens inzichten verkrijgen om goed gefundeerde zakelijke beslissingen te maken.

## <a name="next-steps"></a>Volgende stappen

- [Azure Synapse-architectuur](massively-parallel-processing-mpp-architecture.md) verkennen
- Snel [een SQL-groep maken](create-data-warehouse-portal.md)
- [Voorbeeld gegevens laden](load-data-from-azure-blob-storage-using-polybase.md).
- [Video's](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse) verkennen

Of Bekijk enkele van deze andere Azure Synapse-resources.

- [Blogs](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/) zoeken
- Een [functie aanvraag](https://feedback.azure.com/forums/307516-sql-data-warehouse) indienen
- [Een ondersteuningsticket maken](sql-data-warehouse-get-started-create-support-ticket.md)
- [MSDN-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse) zoeken
- [Stack overflow forum](https://stackoverflow.com/questions/tagged/azure-sqldw) zoeken
