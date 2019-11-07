---
title: Het belang van workload beheren en bewaken
description: Meer informatie over het beheren en controleren van de urgentie van het aanvraag niveau in Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload-management
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: ee9acb873c5118733de142045457028c3f4d5f61
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692705"
---
# <a name="manage-and-monitor-workload-importance-in-azure-sql-data-warehouse"></a>De urgentie van werk belastingen in Azure SQL Data Warehouse beheren en bewaken

De urgentie van het aanvraag niveau beheren en bewaken in Azure SQL Data Warehouse met behulp van Dmv's en catalogus weergaven.

## <a name="monitor-importance"></a>Controle prioriteit

Bewaak de urgentie met behulp van de kolom nieuwe urgentie in de dynamische beheer weergave [sys. DM _pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest) .
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

De catalogus weergave sys. workload_management_workload_classifiers bevat informatie over classificaties in uw Azure SQL Data Warehouse-exemplaar. Voer de volgende code uit om de door het systeem gedefinieerde classificaties uit te sluiten die worden toegewezen aan resource klassen:

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

De catalogus weergave, [sys. workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?view=azure-sqldw-latest), bevat informatie over de para meters die worden gebruikt bij het maken van de classificatie.  In de onderstaande query ziet u dat ExecReportsClassifier is gemaakt op de ```membername``` para meter voor waarden met ExecutiveReports:

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![query resultaten](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

Om het oplossen van problemen met een fout te vereenvoudigen, raden we u aan om resource klasse-roltoewijzingen te verwijderen tijdens het maken van de werk belasting classificaties. De onderstaande code retourneert bestaande lidmaatschappen van resource klassen. Voer sp_droprolemember uit voor elke ```membername``` die wordt geretourneerd door de bijbehorende resource klasse.
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
