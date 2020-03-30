---
title: Wat is Azure Synapse Analytics (voorheen SQL DW)?
description: Azure Synapse Analytics (voorheen SQL DW) is een onbegrensde analyseservice die enterprise data warehousing en Big Data analytics samenbrengt.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: a118d028cc85eb858dd0dc1fa6d5d2268f7db43b
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350409"
---
# <a name="what-is-azure-synapse-analytics-formerly-sql-dw"></a>Wat is Azure Synapse Analytics (voorheen SQL DW)?

Azure Synapse is een oneindige analyseservice die bedrijfsgegevensopslag en big data-analyses combineert. Deze geeft u de vrijheid om op schaal gegevens op te vragen over uw voorwaarden, met behulp van serverloze on-demand of ingerichte resources. Azure Synapse brengt deze twee werelden samen met een uniforme ervaring om gegevens in te nemen, voor te bereiden, te beheren en te leveren voor onmiddellijke BI- en machine learning-behoeften

Azure Synapse heeft vier componenten:
- SQL Analytics: Complete T-SQL based analytics – Algemeen beschikbaar
    - SQL-pool (pay per DWU-voorziening) 
    - SQL on-demand (pay per TB processed) – (Preview)
- Spark: Diep geïntegreerde Apache Spark (Preview) 
- Gegevensintegratie: hybride gegevensintegratie (Preview)
- Studio: Uniforme gebruikerservaring.  (Preview)

> [!NOTE]
> Als u toegang wilt krijgen tot de preview-functies van Azure Synapse, vraagt u [hier](https://aka.ms/synapsepreview)toegang aan. Microsoft zal alle verzoeken triage en zo snel mogelijk reageren.

## <a name="sql-analytics-and-sql-pool-in-azure-synapse"></a>SQL Analytics en SQL-pool in Azure Synapse

SQL Analytics verwijst naar de enterprise data warehousing-functies die algemeen beschikbaar zijn in Azure Synapse. 

SQL-groep vertegenwoordigt een verzameling analytische bronnen die worden ingericht bij het gebruik van SQL Analytics. De grootte van sql-pool wordt bepaald door Data Warehousing Units (DWU).

Importeer big data met eenvoudige [PolyBase](/sql/relational-databases/polybase/polybase-guide?view=sql-server-2017&viewFallbackFrom=azure-sqldw-latest) T-SQL-query's en gebruik vervolgens de kracht van MPP om krachtige analyses uit te voeren. Terwijl u integreert en analyseert, wordt SQL Analytics de enige versie van waarheid waarop uw bedrijf kan rekenen op snellere en robuustere inzichten.  

## <a name="key-component-of-a-big-data-solution"></a>Belangrijkste onderdeel van een big data-oplossing

Data warehousing is een belangrijk onderdeel van een cloudgebaseerde, end-to-end big data-oplossing.

![Data Warehouse-oplossing](./media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

In een cloudgegevensoplossing zijn gegevens opgenomen in big data-archieven uit verschillende bronnen. Nadat de gegevens zijn opgenomen in een big data-archief, worden ze met behulp van Hadoop-, Spark- en machine learning-algoritmen voorbereid en getraind. Wanneer de gegevens klaar zijn voor complexe analyse, gebruikt SQL Analytics PolyBase om de big data-opslag te doorzoeken. PolyBase gebruikt standaard T-SQL-query's om de gegevens in SQL Analytics-tabellen te zetten.
 
SQL Analytics slaat gegevens op in relationele tabellen met kolomopslag. Deze indeling beperkt de kosten voor gegevensopslag aanzienlijk en verbetert de prestaties van query's. Nadat de gegevens zijn opgeslagen, kunt u analyses op grote schaal uitvoeren. Vergeleken met traditionele databasesystemen duurt het analyseren van query's seconden in plaats van minuten, of uren in plaats van dagen. 

De analyseresultaten kunnen worden verzonden naar wereldwijde rapportagedatabases of toepassingen. Bedrijfsanalisten kunnen vervolgens inzichten verkrijgen om goed gefundeerde zakelijke beslissingen te maken.

## <a name="next-steps"></a>Volgende stappen

- [Azure Synapse-architectuur verkennen](massively-parallel-processing-mpp-architecture.md)
- Snel [een SQL-groep maken](create-data-warehouse-portal.md)
- [Voorbeeldgegevens laden](load-data-from-azure-blob-storage-using-polybase.md).
- [Video's verkennen](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)

Of bekijk een aantal van deze andere Azure Synapse-bronnen.  
* [Zoekblogs](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* Een [functieaanvraag indienen](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [Een ondersteuningsticket maken](sql-data-warehouse-get-started-create-support-ticket.md)
* Zoek [MSDN-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse)
* Zoek [Stack Overflow forum](https://stackoverflow.com/questions/tagged/azure-sqldw)
