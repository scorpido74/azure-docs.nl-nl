---
title: Wat is Azure Synapse Analytics (voorheen SQL DW)?
description: Azure Synapse Analytics (voorheen SQL DW) is een onbeperkte analyseservice die datawarehousing voor ondernemingen en big data-analyses combineert.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 27311270c1383a54192d072d0e152c7cf1a58225
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "85200950"
---
# <a name="what-is-azure-synapse-analytics-formerly-sql-dw"></a>Wat is Azure Synapse Analytics (formerly SQL DW)?

> [!NOTE] 
>Verken de [documentatie over Azure Synapse (preview van werkruimten)](../overview-what-is.md).
>

Azure Synapse is een analyseservice die datawarehousing voor ondernemingen en big data-analyses combineert. Deze geeft u de vrijheid om op schaal gegevens op te vragen over uw voorwaarden, met behulp van serverloze on-demand of ingerichte resources. Azure Synapse brengt deze twee werelden samen met een uniforme ervaring om gegevens op te nemen, voor te bereiden, te beheren en te leveren voor directe BI- en machine learning-behoeften.

Azure Synapse heeft vier onderdelen:

- Synapse SQL: Volledige op T-SQL gebaseerde analyse – Algemeen beschikbaar
  - SQL-pool (ingericht voor betalen per DWU)
  - SQL op aanvraag (betalen per verwerkte TB) (preview)
- Spark: Diep geïntegreerde Apache Spark (preview)
- Synapse-pijplijnen: Hybride gegevensintegratie (preview)
- Studio: Uniforme gebruikerservaring. (preview)

## <a name="synapse-sql-pool-in-azure-synapse"></a>Azure Synapse SQL-pool in Azure Synapse

Synapse SQL-pool verwijst naar de functies voor datawarehousing voor ondernemingen, die algemeen beschikbaar zijn in Azure Synapse.

SQL-pool vertegenwoordigt een verzameling analytische resources die worden ingericht bij het gebruik van Synapse SQL. De grootte van de SQL-pool wordt bepaald door DWU’s (datawarehousingeenheden).

Importeer big data met eenvoudige [PolyBase](/sql/relational-databases/polybase/polybase-guide?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) T-SQL-query's en gebruik vervolgens de kracht van MPP om analyses met hoge prestaties uit te voeren. Door te integreren en analyseren wordt Synapse SQL-pool de centrale bron voor waarop uw bedrijf kan rekenen voor het verkrijgen van snellere en meer robuuste inzichten.  

## <a name="key-component-of-a-big-data-solution"></a>Belangrijk onderdeel van een big data-oplossing

Datawarehousing is een belangrijk onderdeel van een end-to-end big data-oplossing in de cloud.

![Data Warehouse-oplossing](./media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png)

In een cloudgegevensoplossing zijn gegevens opgenomen in big data-archieven uit verschillende bronnen. Nadat de gegevens zijn opgenomen in een big data-archief, worden ze met behulp van Hadoop-, Spark- en machine learning-algoritmen voorbereid en getraind. Wanneer de gegevens klaar zijn voor complexe analyse, maakt Synapse SQL-pool gebruik van PolyBase om query's uit te voeren op de big data-archieven. PolyBase gebruikt standaard T-SQL-query's om de gegevens op te slaan in Synapse SQL-pool.

In Synapse SQL-pool worden gegevens opgeslagen in relationele tabellen met opslag in kolommen. Deze indeling beperkt de kosten voor gegevensopslag aanzienlijk en verbetert de prestaties van query's. Wanneer de gegevens zijn opgeslagen, kunt u op grote schaal analyses uitvoeren. Vergeleken met traditionele databasesystemen duurt het analyseren van query's seconden in plaats van minuten, of uren in plaats van dagen.

De analyseresultaten kunnen worden verzonden naar wereldwijde rapportagedatabases of toepassingen. Bedrijfsanalisten kunnen vervolgens inzichten verkrijgen om goed gefundeerde zakelijke beslissingen te maken.

## <a name="next-steps"></a>Volgende stappen

- [Azure Synapse-architectuur](massively-parallel-processing-mpp-architecture.md) verkennen
- Snel [een SQL-groep maken](create-data-warehouse-portal.md)
- [Voorbeeldgegevens laden](load-data-from-azure-blob-storage-using-polybase.md).
- [Video's verkennen](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)

Of bekijk enkele andere Azure Synapse-resources.

- Zoeken in [Blogs](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [Functieaanvragen](https://feedback.azure.com/forums/307516-sql-data-warehouse) indienen
- [Een ondersteuningsticket maken](sql-data-warehouse-get-started-create-support-ticket.md)
- Zoeken op de [Microsoft Q&A-vragenpagina](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html)
- Zoeken in het [Stack Overflow-forum](https://stackoverflow.com/questions/tagged/azure-sqldw)
