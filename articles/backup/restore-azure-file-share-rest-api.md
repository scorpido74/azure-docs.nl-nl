---
title: Azure-bestandsshares herstellen met REST API
description: Meer informatie over het gebruik van REST API om Azure-bestandsshares of specifieke bestanden te herstellen vanaf een herstelpunt dat is gemaakt door Azure Backup
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 1c3160491ef92c62745af1468556e7d5c30437fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252504"
---
# <a name="restore-azure-file-shares-using-rest-api"></a>Azure-bestandsshares herstellen met REST API

In dit artikel wordt uitgelegd hoe u een volledige bestandsshare of specifieke bestanden herstellen vanaf een herstelpunt dat is gemaakt door [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) met behulp van de REST API.

Aan het einde van dit artikel leert u hoe u de volgende bewerkingen uitvoeren met restapi:

* Herstelpunten weergeven voor een back-up azure-bestandsshare.
* Een volledig Azure-bestandsshare herstellen.
* Afzonderlijke bestanden of mappen herstellen.

## <a name="prerequisites"></a>Vereisten

We gaan ervan uit dat u al een back-up bestandsshare hebt die u wilt herstellen. Als u dit niet doet, schakelt u [Azure-bestandsshare back-up in met rest api](backup-azure-file-share-rest-api.md) om te leren hoe u er een maakt.

Voor dit artikel gebruiken we de volgende bronnen:

* **RecoveryServicesVault**: *azurefilesvault*
* **Resourcegroep**: *azurefiles*
* **Opslagrekening**: *afsaccount*
* **Bestandsshare:** *azurefiles*

## <a name="fetch-containername-and-protecteditemname"></a>ContainerNaam en ProtectedItemName ophalen

