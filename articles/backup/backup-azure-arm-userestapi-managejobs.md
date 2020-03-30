---
title: Back-uptaken beheren met de REST-API
description: Lees in dit artikel hoe u back-uptaken van Azure Backup bijhouden en beheren met restapi.
ms.topic: conceptual
ms.date: 08/03/2018
ms.assetid: b234533e-ac51-4482-9452-d97444f98b38
ms.openlocfilehash: 628569c547aa776ec2fbb7ec7e32edad7c1fe7dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273525"
---
# <a name="track-backup-and-restore-jobs-using-rest-api"></a>Back-ups bijhouden en taken herstellen met REST API

Azure Backup-service activeert taken die op de achtergrond worden uitgevoerd in verschillende scenario's, zoals het activeren van back-ups, herstelbewerkingen, het uitschakelen van back-ups. Deze taken kunnen worden bijgehouden met behulp van hun id's.

## <a name="fetch-job-information-from-operations"></a>Taakgegevens ophalen uit bewerkingen

Een bewerking zoals het activeren van back-ups geeft altijd een jobID terug. Bijvoorbeeld: De uiteindelijke reactie van een [trigger back-up REST API-bewerking](backup-azure-arm-userestapi-backupazurevms.md#example-responses-3) is als volgt:

```http
{
  "id": "cd153561-20d3-467a-b911-cc1de47d4763",
  "name": "cd153561-20d3-467a-b911-cc1de47d4763",
  "status": "Succeeded",
  "startTime": "2018-09-12T02:16:56.7399752Z",
  "endTime": "2018-09-12T02:16:56.7399752Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "41f3e94b-ae6b-4a20-b422-65abfcaf03e5"
  }
}
```

De Azure VM-back-uptaak wordt geïdentificeerd door het veld "jobId" en kan worden bijgehouden zoals [hier](https://docs.microsoft.com/rest/api/backup/jobdetails/) vermeld met behulp van een eenvoudige *GET-aanvraag.*

## <a name="tracking-the-job"></a>Het bijhouden van de taak

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupJobs/{jobName}?api-version=2019-05-13
```

De `{jobName}` is "jobId" hierboven vermeld. Het antwoord is altijd 200 OK met het veld 'status', dat de huidige status van de taak aangeeft. Zodra het "Voltooid" of "CompletedWithWarnings" is, onthult de sectie 'extendedInfo' meer details over de taak.

### <a name="response"></a>Antwoord

|Name  |Type  |Beschrijving  |
|---------|---------|---------|
|200 OK     | [JobResource](https://docs.microsoft.com/rest/api/backup/jobdetails/get#jobresource)        | OK        |

#### <a name="example-response"></a>Voorbeeld van een antwoord

Zodra de *GET* URI is ingediend, wordt een 200 (OK) reactie geretourneerd.

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: e9702101-9da2-4681-bdf3-a54e17329a56
x-ms-client-request-id: ba4dff71-1655-4c1d-a71f-c9869371b18b; ba4dff71-1655-4c1d-a71f-c9869371b18b
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14989
x-ms-correlation-request-id: e9702101-9da2-4681-bdf3-a54e17329a56
x-ms-routing-request-id: SOUTHINDIA:20180521T102317Z:e9702101-9da2-4681-bdf3-a54e17329a56
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:23:17 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-RecoveryServices-ResourceGroup-centralindia/providers/microsoft.recoveryservices/vaults/abdemovault/backupJobs/7ddead57-bcb9-4269-ac31-6a1b57588700",
  "name": "7ddead57-bcb9-4269-ac31-6a1b57588700",
  "type": "Microsoft.RecoveryServices/vaults/backupJobs",
  "properties": {
    "jobType": "AzureIaaSVMJob",
    "duration": "00:20:23.0896697",
    "actionsInfo": [
      1
    ],
    "virtualMachineVersion": "Compute",
    "extendedInfo": {
      "tasksList": [
        {
          "taskId": "Take Snapshot",
          "duration": "00:00:00",
          "status": "Completed"
        },
        {
          "taskId": "Transfer data to vault",
          "duration": "00:00:00",
          "status": "Completed"
        }
      ],
      "propertyBag": {
        "VM Name": "uttestvmub1",
        "Backup Size": "2332 MB"
      }
    },
    "entityFriendlyName": "uttestvmub1",
    "backupManagementType": "AzureIaasVM",
    "operation": "Backup",
    "status": "Completed",
    "startTime": "2018-05-21T08:35:40.9488967Z",
    "endTime": "2018-05-21T08:56:04.0385664Z",
    "activityId": "7df8e874-1d66-4f81-8e91-da2fe054811d"
  }
}
}

```
