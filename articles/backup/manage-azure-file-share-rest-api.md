---
title: Back-up voor azure-bestandsshare beheren met Rest API
description: Meer informatie over het gebruik van REST API voor het beheren en bewaken van Azure-bestandsshares waarvan een back-up wordt gemaakt door Azure Backup.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 9d29b226aff568c91de8e1f19ddc0c64f8169e4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444730"
---
# <a name="manage-azure-file-share-backup-with-rest-api"></a>Back-up voor azure-bestandsshare beheren met REST API

In dit artikel wordt uitgelegd hoe u taken uitvoert voor het beheren en bewaken van de Azure-bestandsshares waarvan een back-up wordt gemaakt door [Azure Backup.](https://docs.microsoft.com/azure/backup/backup-overview)

## <a name="monitor-jobs"></a>Taken controleren

De Azure Backup-service activeert taken die op de achtergrond worden uitgevoerd. Dit omvat scenario's zoals het activeren van back-ups, herstelbewerkingen en het uitschakelen van back-ups. Deze taken kunnen worden bijgehouden met behulp van hun id's.

### <a name="fetch-job-information-from-operations"></a>Taakgegevens ophalen uit bewerkingen

Een bewerking zoals het activeren van back-ups retourneert altijd een jobID in het antwoord.

De uiteindelijke reactie van een [triggerback-upREST API-bewerking](backup-azure-file-share-rest-api.md#trigger-an-on-demand-backup-for-file-share) is bijvoorbeeld als volgt:

```json
{
    "id": "c3a52d1d-0853-4211-8141-477c65740264",
    "name": "c3a52d1d-0853-4211-8141-477c65740264",
    "status": "Succeeded",
    "startTime": "2020-02-03T18:10:48.296012Z",
    "endTime": "2020-02-03T18:10:48.296012Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b"
    }
}
```

De back-uptaak azure-bestandsshare wordt geïdentificeerd door het **veld jobId** en kan worden bijgehouden zoals [hier](https://docs.microsoft.com/rest/api/backup/jobdetails/) wordt vermeld met behulp van een GET-aanvraag.

### <a name="tracking-the-job"></a>Het bijhouden van de taak

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupJobs/{jobName}?api-version=2019-05-13
```

De {jobName} is de hierboven genoemde "jobId". Het antwoord is altijd "200 OK" met het **statusveld** dat de status van de taak aangeeft. Zodra het "Voltooid" of "CompletedWithWarnings" is, onthult de uitgebreide **info-sectie** meer details over de taak.

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b?api-version=2019-05-13'
```

#### <a name="response"></a>Antwoord

Name  | Type  |  Beschrijving
--- | --- | ----
200 OK |  JobResource  | OK

#### <a name="response-example"></a>Voorbeeld van antwoord

Zodra de *GET* URI is ingediend, wordt een 200-antwoord geretourneerd.

```http
HTTP/1.1" 200
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Transfer-Encoding': 'chunked'
'Content-Type': 'application/json'
'Content-Encoding': 'gzip'
'Expires': '-1'
'Vary': 'Accept-Encoding'
'Server': 'Microsoft-IIS/10.0, Microsoft-IIS/10.0'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': 'dba43f00-5cdb-43b1-a9ec-23e419db67c5'
'x-ms-client-request-id': 'a644712a-4895-11ea-ba57-0a580af42708, a644712a-4895-11ea-ba57-0a580af42708'
'X-Powered-By': 'ASP.NET'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'x-ms-ratelimit-remaining-subscription-reads': '11999'
'x-ms-correlation-request-id': 'dba43f00-5cdb-43b1-a9ec-23e419db67c5'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T040341Z:dba43f00-5cdb-43b1-a9ec-23e419db67c5'
'Date': 'Thu, 06 Feb 2020 04:03:40 GMT'
{
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b",
    "name": "e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b",
    "type": "Microsoft.RecoveryServices/vaults/backupJobs",
    "properties": {
        "jobType": "AzureStorageJob",
        "duration": "00:00:43.1809140",
        "storageAccountName": "testvault2",
        "storageAccountVersion": "Storage",
        "extendedInfo": {
            "tasksList": [],
            "propertyBag": {
                "File Share Name": "testshare",
                "Storage Account Name": "testvault2",
                "Policy Name": "schedule1"
            }
        },
        "entityFriendlyName": "testshare",
        "backupManagementType": "AzureStorage",
        "operation": "ConfigureBackup",
        "status": "Completed",
        "startTime": "2020-02-03T18:10:48.296012Z",
        "endTime": "2020-02-03T18:11:31.476926Z",
        "activityId": "3677cec0-942d-4eac-921f-8f3c873140d7"
    }
}
```

## <a name="modify-policy"></a>Beleid wijzigen

Als u het beleid wilt wijzigen waarmee de bestandsshare is beveiligd, u dezelfde indeling gebruiken als het inschakelen van beveiliging. Geef de nieuwe beleids-ID op in het aanvraagbeleid en dien de aanvraag in.

Bijvoorbeeld: Als u het beveiligingsbeleid van *testshare* wilt wijzigen van *schema1* naar *schema2,* geeft u de *planning2-id* op in de aanvraaginstantie.

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupPolicies/schedule2"
  }
}
```

## <a name="stop-protection-but-retain-existing-data"></a>De beveiliging stoppen, maar bestaande gegevens bewaren

U de beveiliging van een beveiligde bestandsshare verwijderen, maar de gegevens waarover al een back-up is gemaakt, behouden. Verwijder hiervoor het beleid in de aanvraaginstantie die u hebt gebruikt om[back-up in](backup-azure-file-share-rest-api.md#enable-backup-for-the-file-share) te schakelen en verzendt u de aanvraag. Zodra de koppeling met het beleid is verwijderd, worden back-ups niet meer geactiveerd en worden er geen nieuwe herstelpunten gemaakt.

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": “" ,
“protectionState”:”ProtectionStopped”
  }
}
```

### <a name="sample-response"></a>Voorbeeldantwoord

Het stoppen van de beveiliging voor een bestandsshare is een asynchrone bewerking. De bewerking maakt een andere bewerking die moet worden bijgehouden. Het retourneert twee antwoorden: 202 (Geaccepteerd) wanneer een andere bewerking wordt gemaakt en 200 wanneer die bewerking is voltooid.

Antwoordkopwanneer de bewerking wordt geaccepteerd:

```http
HTTP/1.1" 202
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationResults/b300922a-ad9c-4181-b4cd-d42ea780ad77?api-version=2019-05-13'
'Retry-After': '60'
msrest.http_logger :     'Azure-AsyncOperation': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationsStatus/b300922a-ad9c-4181-b4cd-d42ea780ad77?api-version=2019-05-13'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '3895e8a1-e4b9-4da5-bec7-2cf0266405f8'
'x-ms-client-request-id': 'd331c15e-48ab-11ea-84c0-0a580af46a50, d331c15e-48ab-11ea-84c0-0a580af46a50'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1199'
'x-ms-correlation-request-id': '3895e8a1-e4b9-4da5-bec7-2cf0266405f8'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T064224Z:3895e8a1-e4b9-4da5-bec7-2cf0266405f8'
'Date': 'Thu, 06 Feb 2020 06:42:24 GMT'
'Content-Length': '0'
```

Houd vervolgens de resulterende bewerking bij met de locatiekop- of Azure-AsyncOperation-header met een get-opdracht:

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupoperations/b300922a-ad9c-4181-b4cd-d42ea780ad77?api-version=2016-12-01
```

### <a name="response-body"></a>Hoofdtekst van de reactie

```json
{
    "id": "b300922a-ad9c-4181-b4cd-d42ea780ad77",
    "name": "b300922a-ad9c-4181-b4cd-d42ea780ad77",
    "status": "Succeeded",
    "startTime": "2020-02-06T06:42:24.4001299Z",
    "endTime": "2020-02-06T06:42:24.4001299Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "7816fca8-d5be-4c41-b911-1bbd922e5826"
    }
}
```

## <a name="stop-protection-and-delete-data"></a>Beveiliging stoppen en gegevens verwijderen

Als u de beveiliging van een beveiligde bestandsshare wilt verwijderen en de back-upgegevens ook wilt verwijderen, voert u een verwijderingsbewerking uit zoals hier wordt [beschreven.](https://docs.microsoft.com/rest/api/backup/protecteditems/delete)

```http
DELETE https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

De parameters {containerName} en {protectedItemName} zijn [hier](restore-azure-file-share-rest-api.md#fetch-containername-and-protecteditemname)ingesteld .

In het volgende voorbeeld wordt een bewerking geactiveerd om de beveiliging te stoppen voor de beveiliging van het *testshare-bestand* dat is beveiligd met *azurefilesvault.*

```http
DELETE https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare?api-version=2016-12-01
```

### <a name="responses"></a>Antwoorden

Delete protection is ansynchronous operation. De bewerking maakt een andere bewerking die afzonderlijk moet worden bijgehouden.
Het retourneert twee reacties: 202 (Geaccepteerd) wanneer een andere bewerking wordt gemaakt en 204 (NoContent) wanneer die bewerking is voltooid.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [oplossen van problemen tijdens het configureren van back-ups voor Azure File-shares](troubleshoot-azure-files.md).