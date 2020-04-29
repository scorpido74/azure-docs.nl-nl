---
title: Het belang van workload beheren en bewaken
description: Meer informatie over het beheren en bewaken van de urgentie van het aanvraag niveau in azure Synapse Analytics.
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
ms.openlocfilehash: 3efd8a776542616a9ceefba331b06406540905a8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80633325"
---
# <a name="manage-and-monitor-workload-importance-in-azure-synapse-analytics"></a>De urgentie van werk belastingen in azure Synapse Analytics beheren en bewaken

Synapse SQL-aanvraag niveau urgentie in azure Synapse beheren en bewaken met behulp van Dmv's-en catalogus weergaven.

## <a name="monitor-importance"></a>Controle prioriteit

Controleer het belang van de kolom nieuwe urgentie in de weer gave [sys. dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) Dynamic Management.
De onderstaande bewakings query toont de verzend tijd en start tijd voor query's. Controleer de verzend tijd en de begin tijd, samen met het belang om te zien hoe belang rijke planningen worden beïnvloed.

```sql
SELECT s.login_name, r.status, r.importance, r.submit_time, r.start_time
  FROM sys.dm_pdw_exec_sessions s
  JOIN sys.dm_pdw_exec_requests r ON s.session_id = r.session_id
  WHERE r.resource_class is not null
ORDER BY r.start_time
```

Gebruik de catalogus weergaven om verder te kijken hoe query's worden gepland.

## <a name="manage-importance-with-catalog-views"></a>Urgentie beheren met catalogus weergaven

De catalogus weergave sys. workload_management_workload_classifiers bevat informatie over classificaties. Voer de volgende code uit om de door het systeem gedefinieerde classificaties uit te sluiten die worden toegewezen aan resource klassen:

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

De catalogus weergave, [sys. workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), bevat informatie over de para meters die worden gebruikt bij het maken van de classificatie.  De onderstaande query laat zien dat ExecReportsClassifier is gemaakt op ```membername``` de para meter voor waarden met ExecutiveReports:

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![query resultaten](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

Om het oplossen van problemen met een fout te vereenvoudigen, raden we u aan om resource klasse-roltoewijzingen te verwijderen tijdens het maken van de werk belasting classificaties. De onderstaande code retourneert bestaande lidmaatschappen van resource klassen. Voer sp_droprolemember uit voor ```membername``` elk resultaat van de bijbehorende resource klasse.
Hieronder ziet u een voor beeld van het controleren op aanwezigheid voordat u de classificatie van een werk belasting verwijdert:

```sql
IF EXISTS (SELECT 1 FROM sys.workload_management_workload_classifiers WHERE name = 'ExecReportsClassifier')
  DROP WORKLOAD CLASSIFIER ExecReportsClassifier;
GO
```

## <a name="next-steps"></a>Volgende stappen

- Zie [workload classificatie](sql-data-warehouse-workload-classification.md)voor meer informatie over classificatie.
- Zie [urgentie van werk belasting](sql-data-warehouse-workload-importance.md) voor meer informatie over belang rijkheid

> [!div class="nextstepaction"]
> [Ga naar urgentie van werk belasting configureren](sql-data-warehouse-how-to-configure-workload-importance.md)