Voor de meeste herstelgerelateerde API-aanroepen moet u waarden doorgeven voor de parameters {containerName} en {protectedItemName} URI. Gebruik het kenmerk ID in de antwoordtekst van de bewerking [GET backupprotectableitems](https://docs.microsoft.com/rest/api/backup/protecteditems/get) om waarden voor deze parameters op te halen. In ons voorbeeld is de id van het bestandsaandeel dat we willen beveiligen:

`"/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afsaccount/protectableItems/azurefileshare;azurefiles`

Dus de waarden vertalen als volgt:

* {containername} - *storagecontainer;storage;azurefiles;afsaccount*
* {protectedItemName} - *azurefileshare;azurefiles*

## <a name="fetch-recovery-points-for-backed-up-azure-file-share"></a>Herstelpunten ophalen voor back-ups van Azure-bestandsshare

Als u een back-up bestand of bestanden wilt herstellen, selecteert u eerst een herstelpunt om de herstelbewerking uit te voeren. De beschikbare herstelpunten van een back-upitem kunnen worden vermeld met de [API-aanroep voor herstelpuntenrest.](https://docs.microsoft.com/rest/api/site-recovery/recoverypoints/listbyreplicationprotecteditems) Het is een GET-bewerking met alle relevante waarden.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints?api-version=2019-05-13&$filter={$filter}
```

Stel de URI-waarden als volgt in:

* {fabricName}: *Azure*
* {vaultName}: *azurefilesvault*
* {containername}: *storagecontainer;storage;azurefiles;afsaccount*
* {protectedItemName}: *azurefileshare;azurefiles*
* {ResourceGroupName}: *azurefiles*

De GET URI heeft alle vereiste parameters. Er is geen behoefte aan een extra aanvraag lichaam.

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints?api-version=2019-05-13
```

### <a name="example-response"></a>Voorbeeld van een antwoord

Zodra de GET URI is ingediend, wordt een 200-antwoord geretourneerd:

```http
HTTP/1.1" 200 None
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Transfer-Encoding': 'chunked'
'Content-Type': 'application/json'
'Content-Encoding': 'gzip'
'Expires': '-1'
'Vary': 'Accept-Encoding'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': 'd68d7951-7d97-4c49-9a2d-7fbaab55233a'
'x-ms-client-request-id': '4edb5a58-47ea-11ea-a27a-0a580af41908, 4edb5a58-47ea-11ea-a27a-0a580af41908'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'Server': 'Microsoft-IIS/10.0'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-reads': '11998'
'x-ms-correlation-request-id': 'd68d7951-7d97-4c49-9a2d-7fbaab55233a'
'x-ms-routing-request-id': 'WESTEUROPE:20200205T073708Z:d68d7951-7d97-4c49-9a2d-7fbaab55233a'
'Date': 'Wed, 05 Feb 2020 07:37:08 GMT'
{
“value”:[
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints/932881138555802864",
    "location": null,
    "name": "932881138555802864",
    "properties": {
      "fileShareSnapshotUri": "https://afsaccount.file.core.windows.net/azurefiles?sharesnapshot=2020-02-04T08:01:35.0000000Z",
      "objectType": "AzureFileShareRecoveryPoint",
      "recoveryPointSizeInGb": 1,
      "recoveryPointTime": "2020-02-04T08:01:35+00:00",
      "recoveryPointType": "FileSystemConsistent"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints/932878582606969225",
    "location": null,
    "name": "932878582606969225",
    "properties": {
      "fileShareSnapshotUri": "https://afsaccount.file.core.windows.net/azurefiles?sharesnapshot=2020-02-03T08:05:30.0000000Z",
      "objectType": "AzureFileShareRecoveryPoint",
      "recoveryPointSizeInGb": 1,
      "recoveryPointTime": "2020-02-03T08:05:30+00:00",
      "recoveryPointType": "FileSystemConsistent"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints/932890167574511261",
    "location": null,
    "name": "932890167574511261",
    "properties": {
      "fileShareSnapshotUri": "https://afsaccount.file.core.windows.net/azurefiles?sharesnapshot=2020-02-02T08:03:50.0000000Z",
      "objectType": "AzureFileShareRecoveryPoint",
      "recoveryPointSizeInGb": 1,
      "recoveryPointTime": "2020-02-02T08:03:50+00:00",
      "recoveryPointType": "FileSystemConsistent"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints"
  },
```

Het herstelpunt wordt geïdentificeerd met het veld {name} in het bovenstaande antwoord.

## <a name="full-share-recovery-using-rest-api"></a>Herstel van volledige delen met REST API

Gebruik deze hersteloptie om de volledige bestandsshare in het origineel of een alternatieve locatie te herstellen.
Het activeren van herstel is een POST-aanvraag en u deze bewerking uitvoeren met behulp van de [TRIGGER Restore](https://docs.microsoft.com/rest/api/backup/restores/trigger) REST API.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2019-05-13
```

De waarden {containerName} en {protectedItemName} zijn [hier](#fetch-containername-and-protecteditemname) ingesteld en recoveryPointID is het veld {name} van het hierboven genoemde herstelpunt.

```http
POST https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare%3Bazurefiles/recoveryPoints/932886657837421071/restore?api-version=2019-05-13'
```

### <a name="create-request-body"></a>Aanvraaginstantie maken

Als u een herstel voor een Azure-bestandsshare wilt activeren, zijn de volgende onderdelen van de aanvraaginstantie:

Name |  Type   |   Beschrijving
--- | ---- | ----
Eigenschappen | AzureFileShareRestoreRequest | Eigenschappen RestoreRequestResource

Raadpleeg voor de volledige lijst met definities van de aanvraaginstantie en andere details het [trigger Restore REST API-document](https://docs.microsoft.com/rest/api/backup/restores/trigger#request-body).

### <a name="restore-to-original-location"></a>Herstellen naar oorspronkelijke locatie

#### <a name="request-body-example"></a>Voorbeeld van het hoofdvoorbeeld van aanvraag

In de volgende aanvraaginstantie worden eigenschappen gedefinieerd die nodig zijn om een Azure-bestandsshareherstel te activeren:

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "recoveryType":"OriginalLocation",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
      "copyOptions":"Overwrite",
      "restoreRequestType":"FullShareRestore"
}
}
```

### <a name="restore-to-alternate-location"></a>Herstellen naar alternatieve locatie

Geef de volgende parameters op voor alternatieve locatieherstel:

* **targetResourceId:** het opslagaccount waarop de back-upinhoud wordt hersteld. Het doelopslagaccount moet zich op dezelfde locatie bevinden als de kluis.
* **naam:** de bestandsshare binnen het doelopslagaccount waarop de back-upinhoud is hersteld.
* **targetFolderPath:** de map onder de bestandsshare waaraan gegevens worden hersteld.

#### <a name="request-body-example"></a>Voorbeeld van het hoofdvoorbeeld van aanvraag

De volgende aanvraaginstantie herstelt het *azurefiles-bestandsaandeel* in het *account van afsaccountopslag* naar het *azurefiles1-bestandsaandeel* in het *opslagaccount afaccount1.*

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "recoveryType":"AlternateLocation",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
      "copyOptions":"Overwrite",
      "restoreRequestType":"FullShareRestore",
      "restoreFileSpecs":[
         {
            "targetFolderPath":"restoredata"
}
],
      "targetDetails":{
         "name":"azurefiles1",
         "targetResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afaccount1"
}
}
}
```

### <a name="response"></a>Antwoord

Het activeren van een herstelbewerking is een [asynchrone bewerking.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) Met deze bewerking wordt een andere bewerking uitgevoerd die afzonderlijk moet worden bijgehouden.
Het retourneert twee antwoorden: 202 (Geaccepteerd) wanneer een andere bewerking wordt gemaakt en 200 (OK) wanneer die bewerking is voltooid.

#### <a name="response-example"></a>Voorbeeld van antwoord

Zodra u de *POST* URI indient voor het activeren van een herstel, is het eerste antwoord 202 (Geaccepteerd) met een locatiekop- of Azure-async-header.

```http
HTTP/1.1" 202
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/operationResults/68ccfbc1-a64f-4b29-b955-314b5790cfa9?api-version=2019-05-13'
'Retry-After': '60'
'Azure-AsyncOperation': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/operationsStatus/68ccfbc1-a64f-4b29-b955-314b5790cfa9?api-version=2019-05-13'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '2426777d-c5ec-44b6-a324-384f8947460c'
'x-ms-client-request-id': '3c743096-47eb-11ea-ae90-0a580af41908, 3c743096-47eb-11ea-ae90-0a580af41908'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1198'
'x-ms-correlation-request-id': '2426777d-c5ec-44b6-a324-384f8947460c'
'x-ms-routing-request-id': 'WESTEUROPE:20200205T074347Z:2426777d-c5ec-44b6-a324-384f8947460c'
'Date': 'Wed, 05 Feb 2020 07:43:47 GMT'
```

Houd vervolgens de resulterende bewerking bij met behulp van de locatiekop- of de Azure-AsyncOperation-header met een get-opdracht.

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/68ccfbc1-a64f-4b29-b955-314b5790cfa9?api-version=2016-12-01
```

Zodra de bewerking is voltooid, wordt 200 (OK) geretourneerd met de id van de resulterende hersteltaak in de antwoordinstantie.

```http
HTTP/1.1" 200
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Transfer-Encoding': 'chunked'
'Content-Type': 'application/json'
'Content-Encoding': 'gzip'
'Expires': '-1'
'Vary': 'Accept-Encoding'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '41ee89b2-3be4-40d8-8ff6-f5592c2571e3'
'x-ms-client-request-id': '3c743096-47eb-11ea-ae90-0a580af41908, 3c743096-47eb-11ea-ae90-0a580af41908'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'Server': 'Microsoft-IIS/10.0'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-reads': '11998'
'x-ms-correlation-request-id': '41ee89b2-3be4-40d8-8ff6-f5592c2571e3'
'x-ms-routing-request-id': 'WESTEUROPE:20200205T074348Z:41ee89b2-3be4-40d8-8ff6-f5592c2571e3'
'Date': 'Wed, 05 Feb 2020 07:43:47 GMT'
{
  "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/a7e97e42-4e54-4d4b-b449-26fcf946f42c",
  "location": null,
  "name": "a7e97e42-4e54-4d4b-b449-26fcf946f42c",
  "properties": {
    "actionsInfo": [
      "Cancellable"
    ],
    "activityId": "3c743096-47eb-11ea-ae90-0a580af41908",
    "backupManagementType": "AzureStorage",
    "duration": "0:00:01.863098",
    "endTime": null,
    "entityFriendlyName": "azurefiles",
    "errorDetails": null,
    "extendedInfo": {
      "dynamicErrorMessage": null,
      "propertyBag": {},
      "tasksList": []
    },
    "jobType": "AzureStorageJob",
    "operation": "Restore",
    "startTime": "2020-02-05T07:43:47.144961+00:00",
    "status": "InProgress",
    "storageAccountName": "afsaccount",
    "storageAccountVersion": "Storage"
  },
  "resourceGroup": "azurefiles",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupJobs"
}
```

Voor alternatieve locatie herstel, zal de reactie lichaam als volgt:

```http
{
  "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/7e0ee41e-6e31-4728-a25c-98ff6b777641",
  "location": null,
  "name": "7e0ee41e-6e31-4728-a25c-98ff6b777641",
  "properties": {
    "actionsInfo": [
      "Cancellable"
    ],
    "activityId": "6077be6e-483a-11ea-a915-0a580af4ad72",
    "backupManagementType": "AzureStorage",
    "duration": "0:00:02.171965",
    "endTime": null,
    "entityFriendlyName": "azurefiles",
    "errorDetails": null,
    "extendedInfo": {
      "dynamicErrorMessage": null,
      "propertyBag": {
        "Data Transferred (in MB)": "0",
        "Job Type": "Recover to an alternate file share",
        "Number Of Failed Files": "0",
        "Number Of Restored Files": "0",
        "Number Of Skipped Files": "0",
        "RestoreDestination": "afaccount1/azurefiles1/restoredata",
        "Source File Share Name": "azurefiles",
        "Source Storage Account Name": "afsaccount",
        "Target File Share Name": "azurefiles1",
        "Target Storage Account Name": "afaccount1"
      },
      "tasksList": []
    },
    "jobType": "AzureStorageJob",
    "operation": "Restore",
    "startTime": "2020-02-05T17:10:18.106532+00:00",
    "status": "InProgress",
    "storageAccountName": "afsaccount",
    "storageAccountVersion": "ClassicCompute"
  },
  "resourceGroup": "azurefiles",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupJobs"
}
```

Aangezien de back-uptaak een langdurige bewerking is, moet deze worden bijgehouden zoals uitgelegd in de [monitortaken met rest-API-document.](https://docs.microsoft.com/azure/backup/backup-azure-arm-userestapi-managejobs#tracking-the-job)

## <a name="item-level-recovery-using-rest-api"></a>Itemniveauherstel met REST API

U deze hersteloptie gebruiken om afzonderlijke bestanden of mappen in het origineel of een alternatieve locatie te herstellen.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2019-05-13
```

De waarden {containerName} en {protectedItemName} zijn [hier](#fetch-containername-and-protecteditemname) ingesteld en recoveryPointID is het veld {name} van het hierboven genoemde herstelpunt.

```http
POST https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare%3Bazurefiles/recoveryPoints/932886657837421071/restore?api-version=2019-05-13'
```

### <a name="create-request-body"></a>Aanvraaginstantie maken

Als u een herstel voor een Azure-bestandsshare wilt activeren, zijn de volgende onderdelen van de aanvraaginstantie:

Name |  Type   |   Beschrijving
--- | ---- | ----
Eigenschappen | AzureFileShareRestoreRequest | Eigenschappen RestoreRequestResource

Raadpleeg voor de volledige lijst met definities van de aanvraaginstantie en andere details het [trigger Restore REST API-document](https://docs.microsoft.com/rest/api/backup/restores/trigger#request-body).

### <a name="restore-to-original-location"></a>Herstellen naar oorspronkelijke locatie

De volgende aanvraaginstantie is het herstellen van het bestand *Restoretest.txt* in het *azurefiles-bestandsshare* in het account van de opslag account van *afsaccount.*

Aanvraaghoofd maken

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "copyOptions":"Overwrite",
      "recoveryType":"OriginalLocation",
      "restoreFileSpecs":[
         {
            "fileSpecType":"File",
            "path":"RestoreTest.txt",
            "targetFolderPath":null
}
],
      "restoreRequestType":"ItemLevelRestore",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.storage/storageAccounts/afsaccount",
      "targetDetails":null
}
}
```

### <a name="restore-to-alternate-location"></a>Herstellen naar alternatieve locatie

De volgende aanvraaginstantie is het herstellen van het bestand *Restoretest.txt* in het *azurefiles-bestandsshare* in het account van de opslag van *afsaccount* naar de *map restoredata* van het *azurefiles1-bestandsshare* in het *opslagaccount van afaccount1.*

Aanvraaginstantie maken

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "recoveryType":"AlternateLocation",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
      "copyOptions":"Overwrite",
      "restoreRequestType":"ItemLevelRestore",
      "restoreFileSpecs":[
         {
            "path":"Restore/RestoreTest.txt",
            "fileSpecType":"File",
            "targetFolderPath":"restoredata"
}
],
      "targetDetails":{
         "name":"azurefiles1",
         "targetResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afaccount1"
}
}
}
```

Het antwoord moet worden behandeld op dezelfde manier als hierboven uiteengezet voor [volledige aandeel herstelt](#full-share-recovery-using-rest-api).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [beheren van back-ups van Azure-bestandsshares met behulp van Rest API.](manage-azure-file-share-rest-api.md)
