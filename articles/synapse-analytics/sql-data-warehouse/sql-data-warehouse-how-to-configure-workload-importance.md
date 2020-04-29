---
title: Het belang van workload configureren
description: Meer informatie over het instellen van de urgentie van het aanvraag niveau in azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 0ab7b8be8780f7edb2734d99587bc7709ced9436
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80633351"
---
# <a name="configure-workload-importance-in-azure-synapse-analytics"></a>Urgentie van werk belasting configureren in azure Synapse Analytics

Als u de urgentie instelt in Synapse SQL voor Azure Synapse, kunt u de planning van query's beïnvloeden. Query's met een hogere urgentie worden gepland om te worden uitgevoerd vóór query's met een lagere urgentie. Als u belang rijk aan query's wilt toewijzen, moet u een classificatie voor de werk belasting maken.

## <a name="create-a-workload-classifier-with-importance"></a>Een classificatie van een werk belasting met urgentie maken

Vaak bevat een scenario voor een Data Warehouse gebruikers die hun query's nodig hebben om snel uit te voeren.  De gebruiker kan leidinggevenden zijn van het bedrijf waarvoor rapporten moeten worden uitgevoerd of de gebruiker kan worden uitgevoerd als een adhoc-query. U maakt een classificatie van de werk belasting om belang rijk aan een query toe te wijzen.  De volgende voor beelden gebruiken de nieuwe [classificatie syntaxis CREATE workload](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) om twee classificaties te maken. `Membername`Dit kan één gebruiker of een groep zijn. Afzonderlijke gebruikers classificaties hebben voor rang op classificaties van rollen. Voer de volgende opdracht uit om bestaande Data Warehouse-gebruikers te zoeken:

```sql
Select name from sys.sysusers
```

Voor het maken van een classificatie van een werk belasting voor een gebruiker met een hogere urgentie:

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name'  
         ,IMPORTANCE     =  above_normal);  

```

Voor het maken van een classificatie van werk belasting voor een gebruiker die adhoc-query's uitvoert met een lagere urgentie:  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name'  
         ,IMPORTANCE     =  below_normal);  
```

## <a name="next-steps"></a>Volgende stappen

- Zie [workload-classificatie](sql-data-warehouse-workload-classification.md) voor meer informatie over workload Management
- Zie [urgentie van werk belasting](sql-data-warehouse-workload-importance.md) voor meer informatie over belang rijkheid

> [!div class="nextstepaction"]
> [Ga naar de urgentie van het workload beheren en bewaken](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
