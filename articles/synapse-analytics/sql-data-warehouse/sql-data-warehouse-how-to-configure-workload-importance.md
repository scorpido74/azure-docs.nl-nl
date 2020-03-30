---
title: Het belang van workload configureren
description: Meer informatie over het instellen van het belang van het aanvraagniveau in Azure Synapse Analytics.
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
ms.openlocfilehash: 85fa6a989ae1a6214392914ffd46cd12af27a8d2
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350481"
---
# <a name="configure-workload-importance-in-azure-synapse-analytics"></a>Het werkgebied configureren in Azure Synapse Analytics

Als u belangrijk bent in SQL Analytics voor Azure Synapse, u de planning van query's beïnvloeden. Query's met een groter belang worden gepland om uit te voeren vóór query's met een lager belang. Als u belangrijk wilt zijn voor query's, moet u een classificatie voor werkbelasting maken.

## <a name="create-a-workload-classifier-with-importance"></a>Een workloadclassificatie maken met belang

Vaak heb je in een datawarehousescenario gebruikers die hun query's snel moeten uitvoeren.  De gebruiker kan leidinggevenden van het bedrijf zijn die rapporten moeten uitvoeren of de gebruiker kan een analist zijn die een adhocquery uitvoert. U maakt een classificatie voor werkbelasting om belang toe te wijzen aan een query.  In de onderstaande voorbeelden wordt de syntaxis van de classificatie [voor de werkbelasting gemaakt](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) om twee classificaties te maken.  De naam van het lid kan één gebruiker of een groep zijn. Individuele gebruikersclassificaties hebben voorrang op rolclassificaties. Voer het als u bestaande gebruikers van het gegevensmagazijn wilt vinden:

```sql
Select name from sys.sysusers
```

Ga als beste de geclassificeerde workload voor een gebruiker met een hoger belang:

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name'  
         ,IMPORTANCE     =  above_normal);  

```

Ga als u een classificatie voor werkbelasting maakt voor een gebruiker die adhocquery's met een lager belang uitvoert:  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name'  
         ,IMPORTANCE     =  below_normal);  
```

## <a name="next-steps"></a>Volgende stappen
- Zie Workloadclassification voor meer informatie over [workloadmanagement](sql-data-warehouse-workload-classification.md)
- Zie [Werklastbelang](sql-data-warehouse-workload-importance.md) voor meer informatie over Belang

> [!div class="nextstepaction"]
> [Ga naar Het belang van de werkbelasting beheren en bewaken](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
