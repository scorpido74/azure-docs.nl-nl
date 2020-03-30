---
title: PowerShell gebruiken om een SAS voor gebruikersdelegatie te maken voor een container of blob
titleSuffix: Azure Storage
description: Meer informatie over het maken van een SAS voor gebruikersoverdrachten met Azure Active Directory-referenties met PowerShell.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 5250a27e6c5fcf012207f1edb95ad46c0aabfe63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536170"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-powershell"></a>Een SAS voor gebruikersdelegatie maken voor een container of blob met PowerShell

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

In dit artikel ziet u hoe u Azure Active Directory-referenties (Azure AD) gebruikt om een SAS voor gebruikersdelegatie sas te maken voor een container of blob met Azure PowerShell.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-powershell-module"></a>De PowerShell-module installeren

Als u een SAS voor gebruikersoverdracht wilt maken met PowerShell, installeert u versie 1.10.0 of hoger van de Az.Storage-module. Volg de volgende stappen om de nieuwste versie van de module te installeren:

1. Verwijder eerdere installaties van Azure PowerShell:

    - Verwijder eerdere installaties van Azure PowerShell uit Windows met behulp van de instelling **Apps & functies** onder **Instellingen**.
    - Verwijder alle **Azure-modules** uit `%Program Files%\WindowsPowerShell\Modules`.

1. Zorg ervoor dat u de nieuwste versie van PowerShellGet hebt geïnstalleerd. Open een Windows PowerShell-venster en voer de volgende opdracht uit om de nieuwste versie te installeren:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. Sluit het PowerShell-venster en open het opnieuw na het installeren van PowerShellGet.

1. Installeer de nieuwste versie van Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Zorg ervoor dat u Azure PowerShell-versie 3.2.0 of hoger hebt geïnstalleerd. Voer de volgende opdracht uit om de nieuwste versie van de Azure Storage PowerShell-module te installeren:

    ```powershell
    Install-Module -Name Az.Storage -Repository PSGallery -Force
    ```

1. Sluit het PowerShell-venster en open deze opnieuw.

Voer de volgende opdracht uit om te controleren welke versie van de Az.Storage-module is geïnstalleerd:

```powershell
Get-Module -ListAvailable -Name Az.Storage -Refresh
```

Zie [Azure PowerShell installeren met PowerShellGet](/powershell/azure/install-az-ps)voor meer informatie over het installeren van Azure PowerShell.

## <a name="sign-in-to-azure-powershell-with-azure-ad"></a>Aanmelden bij Azure PowerShell met Azure AD

Bel de opdracht [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) om u aan te melden met uw Azure AD-account:

```powershell
Connect-AzAccount
```

Zie [Aanmelden bij Azure PowerShell](/powershell/azure/authenticate-azureps)voor meer informatie over aanmelden bij PowerShell.

## <a name="assign-permissions-with-rbac"></a>Machtigingen toewijzen met RBAC

Als u een SAS voor gebruikersoverdracht wilt maken vanuit Azure PowerShell, moet het Azure AD-account dat wordt gebruikt om u aan te melden bij PowerShell, een rol worden toegewezen die de actie **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** bevat. Met deze machtiging kan dat Azure AD-account de *sleutel voor gebruikersoverdracht*aanvragen. De gebruikersdelegatiesleutel wordt gebruikt om de gebruikersdelegatie SAS te ondertekenen. De rol die de actie **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** biedt, moet worden toegewezen op het niveau van het opslagaccount, de brongroep of het abonnement. Zie de sectie **Machtigingen toewijzen met RBAC** in SAS voor meer informatie over [RBAC-machtigingen](/rest/api/storageservices/create-user-delegation-sas)voor het maken van een SAS voor gebruikersdelegatie.

Als u niet over voldoende machtigingen beschikt om RBAC-rollen toe te wijzen aan een Azure AD-beveiligingsprincipal, moet u mogelijk de eigenaar of beheerder van het account vragen om de benodigde machtigingen toe te wijzen.

In het volgende voorbeeld wordt de rol **opslagblobgegevensbijdrager toetekent,** waaronder de actie **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey.** De rol wordt verdeeld op het niveau van het opslagaccount.

Vergeet niet om plaatsaanduidingswaarden in hoekhaakjes te vervangen door uw eigen waarden:

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

Zie [Ingebouwde rollen voor Azure-resources voor](../../role-based-access-control/built-in-roles.md)meer informatie over de ingebouwde rollen die de actie **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** bevatten.

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Azure AD-referenties gebruiken om een SAS te beveiligen

Wanneer u een SAS voor gebruikersoverdracht en Azure PowerShell maakt, wordt de gebruikersdelegatiesleutel die wordt gebruikt om de SAS te ondertekenen impliciet voor u gemaakt. De begin- en vervaldatum die u voor de SAS opgeeft, worden ook gebruikt als begin- en vervaldatum voor de gebruikersdelegatiesleutel. 

