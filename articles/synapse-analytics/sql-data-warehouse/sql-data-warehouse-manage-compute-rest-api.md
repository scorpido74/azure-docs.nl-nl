---
title: Pauzeren, hervatten, schalen met REST API's
description: Beheer rekenkracht in Azure Synapse Analytics datawarehouse via REST API's.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/29/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 322f1dfcb709727ddd3a97ea22dbe8243aedca20
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350352"
---
# <a name="rest-apis-for-azure-sql-data-warehouse"></a>REST-API's voor Azure SQL Data Warehouse
REST API's voor het beheren van compute in Azure Synapse Analytics datawarehouse.

## <a name="scale-compute"></a>De schaal van Compute aanpassen
Als u de gegevensmagazijnen wilt wijzigen, gebruikt u de API Database REST [maken of bijwerken.](/rest/api/sql/databases/createorupdate) In het volgende voorbeeld worden de gegevensmagazijnen ingesteld op DW1000 voor de database MySQLDW, die wordt gehost op server MyServer. De server bevindt zich in een Azure-brongroep met de naam ResourceGroup1.

```
PATCH https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

## <a name="pause-compute"></a>Compute onderbreken

Als u een database wilt onderbreken, gebruikt u de API Voor de rest van de [pauze.](/rest/api/sql/databases/pause) In het volgende voorbeeld wordt een database met de naam Database02 onderbroken op een server met de naam Server01. De server bevindt zich in een Azure-brongroep met de naam ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>Compute hervatten

Als u een database wilt starten, gebruikt u de [API VOOR hersteldatabaseREST.](/rest/api/sql/databases/resume) In het volgende voorbeeld wordt een database met de naam Database02 gestart die wordt gehost op een server met de naam Server01. De server bevindt zich in een Azure-brongroep met de naam ResourceGroup1. 

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>Databasestatus controleren

> [!NOTE]
> Momenteel Controleer database status kan online terugkeren terwijl de database is het invullen van de online workflow, wat resulteert in verbindingsfouten. Mogelijk moet u een vertraging van 2 tot 3 minuten toevoegen aan uw toepassingscode als u deze API-aanroep gebruikt om verbindingspogingen te activeren.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01 HTTP/1.1
```

## <a name="get-maintenance-schedule"></a>Onderhoudsschema ophalen
Controleer het onderhoudsschema dat is ingesteld voor een gegevensmagazijn. 

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

```

## <a name="set-maintenance-schedule"></a>Onderhoudsschema instellen
Een onderhoudsschema instellen en bijwerken voor een bestaand gegevensmagazijn.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

{
    "properties": {
        "timeRanges": [
                {
                                "dayOfWeek": Saturday,
                                "startTime": 00:00,
                                "duration": 08:00,
                },
                {
                                "dayOfWeek": Wednesday
                                "startTime": 00:00,
                                "duration": 08:00,
                }
                ]
    }
}

```


## <a name="next-steps"></a>Volgende stappen
Zie [Compute beheren voor](sql-data-warehouse-manage-compute-overview.md)meer informatie .

