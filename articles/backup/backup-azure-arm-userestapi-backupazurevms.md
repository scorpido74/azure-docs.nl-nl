---
title: Back-ups maken van Azure VM's met REST API
description: Lees in dit artikel hoe u back-upbewerkingen van Azure VM Backup configureert, start en beheert met restapi.
ms.topic: conceptual
ms.date: 08/03/2018
ms.assetid: b80b3a41-87bf-49ca-8ef2-68e43c04c1a3
ms.openlocfilehash: 4789ef1e0e09df521f8cab539d972e9e669e0a58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248162"
---
# <a name="back-up-an-azure-vm-using-azure-backup-via-rest-api"></a>Een back-up maken van een Azure VM met Azure Backup via REST API

In dit artikel wordt beschreven hoe u back-ups voor een Azure VM beheert met Azure Backup via REST API. Configureer beveiliging voor de eerste keer voor een voorheen onbeveiligde Azure VM, activeer een on-demand back-up voor een beveiligde Azure VM en wijzig back-upeigenschappen van een back-up VM via REST API zoals hier uitgelegd.

Raadpleeg [het maken van kluis](backup-azure-arm-userestapi-createorupdatevault.md) en [maak zelfstudies](backup-azure-arm-userestapi-createorupdatepolicy.md) voor beleidsREST API voor het maken van nieuwe kluizen en beleidsregels.

Laten we aannemen dat u een VM "testVM" wilt beschermen onder een resourcegroep "testRG" tot een Recovery Services-kluis "testVault", aanwezig binnen de resourcegroep "testVaultRG", met het standaardbeleid (met de naam "DefaultPolicy").

## <a name="configure-backup-for-an-unprotected-azure-vm-using-rest-api"></a>Back-up configureren voor een onbeveiligde Azure VM met REST API

### <a name="discover-unprotected-azure-vms"></a>Onbeschermde Azure VM's ontdekken

