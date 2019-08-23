---
title: Een gebruikers delegering SA'S maken voor een container of BLOB met Power shell (preview)-Azure Storage
description: Meer informatie over het maken van een Shared Access Signature (SAS) met behulp van Azure Active Directory referenties in Azure Storage met behulp van Power shell.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/12/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: bdb66ec65d493c6af2f33bf6ed6e4a2bb2154235
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69897027"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-powershell-preview"></a>Een gebruikers delegering SA'S maken voor een container of BLOB met Power shell (preview)

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

In dit artikel wordt beschreven hoe u Azure Active Directory (Azure AD)-referenties gebruikt voor het maken van een gebruikers delegering SA'S voor een container of BLOB met Azure PowerShell (preview).

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-preview-module"></a>De preview-module installeren

Als u Power shell wilt gebruiken voor het maken van een SA'S voor het delegeren van gebruikers, moet u eerst de module AZ. Storage 1.3.1-Preview installeren. Volg deze stappen om de module te installeren:

1. Eerdere installaties van Azure PowerShell verwijderen:

    - Verwijder eerdere installaties van Azure PowerShell van Windows met de instelling **Apps & onderdelen** onder **instellingen**.
    - Verwijder alle **Azure** -modules `%Program Files%\WindowsPowerShell\Modules`uit.

1. Zorg ervoor dat de meest recente versie van PowerShellGet is geïnstalleerd. Open een Windows Power shell-venster en voer de volgende opdracht uit om de meest recente versie te installeren:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. Sluit het Power shell-venster en open het opnieuw nadat u PowerShellGet hebt geïnstalleerd.

1. Installeer de nieuwste versie van Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Installeer een Azure Storage preview-module die ondersteuning biedt voor SAS voor gebruikers overdracht:

    ```powershell
    Install-Module Az.Storage `
        –Repository PSGallery `
        -RequiredVersion 1.3.1-preview `
        –AllowPrerelease `
        –AllowClobber `
        –Force
    ```

1. Sluit het Power shell-venster en open het opnieuw.

Omdat Power shell de nieuwste AZ. Storage-module laadt, moet u mogelijk expliciet de module 1.3.1-preview laden wanneer u de console start. Als u de preview-module expliciet wilt laden, voert u de opdracht [import-module](/powershell/module/microsoft.powershell.core/import-module) uit:

```powershell
Import-Module Az.Storage -RequiredVersion 1.3.1
```

Zie [Install Azure PowerShell with PowerShellGet](/powershell/azure/install-az-ps)(Engelstalig) voor meer informatie over het installeren van Azure PowerShell.

## <a name="sign-in-to-azure-powershell-with-azure-ad"></a>Meld u aan bij Azure PowerShell met Azure AD

Roep de opdracht [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) aan om u aan te melden met uw Azure ad-account:

```powershell
Connect-AzAccount
```

Zie [Aanmelden met Azure PowerShell](/powershell/azure/authenticate-azureps)voor meer informatie over het aanmelden met Power shell.

## <a name="assign-permissions-with-rbac"></a>Machtigingen toewijzen met RBAC

Als u een gebruikers delegering SA'S wilt maken op basis van Azure PowerShell, moet aan het Azure AD-account dat wordt gebruikt om u aan te melden bij Power shell een rol worden toegewezen die de actie **micro soft. Storage/Storage accounts/blobServices/generateUserDelegationKey** bevat. Met deze machtiging is het mogelijk dat Azure AD-account de *gebruikers delegerings sleutel*aanvraagt. De sleutel voor gebruikers overdracht wordt gebruikt voor het ondertekenen van de SA'S van de gebruikers delegering. De rol voor het opgeven van de actie **micro soft. Storage/Storage accounts/blobServices/generateUserDelegationKey** moet worden toegewezen op het niveau van het opslag account, de resource groep of het abonnement. Voor meer informatie over RBAC-machtigingen voor het maken van een SA'S voor het delegeren van gebruikers, zie de sectie **machtigingen toewijzen met RBAC** in [een gebruikers delegering maken sa's](/rest/api/storageservices/create-user-delegation-sas).

Als u niet gemachtigd bent om RBAC-rollen toe te wijzen aan een Azure AD-beveiligingsprincipal, moet u mogelijk de eigenaar van het account of de beheerder vragen om de benodigde machtigingen toe te wijzen.

In het volgende voor beeld wordt de rol **Storage BLOB data Inzender** toegewezen, inclusief de actie **micro soft. Storage/Storage accounts/blobServices/generateUserDelegationKey** . De rol is bereik op het niveau van het opslag account.

Vergeet niet om de waarden van de tijdelijke aanduidingen tussen punt haken te vervangen door uw eigen waarden:

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

Zie [ingebouwde rollen voor Azure-resources](../../role-based-access-control/built-in-roles.md)voor meer informatie over de ingebouwde rollen die de actie **micro soft. Storage/Storage accounts/blobServices/generateUserDelegationKey** bevatten.

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Azure AD-referenties gebruiken voor het beveiligen van een SAS

Wanneer u een SAS voor gebruikers overdracht met Azure PowerShell maakt, wordt de sleutel voor gebruikers overdracht die wordt gebruikt om de SA'S te ondertekenen, impliciet voor u gemaakt. De begin tijd en verloop tijd die u opgeeft voor de SA'S, worden ook gebruikt als de begin tijd en verloop tijd voor de gebruikers delegerings sleutel. 

Omdat het maximale interval waarover de gebruikers overdracht-sleutel geldig is, zeven dagen na de begin datum is, moet u een verloop tijd opgeven voor de SA'S die binnen zeven dagen na de begin tijd vallen. De SAS is ongeldig nadat de gebruikers delegerings sleutel is verlopen, waardoor een SAS met een verloop tijd van meer dan zeven dagen nog geldig is gedurende 7 dagen.

Als u een gebruikers delegering SAS voor een container of BLOB met Azure PowerShell wilt maken, moet u eerst een nieuw Azure Storage `-UseConnectedAccount` context-object maken, waarbij u de para meter opgeeft. De `-UseConnectedAccount` para meter geeft aan dat met de opdracht het context object wordt gemaakt onder het Azure ad-account waarmee u zich hebt aangemeld.

Vergeet niet om de waarden van de tijdelijke aanduidingen tussen punt haken te vervangen door uw eigen waarden:

```powershell
$ctx = New-AzStorageContext -StorageAccountName <storage-account> -UseConnectedAccount
```

### <a name="create-a-user-delegation-sas-for-a-container"></a>Een SAS voor gebruikers overdracht maken voor een container

Als u een SAS-token voor gebruikers overdracht voor een container wilt retour neren, roept u de opdracht [New-AzStorageContainerSASToken](/powershell/module/az.storage/new-azstoragecontainersastoken) aan en geeft u het object Azure Storage context door dat u eerder hebt gemaakt.

In het volgende voor beeld wordt een SAS-token voor gebruikers overdracht voor een container geretourneerd. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden:

```powershell
New-AzStorageContainerSASToken -Context $ctx `
    -Name <container> `
    -Permission racwdl `
    -ExpiryTime <date-time>
```

