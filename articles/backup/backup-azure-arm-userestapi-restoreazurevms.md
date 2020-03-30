---
title: Azure VM's herstellen met REST API
description: In dit artikel leest u hoe u herstelbewerkingen van Azure Virtual Machine Backup beheert met behulp van REST API.
ms.topic: conceptual
ms.date: 09/12/2018
ms.assetid: b8487516-7ac5-4435-9680-674d9ecf5642
ms.openlocfilehash: 4990d815721ddbdde8e6eb6ebf8d6d3b49adc700
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74173373"
---
# <a name="restore-azure-virtual-machines-using-rest-api"></a>Azure Virtual-machines herstellen met REST API

Zodra de back-up van een virtuele Azure-machine met Azure Backup is voltooid, kan men volledige Azure Virtual-machines of -schijven of -bestanden herstellen van dezelfde back-upkopie. In dit artikel wordt beschreven hoe u een Azure VM of -schijven herstellen met behulp van REST API.

Voor elke herstelbewerking moet men eerst het desbetreffende herstelpunt identificeren.

## <a name="select-recovery-point"></a>Herstelpunt selecteren

De beschikbare herstelpunten van een back-upitem kunnen worden vermeld met behulp van de [rest-API voor het herstelpunt van](https://docs.microsoft.com/rest/api/backup/recoverypoints/list)de lijst. Het is een eenvoudige *GET-bewerking* met alle relevante waarden.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints?api-version=2019-05-13
```

De `{containerName}` `{protectedItemName}` en zijn zoals [hier](backup-azure-arm-userestapi-backupazurevms.md#example-responses-1)gebouwd . `{fabricName}`is "Azure".

De *GET* URI heeft alle vereiste parameters. Er is geen behoefte aan een extra aanvraaginstantie

### <a name="responses"></a>Antwoorden

|Name  |Type  |Beschrijving  |
|---------|---------|---------|
|200 OK     |   [RecoveryPointResourceList](https://docs.microsoft.com/rest/api/backup/recoverypoints/list#recoverypointresourcelist)      |       OK  |

#### <a name="example-response"></a>Voorbeeld van een antwoord

Zodra de *GET* URI is ingediend, wordt een 200 (OK) reactie geretourneerd.

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: 03453538-2f8d-46de-8374-143ccdf60f40
x-ms-client-request-id: c48f4436-ce3f-42da-b537-12710d4d1a24; c48f4436-ce3f-42da-b537-12710d4d1a24
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14998
x-ms-correlation-request-id: 03453538-2f8d-46de-8374-143ccdf60f40
x-ms-routing-request-id: SOUTHINDIA:20180604T071851Z:03453538-2f8d-46de-8374-143ccdf60f40
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 07:18:51 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/VM;testRG;testVM/recoveryPoints/20982486783671",
      "name": "20982486783671",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "AppConsistent",
        "recoveryPointTime": "2018-06-04T06:06:00.5121087Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "NormalStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          },
          {
            "type": 2,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_A1_v2",
        "originalStorageAccountOption": false
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/VM;testRG;testVM/recoveryPoints/23358112038108",
      "name": "23358112038108",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "CrashConsistent",
        "recoveryPointTime": "2018-06-03T06:20:56.3043878Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "NormalStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          },
          {
            "type": 2,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_A1_v2",
        "originalStorageAccountOption": false
      }
    },
......
```

Het herstelpunt wordt in `{name}` de bovenstaande reactie met het veld geïdentificeerd.

## <a name="restore-disks"></a>Schijven herstellen

Als er behoefte is aan het aanpassen van het maken van een VM uit de back-upgegevens, kan men schijven gewoon herstellen naar een gekozen opslagaccount en een VM maken van die schijven volgens hun vereisten. De opslagrekening moet zich in dezelfde regio bevinden als de kluis van de herstelservices en mag niet zoneoverbodig zijn. De schijven en de configuratie van de back-up VM ("vmconfig.json") worden opgeslagen in het opgegeven opslagaccount.

Het activeren van herstelschijven is een *POST-verzoek.* Voor meer informatie over de bewerking Schijven herstellen raadpleegt u de [REST-API voor 'trigger restore'.](https://docs.microsoft.com/rest/api/backup/restores/trigger)

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2019-05-13
```

De `{containerName}` `{protectedItemName}` en zijn zoals [hier](backup-azure-arm-userestapi-backupazurevms.md#example-responses-1)gebouwd . `{fabricName}`is "Azure" `{recoveryPointId}` en `{name}` het is het veld van het [hierboven](#example-response)genoemde herstelpunt.

### <a name="create-request-body"></a>Aanvraaginstantie maken

Als u een schijfherstel wilt activeren vanuit een Azure VM-back-up, volgt u de onderdelen van de aanvraaginstantie.

|Name  |Type  |Beschrijving  |
|---------|---------|---------|
|properties     | [IaaSVMRestoreRequest](https://docs.microsoft.com/rest/api/backup/restores/trigger#iaasvmrestorerequest)        |    Eigenschappen van RestoreRequestResource     |

Raadpleeg voor de volledige lijst met definities van de aanvraaginstantie en andere details het [HERSTEL REST API-document activeren.](https://docs.microsoft.com/rest/api/backup/restores/trigger#request-body)

#### <a name="example-request"></a>Voorbeeldaanvraag

In de volgende aanvraaginstantie worden eigenschappen gedefinieerd die nodig zijn om een schijfherstel te activeren.

```json
{
  "properties": {
    "objectType": "IaasVMRestoreRequest",
    "recoveryPointId": "20982486783671",
    "recoveryType": "RestoreDisks",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testAccount",
    "region": "westus",
    "createNewCloudService": false,
    "originalStorageAccountOption": false,
    "encryptionDetails": {
      "encryptionEnabled": false
    }
  }
}
```

### <a name="response"></a>Antwoord

Het activeren van een herstelschijf is een [asynchrone bewerking.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) Dit betekent dat deze bewerking een andere bewerking maakt die afzonderlijk moet worden bijgehouden.

Het retourneert twee antwoorden: 202 (Geaccepteerd) wanneer een andere bewerking wordt gemaakt en vervolgens 200 (OK) wanneer die bewerking is voltooid.

|Name  |Type  |Beschrijving  |
|---------|---------|---------|
|202 Geaccepteerd     |         |     Geaccepteerd    |

#### <a name="example-responses"></a>Voorbeeldreacties

Zodra u de *POST* URI indient voor het activeren van herstelschijven, is het eerste antwoord 202 (Geaccepteerd) met een locatiekop- of Azure-async-header.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-client-request-id: a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b; a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1198
x-ms-correlation-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-routing-request-id: SOUTHINDIA:20180604T130003Z:893fe372-8d6c-4c56-b589-45a95eeef95f
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 13:00:03 GMT
Location: https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Houd vervolgens de resulterende bewerking bij met behulp van de locatiekop- of Azure-AsyncOperation-header met een eenvoudige *OPDRACHT GET.*

```http
GET https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
```

Zodra de bewerking is voltooid, wordt 200 (OK) geretourneerd met de id van de resulterende hersteltaak in de antwoordinstantie.

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: ea2a8011-eb83-4a4b-9ed2-e694070a966a
x-ms-client-request-id: a7f3a144-ed80-41ee-bffe-ae6a90c35a2f; a7f3a144-ed80-41ee-bffe-ae6a90c35a2f
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14973
x-ms-correlation-request-id: ea2a8011-eb83-4a4b-9ed2-e694070a966a
x-ms-routing-request-id: SOUTHINDIA:20180604T130917Z:ea2a8011-eb83-4a4b-9ed2-e694070a966a
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 13:09:17 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "781a0f18-e250-4d73-b059-5e9ffed4069e",
  "name": "781a0f18-e250-4d73-b059-5e9ffed4069e",
  "status": "Succeeded",
  "startTime": "2018-06-04T13:00:03.8068176Z",
  "endTime": "2018-06-04T13:00:03.8068176Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "3021262a-fb3a-4538-9b37-e3e97a386093"
  }
}
```

Aangezien de back-uptaak een langdurige bewerking is, moet deze worden bijgehouden zoals uitgelegd in de [monitortaken met rest-API-document.](backup-azure-arm-userestapi-managejobs.md#tracking-the-job)

Zodra de langlopende taak is voltooid, zullen de schijven en de configuratie van de back-up virtuele machine ("VMConfig.json") aanwezig zijn in het opgegeven opslagaccount.

## <a name="restore-as-another-virtual-machine"></a>Herstellen als een andere virtuele machine

[Selecteer het herstelpunt](#select-recovery-point) en maak de aanvraagtekst zoals hieronder opgegeven om een andere Azure Virtual-machine met de gegevens vanaf het herstelpunt te maken.

De volgende aanvraaginstantie definieert eigenschappen die nodig zijn om een virtuele machine te herstellen.

```json
{
  "parameters": {
        "subscriptionId": "00000000-0000-0000-0000-000000000000",
        "resourceGroupName": "testVaultRG",
        "vaultName": "testVault",
        "fabricName": "Azure",
        "containerName": "IaasVMContainer;iaasvmcontainerv2;testRG;testVM",
        "protectedItemName": "VM;iaasvmcontainerv2;testRG;testVM",
        "recoveryPointId": "348916168024334",
        "api-version": "2019-05-13",
      "parameters": {
        "properties": {
          "objectType":  "IaasVMRestoreRequest",
          "recoveryPointId": "348916168024334",
          "recoveryType": "AlternateLocation",
          "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
          "targetVirtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Compute/virtualmachines/targetVM",
          "targetResourceGroupId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG",
          "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testingAccount",
          "virtualNetworkId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Network/virtualNetworks/testNet",
          "subnetId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Network/virtualNetworks/testNet/subnets/default",
          "region": "westus",
          "createNewCloudService": false,
          "originalStorageAccountOption": false,
          "encryptionDetails": {
            "encryptionEnabled": false
          }
        }
      }
    }
}
```

Het antwoord moet worden behandeld op dezelfde manier als [hierboven uitgelegd voor het herstellen van schijven](#response).

## <a name="next-steps"></a>Volgende stappen

Zie de volgende documenten voor meer informatie over de AZURE Backup REST API's:

- [REST-API van Azure Recovery Services-provider](/rest/api/recoveryservices/)
- [Aan de slag gaan met Azure REST API](/rest/api/azure/)