Eerst moet de kluis de Azure VM kunnen identificeren. Dit wordt geactiveerd met behulp van de [vernieuwingsbewerking](https://docs.microsoft.com/rest/api/backup/protectioncontainers/refresh). Het is een asynchrone *POST-bewerking* die ervoor zorgt dat de kluis de nieuwste lijst van alle onbeschermde VM in het huidige abonnement krijgt en ze 'caches' maakt. Zodra de VM is 'gecached', kunnen herstelservices toegang krijgen tot de VM en deze beveiligen.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01
```

De POST `{subscriptionId}`URI `{vaultName}` `{vaultresourceGroupName}`heeft `{fabricName}` , , , parameters. Het `{fabricName}` is "Azure". Volgens ons voorbeeld, `{vaultName}` is "testVault" en `{vaultresourceGroupName}` is "testVaultRG". Aangezien alle vereiste parameters in de URI worden gegeven, is er geen behoefte aan een aparte aanvraaginstantie.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01
```

#### <a name="responses"></a>Antwoorden

De bewerking 'vernieuwen' is een [asynchrone bewerking.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) Dit betekent dat deze bewerking een andere bewerking maakt die afzonderlijk moet worden bijgehouden.

Het retourneert twee antwoorden: 202 (Geaccepteerd) wanneer een andere bewerking wordt gemaakt en vervolgens 200 (OK) wanneer die bewerking is voltooid.

|Name  |Type  |Beschrijving  |
|---------|---------|---------|
|204 Geen inhoud     |         |  OK zonder inhoud geretourneerd      |
|202 Geaccepteerd     |         |     Geaccepteerd    |

##### <a name="example-responses"></a>Voorbeeldreacties

Zodra de *POST-aanvraag* is ingediend, wordt een antwoord van 202 (Geaccepteerd) geretourneerd.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
X-Content-Type-Options: nosniff
x-ms-request-id: 43cf550d-e463-421c-8922-37e4766db27d
x-ms-client-request-id: 4910609f-bb9b-4c23-8527-eb6fa2d3253f; 4910609f-bb9b-4c23-8527-eb6fa2d3253f
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 43cf550d-e463-421c-8922-37e4766db27d
x-ms-routing-request-id: SOUTHINDIA:20180521T105701Z:43cf550d-e463-421c-8922-37e4766db27d
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:57:00 GMT
Location: https://management.azure.com/subscriptions//00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2019-05-13
X-Powered-By: ASP.NET
```

De resulterende bewerking bijhouden met de koptekst 'Locatie' met een eenvoudige *opdracht GET*

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2019-05-13
```

Zodra alle Azure VM's zijn ontdekt, retourneert de opdracht GET een respons van 204 (Geen inhoud). De kluis is nu in staat om elke VM te ontdekken binnen het abonnement.

```http
HTTP/1.1 204 NoContent
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: cf6cd73b-9189-4942-a61d-878fcf76b1c1
x-ms-client-request-id: 25bb6345-f9fc-4406-be1a-dc6db0eefafe; 25bb6345-f9fc-4406-be1a-dc6db0eefafe
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14997
x-ms-correlation-request-id: cf6cd73b-9189-4942-a61d-878fcf76b1c1
x-ms-routing-request-id: SOUTHINDIA:20180521T105825Z:cf6cd73b-9189-4942-a61d-878fcf76b1c1
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:58:25 GMT
X-Powered-By: ASP.NET
```

### <a name="selecting-the-relevant-azure-vm"></a>De relevante Azure VM selecteren

 U bevestigen dat 'caching' wordt gedaan door [alle beschermbare items](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list) onder het abonnement te vermelden en de gewenste VM in het antwoord te vinden. [De reactie van deze bewerking](#example-responses-1) geeft u ook informatie over hoe Recovery Services een VM identificeert.  Zodra u bekend bent met het patroon, u deze stap overslaan en direct overgaan tot [het inschakelen van bescherming.](#enabling-protection-for-the-azure-vm)

Deze bewerking is een *GET-bewerking.*

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter=backupManagementType eq 'AzureIaasVM'
```

De *GET* URI heeft alle vereiste parameters. Er is geen extra aanvraaginstantie nodig.

#### <a name="responses"></a><a name="responses-1"></a>Antwoorden

|Name  |Type  |Beschrijving  |
|---------|---------|---------|
|200 OK     | [WorkloadProtectableItemResourceList](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list#workloadprotectableitemresourcelist)        |       OK |

#### <a name="example-responses"></a><a name="example-responses-1"></a>Voorbeeldreacties

Zodra de *GET-aanvraag* is ingediend, wordt een antwoord van 200 (OK) geretourneerd.

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: 7c2cf56a-e6be-4345-96df-c27ed849fe36
x-ms-client-request-id: 40c8601a-c217-4c68-87da-01db8dac93dd; 40c8601a-c217-4c68-87da-01db8dac93dd
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14979
x-ms-correlation-request-id: 7c2cf56a-e6be-4345-96df-c27ed849fe36
x-ms-routing-request-id: SOUTHINDIA:20180521T071408Z:7c2cf56a-e6be-4345-96df-c27ed849fe36
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:14:08 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainerv2;testRG;testVM/protectableItems/vm;iaasvmcontainerv2;testRG;testVM",
      "name": "iaasvmcontainerv2;testRG;testVM",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
      "properties": {
        "virtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
        "virtualMachineVersion": "Compute",
        "resourceGroup": "testRG",
        "backupManagementType": "AzureIaasVM",
        "protectableItemType": "Microsoft.Compute/virtualMachines",
        "friendlyName": "testVM",
        "protectionState": "NotProtected"
      }
    },……………..

```

> [!TIP]
> Het aantal waarden in een *GET-respons* is beperkt tot 200 voor een 'pagina'. Gebruik het veld 'nextLink' om de URL voor de volgende set reacties te krijgen.

Het antwoord bevat de lijst met alle `{value}` onbeveiligde Azure VM's en bevat alle informatie die azure recovery service nodig heeft om back-ups te configureren. Als u een `{name}` back-up `{virtualMachineId}` wilt `{properties}` configureren, noteert u het veld en het veld in sectie. Maak twee variabelen uit deze veldwaarden zoals hieronder vermeld.

- containerNaam = "iaasvmcontainer;" +`{name}`
- protectedItemName = "vm;" +`{name}`
- `{virtualMachineId}`wordt later gebruikt in [de aanvraaginstantie](#example-request-body)

In het voorbeeld vertalen de bovenstaande waarden naar:

- containerName = "iaasvmcontainer;iaasvmcontainerv2;testRG;testVM"
- protectedItemName = "vm;iaasvmcontainerv2;testRG;testVM"

### <a name="enabling-protection-for-the-azure-vm"></a>Beveiliging inschakelen voor de Azure VM

Nadat de relevante VM is "gecached" en "geïdentificeerd", selecteert u het te beschermen beleid. Voor meer informatie over bestaand beleid in de kluis verwijzen naar [lijst Beleids API](https://docs.microsoft.com/rest/api/backup/backuppolicies/list). Selecteer vervolgens het [relevante beleid](/rest/api/backup/protectionpolicies/get) door te verwijzen naar de beleidsnaam. Als u beleid wilt maken, verwijst u naar [beleidszelfstudie maken](backup-azure-arm-userestapi-createorupdatepolicy.md). In het onderstaande voorbeeld wordt 'Standaardbeleid' geselecteerd.

Beveiliging inschakelen is een asynchrone *PUT-bewerking* die een 'beveiligd item' maakt.

```http
https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

De `{containerName}` `{protectedItemName}` en zijn zoals hierboven gebouwd. Het `{fabricName}` is "Azure". Voor ons voorbeeld vertaalt dit zich in:

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2019-05-13
```

#### <a name="create-the-request-body"></a>De aanvraaginstantie maken

Als u een beveiligd item wilt maken, volgen de onderdelen van de aanvraaginstantie.

|Name  |Type  |Beschrijving  |
|---------|---------|---------|
|properties     | AzureIaaSVMProtectedItem        |Eigenschappen van ProtectedItem-bron         |

Raadpleeg voor de volledige lijst met definities van de aanvraaginstantie en andere details het beheer van [het API-document voor beveiligde items REST](https://docs.microsoft.com/rest/api/backup/protecteditems/createorupdate#request-body).

##### <a name="example-request-body"></a>Voorbeeldaanvraaginstantie

In de volgende aanvraaginstantie worden eigenschappen gedefinieerd die nodig zijn om een beveiligd item te maken.

```json
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy"
  }
}
```

Het `{sourceResourceId}` is `{virtualMachineId}` het hierboven genoemde uit de [reactie van de lijst beschermbare items](#example-responses-1).

#### <a name="responses"></a>Antwoorden

Het maken van een beveiligd item is een [asynchrone bewerking](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Dit betekent dat deze bewerking een andere bewerking maakt die afzonderlijk moet worden bijgehouden.

Het retourneert twee antwoorden: 202 (Geaccepteerd) wanneer een andere bewerking wordt gemaakt en vervolgens 200 (OK) wanneer die bewerking is voltooid.

|Name  |Type  |Beschrijving  |
|---------|---------|---------|
|200 OK     |    [ProtectedItemResource](https://docs.microsoft.com/rest/api/backup/protecteditemoperationresults/get#protecteditemresource)     |  OK       |
|202 Geaccepteerd     |         |     Geaccepteerd    |

##### <a name="example-responses"></a>Voorbeeldreacties

Zodra u het *PUT-verzoek* voor het maken of bijwerken van beveiligde items hebt ingediend, is het eerste antwoord 202 (Geaccepteerd) met een locatiekop- of Azure-async-header.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-client-request-id: e1f94eef-9b2d-45c4-85b8-151e12b07d03; e1f94eef-9b2d-45c4-85b8-151e12b07d03
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-routing-request-id: SOUTHINDIA:20180521T073907Z:db785be0-bb20-4598-bc9f-70c9428b170b
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:39:06 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Houd vervolgens de resulterende bewerking bij met behulp van de locatiekop- of Azure-AsyncOperation-header met een eenvoudige *OPDRACHT GET.*

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
```

Zodra de bewerking is voltooid, retourneert deze 200 (OK) met de beveiligde iteminhoud in de antwoordtekst.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM",
  "name": "VM;testRG;testVM",
  "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems",
  "properties": {
    "friendlyName": "testVM",
    "virtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "protectionStatus": "Healthy",
    "protectionState": "IRPending",
    "healthStatus": "Passed",
    "lastBackupStatus": "",
    "lastBackupTime": "2001-01-01T00:00:00Z",
    "protectedItemDataId": "17592691116891",
    "extendedInfo": {
      "recoveryPointCount": 0,
      "policyInconsistent": false
    },
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "backupManagementType": "AzureIaasVM",
    "workloadType": "VM",
    "containerName": "iaasvmcontainerv2;testRG;testVM",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy",
    "policyName": "DefaultPolicy"
  }
}
```

Dit bevestigt dat de beveiliging is ingeschakeld voor de VM en dat de eerste back-up wordt geactiveerd volgens het beleidsschema.

## <a name="trigger-an-on-demand-backup-for-a-protected-azure-vm"></a>Een on-demand back-up activeren voor een beveiligde Azure VM

Zodra een Azure VM is geconfigureerd voor back-ups, worden back-ups uitgevoerd volgens het beleidsschema. U op elk gewenst moment wachten op de eerste geplande back-up of een on-demand back-up activeren. Retentie voor on-demand back-ups staat los van de retentie van het back-upbeleid en kan worden opgegeven tot een bepaalde datumtijd. Als dit niet is opgegeven, wordt ervan uitgegaan dat dit 30 dagen vanaf de dag van de trigger van on-demand back-up is.

Het activeren van een on-demand back-up is een *POST-bewerking.*

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

De `{containerName}` `{protectedItemName}` en zijn zoals [hierboven](#responses-1)gebouwd . Het `{fabricName}` is "Azure". Voor ons voorbeeld vertaalt dit zich in:

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM/backup?api-version=2016-12-01
```

### <a name="create-the-request-body"></a>De aanvraaginstantie maken

Om een on-demand back-up te activeren, volgen de onderdelen van de aanvraaginstantie.

|Name  |Type  |Beschrijving  |
|---------|---------|---------|
|properties     | [IaaSVMBackupRequest](https://docs.microsoft.com/rest/api/backup/backups/trigger#iaasvmbackuprequest)        |BackupRequestResource-eigenschappen         |

Raadpleeg voor de volledige lijst met definities van de aanvraaginstantie en andere details [back-ups voor beveiligde items REST API-document.](https://docs.microsoft.com/rest/api/backup/backups/trigger#request-body)

#### <a name="example-request-body"></a>Voorbeeldaanvraaginstantie

De volgende aanvraaginstantie definieert eigenschappen die nodig zijn om een back-up voor een beveiligd item te activeren. Als de retentie niet is opgegeven, wordt deze 30 dagen bewaard vanaf het moment van trigger van de back-uptaak.

```json
{
   "properties": {
    "objectType": "IaasVMBackupRequest",
    "recoveryPointExpiryTimeInUTC": "2018-12-01T02:16:20.3156909Z"
  }
}
```

### <a name="responses"></a>Antwoorden

Het activeren van een on-demand back-up is een [asynchrone bewerking.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) Dit betekent dat deze bewerking een andere bewerking maakt die afzonderlijk moet worden bijgehouden.

Het retourneert twee antwoorden: 202 (Geaccepteerd) wanneer een andere bewerking wordt gemaakt en vervolgens 200 (OK) wanneer die bewerking is voltooid.

|Name  |Type  |Beschrijving  |
|---------|---------|---------|
|202 Geaccepteerd     |         |     Geaccepteerd    |

#### <a name="example-responses"></a><a name="example-responses-3"></a>Voorbeeldreacties

Zodra u de *POST-aanvraag* voor een on-demand back-up hebt ingediend, is het eerste antwoord 202 (Geaccepteerd) met een locatiekop- of Azure-async-header.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: 7885ca75-c7c6-43fb-a38c-c0cc437d8810
x-ms-client-request-id: 7df8e874-1d66-4f81-8e91-da2fe054811d; 7df8e874-1d66-4f81-8e91-da2fe054811d
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 7885ca75-c7c6-43fb-a38c-c0cc437d8810
x-ms-routing-request-id: SOUTHINDIA:20180521T083541Z:7885ca75-c7c6-43fb-a38c-c0cc437d8810
Cache-Control: no-cache
Date: Mon, 21 May 2018 08:35:41 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationResults/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Houd vervolgens de resulterende bewerking bij met behulp van de locatiekop- of Azure-AsyncOperation-header met een eenvoudige *OPDRACHT GET.*

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
```

Zodra de bewerking is voltooid, retourneert deze 200 (OK) met de id van de resulterende back-uptaak in de antwoordinstantie.

```json
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: a8b13524-2c95-445f-b107-920806f385c1
x-ms-client-request-id: 5a63209d-3708-4e69-a75f-9499f4c8977c; 5a63209d-3708-4e69-a75f-9499f4c8977c
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14995
x-ms-correlation-request-id: a8b13524-2c95-445f-b107-920806f385c1
x-ms-routing-request-id: SOUTHINDIA:20180521T083723Z:a8b13524-2c95-445f-b107-920806f385c1
Cache-Control: no-cache
Date: Mon, 21 May 2018 08:37:22 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "00000000-0000-0000-0000-000000000000",
  "status": "Succeeded",
  "startTime": "2018-05-21T08:35:40.9488967Z",
  "endTime": "2018-05-21T08:35:40.9488967Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "7ddead57-bcb9-4269-ac31-6a1b57588700"
  }
}
```

Aangezien de back-uptaak een langdurige bewerking is, moet deze worden bijgehouden zoals uitgelegd in de [monitortaken met rest-API-document.](backup-azure-arm-userestapi-managejobs.md#tracking-the-job)

## <a name="modify-the-backup-configuration-for-a-protected-azure-vm"></a>De back-upconfiguratie voor een beveiligde Azure-vm wijzigen

### <a name="changing-the-policy-of-protection"></a>Het beschermingsbeleid wijzigen

Als u het beleid wilt wijzigen waarmee VM is beveiligd, u dezelfde indeling gebruiken als [het inschakelen van beveiliging.](#enabling-protection-for-the-azure-vm) Geef de nieuwe beleids-ID in [de aanvraaginstantie](#example-request-body) op en dien het verzoek in. Bijvoorbeeld: Als u het beleid van testVM wilt wijzigen van 'DefaultPolicy' naar 'ProdPolicy', geeft u de ID 'ProdPolicy' op in de aanvraaginstantie.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/ProdPolicy"
  }
}
```

Het antwoord volgt hetzelfde formaat als vermeld [voor het mogelijk maken van bescherming](#responses-2)

### <a name="stop-protection-but-retain-existing-data"></a>De beveiliging stoppen, maar bestaande gegevens bewaren

Als u de beveiliging van een beveiligde virtuele machine wilt verwijderen, maar de gegevens waarover al een back-up is gemaakt, wilt u het beleid in de aanvraaginstantie verwijderen en het verzoek indienen. Zodra de koppeling met het beleid is verwijderd, worden back-ups niet meer geactiveerd en worden er geen nieuwe herstelpunten gemaakt.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": ""
  }
}
```

Het antwoord volgt hetzelfde formaat als vermeld [voor het activeren van een on-demand back-up](#example-responses-3). De resulterende taak moet worden bijgehouden zoals uitgelegd in de [monitortaken met rest-API-document](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

### <a name="stop-protection-and-delete-data"></a>Beveiliging stoppen en gegevens verwijderen

Als u de beveiliging op een beveiligde virtuele machine wilt verwijderen en de back-upgegevens ook wilt verwijderen, voert u een verwijderingsbewerking uit zoals hier wordt [beschreven.](https://docs.microsoft.com/rest/api/backup/protecteditems/delete)

Het stoppen van beveiliging en het verwijderen van gegevens is een *DELETE-bewerking.*

```http
DELETE https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

De `{containerName}` `{protectedItemName}` en zijn zoals [hierboven](#responses-1)gebouwd . `{fabricName}`is "Azure". Voor ons voorbeeld vertaalt dit zich in:

```http
DELETE https://management.azure.com//Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2019-05-13
```

#### <a name="responses"></a><a name="responses-2"></a>Antwoorden

*DELETE-beveiliging* is een [asynchrone bewerking](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Dit betekent dat deze bewerking een andere bewerking maakt die afzonderlijk moet worden bijgehouden.

Het retourneert twee antwoorden: 202 (Geaccepteerd) wanneer een andere bewerking wordt gemaakt en vervolgens 204 (NoContent) wanneer die bewerking is voltooid.

|Name  |Type  |Beschrijving  |
|---------|---------|---------|
|204 NoContent     |         |  NoContent       |
|202 Geaccepteerd     |         |     Geaccepteerd    |

> [!IMPORTANT]
> Om te beschermen tegen onbedoelde verwijderingsscenario's, is er een [soft-delete-functie beschikbaar](use-restapi-update-vault-properties.md#soft-delete-state) voor vault van herstelservices. Als de status soft-delete van de kluis is ingesteld op ingeschakeld, wordt de gegevens niet onmiddellijk verwijderd door de verwijderingsbewerking. Het zal worden bewaard voor 14 dagen en vervolgens permanent gezuiverd. De klant wordt gedurende deze periode van 14 dagen niet in rekening gebracht voor opslag. Als u de verwijderingsbewerking ongedaan wilt maken, raadpleegt u de [sectie ongedaan maken verwijderen](#undo-the-stop-protection-and-delete-data).

### <a name="undo-the-stop-protection-and-delete-data"></a>De stopbeveiliging ongedaan maken en gegevens verwijderen

Het ongedaan maken van de onbedoelde verwijdering is vergelijkbaar met het maken van het back-upitem. Nadat het verwijderen is ongedaan gemaakt, blijft het item behouden, maar worden er geen toekomstige back-ups geactiveerd.

Verwijdering ongedaan maken is een *PUT-bewerking* die sterk lijkt op [het wijzigen van het beleid](#changing-the-policy-of-protection) en/of het inschakelen van de [bescherming.](#enabling-protection-for-the-azure-vm) Geef gewoon de intentie om de verwijdering ongedaan te maken met de variabele *isRehydraat* in [de aanvraaginstantie](#example-request-body) en het verzoek in te dienen. Bijvoorbeeld: Om de verwijdering voor testVM ongedaan te maken, moet de volgende aanvraaginstantie worden gebruikt.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "protectionState": "ProtectionStopped",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "isRehydrate": true
  }
}
```

Het antwoord volgt hetzelfde formaat als vermeld [voor het activeren van een on-demand back-up](#example-responses-3). De resulterende taak moet worden bijgehouden zoals uitgelegd in de [monitortaken met rest-API-document](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

## <a name="next-steps"></a>Volgende stappen

[Gegevens herstellen van een back-up van een Azure Virtual-machine.](backup-azure-arm-userestapi-restoreazurevms.md)

Zie de volgende documenten voor meer informatie over de AZURE Backup REST API's:

- [REST-API van Azure Recovery Services-provider](/rest/api/recoveryservices/)
- [Aan de slag gaan met Azure REST API](/rest/api/azure/)