Het geretourneerde SAS-token voor gebruikers overdracht is vergelijkbaar met:

```
?sv=2018-11-09&sr=c&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-05T22%3A24%3A36Z&ske=2019-08-07T07%3A
00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=rwdl
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Een SAS voor gebruikers overdracht maken voor een BLOB

Als u een SAS-token voor gebruikers overdracht voor een BLOB wilt retour neren, roept u de opdracht [New-AzStorageBlobSASToken](/powershell/module/az.storage/new-azstorageblobsastoken) aan en geeft u het object Azure Storage context door dat u eerder hebt gemaakt.

De volgende syntaxis retourneert een gebruikers delegering SA'S voor een blob. In het voor beeld `-FullUri` wordt de para meter opgegeven, die de BLOB-URI retourneert waaraan het SAS-token is toegevoegd. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden:

```powershell
New-AzStorageBlobSASToken -Context $ctx `
    -Container <container> `
    -Blob <blob> `
    -Permission racwd `
    -ExpiryTime <date-time>
    -FullUri
```

De geretourneerde SAS-URI voor gebruikers overdracht is vergelijkbaar met:

```
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?sv=2018-11-09&sr=b&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-06T21%3A16%3A54Z&ske=2019-08-07T07%3A00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=racwd
```

> [!NOTE]
> Een door de gebruiker overgedragen SA'S biedt geen ondersteuning voor het definiëren van machtigingen met een opgeslagen toegangs beleid.

## <a name="revoke-a-user-delegation-sas"></a>Een SAS voor gebruikers overdracht intrekken

Als u een gebruikers delegering SA'S van Azure PowerShell wilt intrekken, roept u de opdracht **REVOKE-AzStorageAccountUserDelegationKeys** aan. Met deze opdracht worden alle sleutels voor gebruikers overdracht die zijn gekoppeld aan het opgegeven opslag account ingetrokken. Shared Access signatures die zijn gekoppeld aan deze sleutels, worden ongeldig gemaakt.

Vergeet niet om de waarden van de tijdelijke aanduidingen tussen punt haken te vervangen door uw eigen waarden:

```powershell
Revoke-AzStorageAccountUserDelegationKeys -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
```

## <a name="next-steps"></a>Volgende stappen

- [Een SAS (REST API) voor gebruikers overdracht maken](/rest/api/storageservices/create-user-delegation-sas)
- [Sleutel bewerking voor gebruikers overdracht ophalen](/rest/api/storageservices/get-user-delegation-key)