Omdat het maximale interval waarop de gebruikersdelegatiesleutel geldig is, 7 dagen na de begindatum is, moet u een vervaldatum opgeven voor de SAS die binnen 7 dagen na de begintijd is. De SAS is ongeldig nadat de sleutel voor de delegatie van de gebruiker is verlopen, dus een SAS met een vervaldatum van meer dan 7 dagen is nog steeds slechts 7 dagen geldig.

Als u een SAS voor gebruikersdelegatie wilt maken voor een container of blob met `-UseConnectedAccount` Azure PowerShell, maakt u eerst een nieuw contextobject azure storage, waarin de parameter wordt opgegeven. De `-UseConnectedAccount` parameter geeft aan dat de opdracht het contextobject maakt onder het Azure AD-account waarmee u zich hebt aangemeld.

Vergeet niet om plaatsaanduidingswaarden in hoekhaakjes te vervangen door uw eigen waarden:

```powershell
$ctx = New-AzStorageContext -StorageAccountName <storage-account> -UseConnectedAccount
```

### <a name="create-a-user-delegation-sas-for-a-container"></a>Een gebruikersdelegatie SAS voor een container maken

Als u een SAS-token voor gebruikersdelegatie voor een container wilt retourneren, roept u de opdracht [Nieuw-AzStorageContainerSASToken](/powershell/module/az.storage/new-azstoragecontainersastoken) aan en geeft u het contextobject Azure Storage door dat u eerder hebt gemaakt.

In het volgende voorbeeld wordt een SAS-token voor gebruikersdelegatie voor een container geretourneerd. Vergeet niet om de plaatsaanduidingswaarden tussen haakjes te vervangen door uw eigen waarden:

```powershell
New-AzStorageContainerSASToken -Context $ctx `
    -Name <container> `
    -Permission racwdl `
    -ExpiryTime <date-time>
```

Het geretourneerde SAS-token voor gebruikersdelegeren is vergelijkbaar met:

```output
?sv=2018-11-09&sr=c&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-05T22%3A24%3A36Z&ske=2019-08-07T07%3A
00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=rwdl
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Een SAS voor gebruikersdelegatie maken voor een blob

Als u een SAS-token voor gebruikersdelegatie wilt retourneren voor een blob, roept u de opdracht [Nieuw-AzStorageBlobSASToken](/powershell/module/az.storage/new-azstorageblobsastoken) aan en geeft u het contextobject Azure Storage door dat u eerder hebt gemaakt.

Met de volgende syntaxis retourneert een gebruikersdelegatie SAS voor een blob. In het `-FullUri` voorbeeld wordt de parameter opgegeven, waardoor de blob URI wordt geretourneerd met het SAS-token toegevoegd. Vergeet niet om de plaatsaanduidingswaarden tussen haakjes te vervangen door uw eigen waarden:

```powershell
New-AzStorageBlobSASToken -Context $ctx `
    -Container <container> `
    -Blob <blob> `
    -Permission racwd `
    -ExpiryTime <date-time>
    -FullUri
```

De teruggekeerde gebruikersdelegatie SAS URI is vergelijkbaar met:

```output
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?sv=2018-11-09&sr=b&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-06T21%3A16%3A54Z&ske=2019-08-07T07%3A00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=racwd
```

> [!NOTE]
> Een gebruikersdelegatie SAS ondersteunt geen definiërende machtigingen met een opgeslagen toegangsbeleid.

## <a name="revoke-a-user-delegation-sas"></a>Een gebruikersdelegatie SAS intrekken

Als u een SAS voor gebruikersdeleger wilt intrekken vanuit Azure PowerShell, roept u de opdracht **Intrekken-AzStorageAccountUserDelegationKeys** in. Met deze opdracht worden alle gebruikersdelegatiesleutels ingetrokken die zijn gekoppeld aan het opgegeven opslagaccount. Alle gedeelde toegangshandtekeningen die aan deze sleutels zijn gekoppeld, worden ongeldig verklaard.

Vergeet niet om plaatsaanduidingswaarden in hoekhaakjes te vervangen door uw eigen waarden:

```powershell
Revoke-AzStorageAccountUserDelegationKeys -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
```

> [!IMPORTANT]
> Zowel de gebruikersdelegatiesleutel als de RBAC-roltoewijzingen worden in de cache opgeslagen door Azure Storage, dus er kan een vertraging optreden tussen het moment waarop u het intrekkingsproces initieert en wanneer een bestaande gebruikersdelegatie SAS ongeldig wordt.

## <a name="next-steps"></a>Volgende stappen

- [Een SAS voor gebruikersdelegatie (REST API) maken](/rest/api/storageservices/create-user-delegation-sas)
- [De sleutelbewerking voor gebruikersdelegatie uitvoeren](/rest/api/storageservices/get-user-delegation-key)
