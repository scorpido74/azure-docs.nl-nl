---
title: Het belang van workload configureren
description: Meer informatie over het instellen van de urgentie van het aanvraag niveau in azure Synapse Analytics.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload-management
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 8b2a4333717938edf9f3039e29e8df88cece7cc1
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78196794"
---
# <a name="configure-workload-importance-in-azure-synapse-analytics"></a>Urgentie van werk belasting configureren in azure Synapse Analytics

Als u de urgentie van SQL Analytics voor Azure Synapse instelt, kunt u de planning van query's beïnvloeden. Query's met een hogere urgentie worden gepland om te worden uitgevoerd vóór query's met een lagere urgentie. Als u belang rijk aan query's wilt toewijzen, moet u een classificatie voor de werk belasting maken.

## <a name="create-a-workload-classifier-with-importance"></a>Een classificatie van een werk belasting met urgentie maken

Vaak bevat een scenario voor een Data Warehouse gebruikers die hun query's nodig hebben om snel uit te voeren.  De gebruiker kan leidinggevenden zijn van het bedrijf waarvoor rapporten moeten worden uitgevoerd of de gebruiker kan worden uitgevoerd als een adhoc-query. U maakt een classificatie van de werk belasting om belang rijk aan een query toe te wijzen.  De volgende voor beelden gebruiken de nieuwe [classificatie syntaxis CREATE workload](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) om twee classificaties te maken.  Lidnaam kan één gebruiker of een groep zijn. Afzonderlijke gebruikers classificaties hebben voor rang op classificaties van rollen. Voer de volgende opdracht uit om bestaande Data Warehouse-gebruikers te zoeken:

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
