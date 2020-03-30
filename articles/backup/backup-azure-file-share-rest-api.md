---
title: Back-ups maken van Azure-bestandsshares met REST API
description: Meer informatie over het gebruik van REST API om back-ups te maken van Azure-bestandsshares in de Vault voor Herstelservices
ms.topic: conceptual
ms.date: 02/16/2020
ms.openlocfilehash: 2cf385830ec1be17cb62432e6ef9cba7d82a9db1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248097"
---
# <a name="backup-azure-file-share-using-azure-backup-via-rest-api"></a>Back-up maken van Azure-bestandsshare met Azure Backup via Rest API

In dit artikel wordt beschreven hoe u een back-up maakt van een Azure File Share met Azure Backup via REST API.

In dit artikel wordt ervan uitgegaan dat u al een kluis voor herstelservices hebt gemaakt en een beleid hebt gemaakt voor het configureren van back-ups voor uw bestandsshare. Als u dit nog niet hebt gedaan, [raadpleegt](https://docs.microsoft.com/azure/backup/backup-azure-arm-userestapi-createorupdatevault) u de kluis maken en [maakt u zelfstudies](https://docs.microsoft.com/azure/backup/backup-azure-arm-userestapi-createorupdatepolicy) voor beleidsREST API voor het maken van nieuwe kluizen en beleidsregels.

Voor dit artikel gebruiken we de volgende bronnen:

- **RecoveryServicesVault**: *azurefilesvault*

- **Beleid:** *schema1*

- **Resourcegroep**: *azurefiles*

- **Opslagaccount**: *testvault2*

- **Bestandsshare:** *testshare*

## <a name="configure-backup-for-an-unprotected-azure-file-share-using-rest-api"></a>Back-up configureren voor een onbeveiligd Azure-bestandsshare met REST API

### <a name="discover-storage-accounts-with-unprotected-azure-file-shares"></a>Opslagaccounts ontdekken met onbeveiligde Azure-bestandsshares

De kluis moet alle Azure-opslagaccounts in het abonnement ontdekken met bestandsshares waarvan een back-up kan worden gemaakt naar de Vault voor Herstelservices. Dit wordt geactiveerd met behulp van de [vernieuwingsbewerking](https://docs.microsoft.com/rest/api/backup/protectioncontainers/refresh). Het is een asynchrone *POST-bewerking* die ervoor zorgt dat de kluis de nieuwste lijst krijgt van alle onbeveiligde Azure File-shares in het huidige abonnement en ze 'caches' maakt. Zodra het bestandsaandeel in de cache is opgeslagen, hebben herstelservices toegang tot het bestandsaandeel en deze kunnen beveiligen.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01&$filter={$filter}
```

De POST `{subscriptionId}`URI `{vaultName}` `{vaultresourceGroupName}`heeft `{fabricName}` , , , en parameters. In ons voorbeeld zou de waarde voor de verschillende parameters als volgt zijn:

- `{fabricName}`is *Azure*

- `{vaultName}`is *azurefilesvault*

- `{vaultresourceGroupName}`is *azurefiles*

- $filter=backupManagementType eq 'AzureStorage'

Aangezien alle vereiste parameters in de URI worden gegeven, is er geen behoefte aan een aparte aanvraaginstantie.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

#### <a name="responses"></a>Antwoorden

De bewerking 'vernieuwen' is een [asynchrone bewerking.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) Dit betekent dat deze bewerking een andere bewerking maakt die afzonderlijk moet worden bijgehouden.

Het retourneert twee antwoorden: 202 (Geaccepteerd) wanneer een andere bewerking wordt gemaakt en 200 (OK) wanneer die bewerking is voltooid.

##### <a name="example-responses"></a>Voorbeeldreacties

Zodra de *POST-aanvraag* is ingediend, wordt een antwoord van 202 (Geaccepteerd) geretourneerd.

```http
HTTP/1.1 202 Accepted
'Pragma': 'no-cache'
'Expires': '-1'
'Location': ‘https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/ResourceGroups
/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/operationResults/
cca47745-12d2-42f9-b3a4-75335f18fdf6?api-version=2016-12-01’
'Retry-After': '60'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'x-ms-client-request-id': ‘3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61’
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-reads': '11996'
'x-ms-correlation-request-id': '6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'x-ms-routing-request-id': CENTRALUSEUAP:20200203T091326Z:6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'Date': 'Mon, 03 Feb 2020 09:13:25 GMT'
```

De resulterende bewerking bijhouden met de koptekst 'Locatie' met een eenvoudige *opdracht GET*

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/operationResults/cca47745-12d2-42f9-b3a4-75335f18fdf6?api-version=2016-12-01
```

Zodra alle Azure Storage-accounts zijn ontdekt, retourneert de opdracht GET een reactie van 200 (Geen inhoud). De kluis kan nu elk opslagaccount met bestandsshares ontdekken waar binnen het abonnement een back-up van kan worden gemaakt.

```http
HTTP/1.1 200 NoContent
Cache-Control  : no-cache
Pragma   : no-cache
X-Content-Type-Options  : nosniff
x-ms-request-id    : d9bdb266-8349-4dbd-9688-de52f07648b2
x-ms-client-request-id  : 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security  : max-age=31536000; includeSubDomains
X-Powered-By    : ASP.NET
x-ms-ratelimit-remaining-subscription-reads: 11933
x-ms-correlation-request-id   : d9bdb266-8349-4dbd-9688-de52f07648b2
x-ms-routing-request-id  : CENTRALUSEUAP:20200127T105304Z:d9bdb266-8349-4dbd-9688-de52f07648b2
Date   : Mon, 27 Jan 2020 10:53:04 GMT
```

### <a name="get-list-of-storage-accounts-that-can-be-protected-with-recovery-services-vault"></a>Lijst met opslagaccounts krijgen die kunnen worden beveiligd met de vault van Recovery Services

Als u wilt bevestigen dat 'caching' wordt uitgevoerd, vermeldt u alle beschermbare opslagaccounts onder het abonnement. Zoek vervolgens het gewenste opslagaccount in het antwoord. Dit gebeurt met behulp van de [GET ProtectableContainers](https://docs.microsoft.com/rest/api/backup/protectablecontainers/list) operatie.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectableContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

De *GET* URI heeft alle vereiste parameters. Er is geen extra aanvraaginstantie nodig.

Voorbeeld van responslichaam:

```json
{

  "value": [

    {

      "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers
 /Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/

protectableContainers/StorageContainer;Storage;AzureFiles;testvault2",

      "name": "StorageContainer;Storage;AzureFiles;testvault2",

      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectableContainers",

      "properties": {

        "friendlyName": "testvault2",

        "backupManagementType": "AzureStorage",

        "protectableContainerType": "StorageContainer",

        "healthStatus": "Healthy",

        "containerId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/
 AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2"

      }

    }

  ]

}
```

Aangezien we het *testvault2-opslagaccount* in de responsinstantie met de vriendelijke naam kunnen vinden, is de bovenstaande vernieuwingsbewerking succesvol. De kluis van herstelservices kan nu met succes opslagaccounts met onbeveiligde bestanden in hetzelfde abonnement ontdekken.

### <a name="register-storage-account-with-recovery-services-vault"></a>Opslagaccount registreren bij vault Recovery Services

Deze stap is alleen nodig als u het opslagaccount niet eerder bij de kluis hebt geregistreerd. U de kluis registreren via de [ProtectionContainers-Register operatie.](https://docs.microsoft.com/rest/api/backup/protectioncontainers/register)

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}?api-version=2016-12-01
```

Stel de variabelen voor de URI als volgt in:

- {resourceGroupName} - *azurefiles*
- {fabricName} - *Azure*
- {vaultName} - *azurefilesvault*
- {containerName} - Dit is het naamkenmerk in de reactietekst van de bewerking GET ProtectableContainers.
   In ons voorbeeld is het *StorageContainer; Opslag; AzureFiles;testvault2*

>[!NOTE]
> Neem altijd het naamkenmerk van het antwoord en vul het in dit verzoek in. Niet hardcoderen of de containernaamnotatie maken. Als u deze maakt of hard-code, mislukt de API-aanroep als de containernaamnotatie in de toekomst verandert.

<br>

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2?api-version=2016-12-01
```

De instantie voor aanvragen maken is als volgt:

```json
{

 "properties": {


  "containerType": "StorageContainer",


  "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",


  "resourceGroup": "AzureFiles",


  "friendlyName": "testvault2",


  "backupManagementType": "AzureStorage"


 }
```

Voor de volledige lijst van definities van de aanvraaginstantie en andere details verwijzen wij u naar [ProtectionContainers-Register](https://docs.microsoft.com/rest/api/backup/protectioncontainers/register#azurestoragecontainer).

Dit is een asynchrone bewerking en retourneert twee antwoorden: "202 Geaccepteerd" wanneer de bewerking wordt geaccepteerd en "200 OK" wanneer de bewerking is voltooid.  Als u de bewerkingsstatus wilt bijhouden, gebruikt u de locatiekoptekst om de laatste status van de bewerking te krijgen.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/operationresults/1a3c8ee7-e0e5-43ed-b8b3-73cc992b6db9?api-version=2016-12-01
```

Voorbeeld van antwoordlichaam wanneer de bewerking is voltooid:

```json
{
    "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/
protectionContainers/StorageContainer;Storage;AzureFiles;testvault2",
    "name": "StorageContainer;Storage;AzureFiles;testvault2",
    "properties": {
        "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
        "protectedItemCount": 0,
        "friendlyName": "testvault2",
        "backupManagementType": "AzureStorage",
        "registrationStatus": "Registered",
        "healthStatus": "Healthy",
        "containerType": "StorageContainer",
        "protectableObjectType": "StorageContainer"
    }
}
```

U controleren of de registratie is geslaagd op basis van de waarde van de parameter *registratiestatus* in de antwoordinstantie. In ons geval toont het de status als geregistreerd voor *testvault2,* dus de registratie operatie was succesvol.

### <a name="inquire-all-unprotected-files-shares-under-a-storage-account"></a>Alle onbeveiligde bestanden onder een opslagaccount informeer

U informatie stellen over beschermbare artikelen in een opslagaccount met behulp van de bewerking [Protection Containers-Inquire.](https://docs.microsoft.com/rest/api/backup/protectioncontainers/inquire) Het is een asynchrone bewerking en de resultaten moeten worden bijgehouden met behulp van de locatieheader.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/inquire?api-version=2016-12-01
```

Stel de variabelen voor de bovenstaande URI als volgt in:

- {vaultName} - *azurefilesvault*
- {fabricName} - *Azure*
- {containerName}- Raadpleeg het naamkenmerk in de reactietekst van de bewerking GET ProtectableContainers. In ons voorbeeld is het *StorageContainer; Opslag; AzureFiles;testvault2*

```http
https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/inquire?api-version=2016-12-01
```

Zodra de aanvraag is geslaagd, wordt de statuscode 'OK' geretourneerd

```http
Cache-Control : no-cache
Pragma   : no-cache
X-Content-Type-Options: nosniff
x-ms-request-id  : 68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
x-ms-client-request-id  : 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security: max-age=31536000; includeSubDomains
Server  : Microsoft-IIS/10.0
X-Powered-B : ASP.NET
x-ms-ratelimit-remaining-subscription-reads: 11932
x-ms-correlation-request-id  : 68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
x-ms-routing-request-id   : CENTRALUSEUAP:20200127T105305Z:68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
Date  : Mon, 27 Jan 2020 10:53:05 GMT
```

### <a name="select-the-file-share-you-want-to-back-up"></a>Selecteer het bestandsaandeel waarop u een back-up wilt maken

U alle beschermbare items onder het abonnement weergeven en het gewenste bestandsaandeel vinden waareen back-up van moet worden gemaakt met behulp van de bewerking [GET backupprotectableItems.](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list)

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter={$filter}
```

Bouw de URI als volgt:

- {vaultName} - *azurefilesvault*
- {$filter} - *backupManagementType eq 'AzureStorage'*

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupProtectableItems?$filter=backupManagementType eq 'AzureStorage'&api-version=2016-12-01
```

Voorbeeldrespons:

```json
Status Code:200

{
    "value": [
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afaccount1/protectableItems/azurefileshare;azurefiles1",
            "name": "azurefileshare;azurefiles1",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afaccount1",
                "parentContainerFriendlyName": "afaccount1",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "azurefiles1",
                "protectionState": "NotProtected"
            }
        },
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afsaccount/protectableItems/azurefileshare;afsresource",
            "name": "azurefileshare;afsresource",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
                "parentContainerFriendlyName": "afsaccount",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "afsresource",
                "protectionState": "NotProtected"
            }
        },
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;testvault2/protectableItems/azurefileshare;testshare",
            "name": "azurefileshare;testshare",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
                "parentContainerFriendlyName": "testvault2",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "testshare",
                "protectionState": "NotProtected"
            }
        }
    ]
}
```

Het antwoord bevat de lijst met alle onbeveiligde bestandsshares en bevat alle informatie die de Azure Recovery Service nodig heeft om de back-up te configureren.

### <a name="enable-backup-for-the-file-share"></a>Back-up voor het bestandsshare inschakelen

Nadat het relevante bestandsaandeel is "geïdentificeerd" met de vriendelijke naam, selecteert u het beleid om te beschermen. Raadpleeg [de lijst BeleidsAPI](https://docs.microsoft.com/rest/api/backup/backuppolicies/list)voor meer informatie over bestaand beleid in de kluis. Selecteer vervolgens het [relevante beleid](https://docs.microsoft.com/rest/api/backup/protectionpolicies/get) door te verwijzen naar de beleidsnaam. Als u beleid wilt maken, verwijst u naar [beleidszelfstudie maken](https://docs.microsoft.com/azure/backup/backup-azure-arm-userestapi-createorupdatepolicy).

Beveiliging inschakelen is een asynchrone *PUT-bewerking* die een 'beveiligd item' maakt.

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

Stel de **containernaam-** en **protecteditemnamevariabelen** in met het kenmerk ID in de antwoordtekst van de bewerking GET backupprotectableitems.

In ons voorbeeld is de id van bestandsshare die we willen beveiligen:

```output
"/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;testvault2/protectableItems/azurefileshare;testshare
```

- {containername} - *storagecontainer;storage;azurefiles;testvault2*
- {protectedItemName} - *azurefileshare;testshare*

U ook verwijzen naar het **naamkenmerk** van de beveiligingscontainer en de antwoorden op het beschermde artikel.

>[!NOTE]
>Neem altijd het naamkenmerk van het antwoord en vul het in dit verzoek in. Niet hardcoderen of maak de containernaamnotatie of beveiligde itemnaamnotatie. Als u deze maakt of hard-code, mislukt de API-aanroep als de containernaamnotatie of beveiligde itemnaamnotatie in de toekomst verandert.

<br>

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare?api-version=2016-12-01
```

Een aanvraaginstantie maken:

In de volgende aanvraaginstantie worden eigenschappen gedefinieerd die nodig zijn om een beveiligd item te maken.

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupPolicies/schedule1"
  }
}
```

De **sourceResourceId** is de **parentcontainerFabricID** in reactie op GET backupprotectableItems.

Voorbeeldreactie

Het maken van een beveiligd item is een asynchrone bewerking, waardoor een andere bewerking wordt gemaakt die moet worden bijgehouden. Het retourneert twee antwoorden: 202 (Geaccepteerd) wanneer een andere bewerking wordt gemaakt en 200 (OK) wanneer die bewerking is voltooid.

Zodra u het *PUT-verzoek* voor het maken of bijwerken van beveiligde items hebt ingediend, is het eerste antwoord 202 (Geaccepteerd) met een locatiekoptekst.

```http
HTTP/1.1 202 Accepted
Cache-Control  : no-cache
Pragma  : no-cache
Location : https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare/operationResults/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
Retry-Afte  : 60
Azure-AsyncOperation  : https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare/operationResults/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
X-Content-Type-Options : nosniff
x-ms-request-id : b55527fa-f473-4f09-b169-9cc3a7a39065
x-ms-client-request-id: 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security : max-age=31536000; includeSubDomains
X-Powered-By  : ASP.NET
x-ms-ratelimit-remaining-subscription-writes: 1198
x-ms-correlation-request-id : b55527fa-f473-4f09-b169-9cc3a7a39065
x-ms-routing-request-id  : CENTRALUSEUAP:20200127T105412Z:b55527fa-f473-4f09-b169-9cc3a7a39065
Date : Mon, 27 Jan 2020 10:54:12 GMT
```

Houd vervolgens de resulterende bewerking bij met behulp van de locatiekop- of Azure-AsyncOperation-header met een *get-opdracht.*

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
```

Zodra de bewerking is voltooid, retourneert deze 200 (OK) met de beveiligde iteminhoud in de antwoordtekst.

Voorbeeldresponsinstantie:

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

Dit bevestigt dat de beveiliging is ingeschakeld voor het delen van bestanden en dat de eerste back-up wordt geactiveerd volgens het beleidsschema.

## <a name="trigger-an-on-demand-backup-for-file-share"></a>Een on-demand back-up activeren voor bestandsshare

Zodra een Azure-bestandsshare is geconfigureerd voor back-ups, worden back-ups uitgevoerd volgens het beleidsschema. U op elk gewenst moment wachten op de eerste geplande back-up of een on-demand back-up activeren.

Het activeren van een on-demand back-up is een POST-bewerking.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

{containerName} en {protectedItemName} zijn zoals hierboven opgebouwd en schakelen back-up in. Voor ons voorbeeld vertaalt dit zich in:

```http
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/backup?api-version=2017-07-01
```

### <a name="create-request-body"></a>Aanvraaginstantie maken

Om een on-demand back-up te activeren, volgen de onderdelen van de aanvraaginstantie.

| Name       | Type                       | Beschrijving                       |
| ---------- | -------------------------- | --------------------------------- |
| Eigenschappen | AzurefilesharebackupReques | BackupRequestResource-eigenschappen |

Raadpleeg voor de volledige lijst met definities van de aanvraaginstantie en andere details [back-ups voor beveiligde items REST API-document.](https://docs.microsoft.com/rest/api/backup/backups/trigger#request-body)

Voorbeeld van aanvraaginstantie

```json
{

  "properties":{

   "objectType":"AzureFileShareBackupRequest",
    "recoveryPointExpiryTimeInUTC":"2020-03-07T18:29:59.000Z"
}

}
```

### <a name="responses"></a>Antwoorden

Het activeren van een on-demand back-up is een [asynchrone bewerking.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) Dit betekent dat deze bewerking een andere bewerking maakt die afzonderlijk moet worden bijgehouden.

Het retourneert twee antwoorden: 202 (Geaccepteerd) wanneer een andere bewerking wordt gemaakt en 200 (OK) wanneer die bewerking is voltooid.

### <a name="example-responses"></a>Voorbeeldreacties

Zodra u de *POST-aanvraag* voor een on-demand back-up hebt ingediend, is het eerste antwoord 202 (Geaccepteerd) met een locatiekop- of Azure-async-header.

```http
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationResults/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2017-07-01
'Retry-After': '60'
'Azure-AsyncOperation': https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationsStatus/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2017-07-01
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'x-ms-client-request-id': 'a644712a-4895-11ea-ba57-0a580af42708, a644712a-4895-11ea-ba57-0a580af42708'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1199'
'x-ms-correlation-request-id': '2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T040339Z:2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'Date': 'Thu, 06 Feb 2020 04:03:38 GMT'
'Content-Length': '0'
```

Houd vervolgens de resulterende bewerking bij met behulp van de locatiekop- of Azure-AsyncOperation-header met een *get-opdracht.*

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2016-12-01
```

Zodra de bewerking is voltooid, retourneert deze 200 (OK) met de id van de resulterende back-uptaak in de antwoordinstantie.

#### <a name="sample-response-body"></a>Voorbeeldresponsinstantie

```json
{
    "id": "dc62d524-427a-4093-968d-e951c0a0726e",
    "name": "dc62d524-427a-4093-968d-e951c0a0726e",
    "status": "Succeeded",
    "startTime": "2020-02-06T11:06:02.1327954Z",
    "endTime": "2020-02-06T11:06:02.1327954Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "39282261-cb52-43f5-9dd0-ffaf66beeaef"
    }
}
```

Aangezien de back-uptaak een langdurige bewerking is, moet deze worden bijgehouden zoals uitgelegd in de [monitortaken met rest-API-document.](https://docs.microsoft.com/azure/backup/backup-azure-arm-userestapi-managejobs#tracking-the-job)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [herstellen van Azure-bestandsshares met rest-API](restore-azure-file-share-rest-api.md).
