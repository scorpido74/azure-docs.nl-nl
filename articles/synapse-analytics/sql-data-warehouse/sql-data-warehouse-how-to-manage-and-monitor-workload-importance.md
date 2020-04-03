---
title: Het belang van workload beheren en bewaken
description: Meer informatie over het beheren en bewaken van het belang van aanvragenin Azure Synapse Analytics.
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
ms.openlocfilehash: 114f8d637a927a899807a676fb3e1b45f5c7687c
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585751"
---
# <a name="manage-and-monitor-workload-importance-in-azure-synapse-analytics"></a>Het belang van de werkbelasting beheren en bewaken in Azure Synapse Analytics

Het belang van Synapse SQL-aanvraagniveau in Azure Synapse beheren en bewaken met behulp van DMVs en catalogusweergaven.

## <a name="monitor-importance"></a>Belang bewaken

Controleer het belang met behulp van de nieuwe belangrijkheidskolom in de dynamische beheerweergave [sys.dm_pdw_exec_requests.](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest)
In de onderstaande bewakingsquery worden de tijd en begintijd voor query's weergegeven. Bekijk de tijd en starttijd voor verzenden, samen met het belang om te zien hoe belangrijk het plannen heeft beïnvloed.

```sql
SELECT s.login_name, r.status, r.importance, r.submit_time, r.start_time
  FROM sys.dm_pdw_exec_sessions s
  JOIN sys.dm_pdw_exec_requests r ON s.session_id = r.session_id
  WHERE r.resource_class is not null
ORDER BY r.start_time
```

Als u verder wilt kijken hoe query's worden gepland, gebruikt u de catalogusweergaven.

## <a name="manage-importance-with-catalog-views"></a>Belang beheren met catalogusweergaven

De sys.workload_management_workload_classifiers catalogusweergave bevat informatie over classificaties. Als u de door het systeem gedefinieerde classificaties die worden toegewezen aan resourceklassen, wilt uitsluiten, voert u de volgende code uit:

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

De catalogusweergave, [sys.workload_management_workload_classifier_details,](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?view=azure-sqldw-latest)bevat informatie over de parameters die worden gebruikt bij het maken van de classificatie.  De onderstaande query laat zien dat ExecReportsClassifier is gemaakt op de ```membername``` parameter voor waarden met ExecutiveReports:

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![queryresultaten](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

Om foutikale problemen te vereenvoudigen, raden we u aan toewijzingen voor resourceklassen te verwijderen terwijl u workloadclassificaties maakt. De onderstaande code retourneert bestaande lidmaatschappen van resourceklassen. Voer sp_droprolemember ```membername``` uit voor elke geretourneerde bronklasse.
Hieronder vindt u een voorbeeld van het controleren op bestaan voordat u een workload classifier laat vallen:

```sql
IF EXISTS (SELECT 1 FROM sys.workload_management_workload_classifiers WHERE name = 'ExecReportsClassifier')
  DROP WORKLOAD CLASSIFIER ExecReportsClassifier;
GO
```

## <a name="next-steps"></a>Volgende stappen
- Zie [Workloadclassification](sql-data-warehouse-workload-classification.md)voor meer informatie over classificatie.
- Zie [Werklastbelang](sql-data-warehouse-workload-importance.md) voor meer informatie over Belang

> [!div class="nextstepaction"]
> [Ga naar Het belang van workload configureren](sql-data-warehouse-how-to-configure-workload-importance.md)
