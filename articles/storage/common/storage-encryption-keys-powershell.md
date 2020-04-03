---
title: PowerShell gebruiken om door de klant beheerde sleutels te configureren
titleSuffix: Azure Storage
description: Meer informatie over het gebruik van PowerShell om door klanten beheerde sleutels voor Azure Storage-versleuteling te configureren.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f24c89a53af5e618d64b78d6001040190c1f339c
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618335"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-powershell"></a>Door de klant beheerde sleutels configureren met Azure Key Vault met PowerShell

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

In dit artikel ziet u hoe u een Azure Key Vault configureert met door de klant beheerde sleutels met PowerShell. Zie [Snelstart: Een geheim instellen en ophalen van Azure Key Vault met PowerShell](../../key-vault/quick-create-powershell.md)voor meer informatie over het maken van een sleutelkluis met Azure CLI.

## <a name="assign-an-identity-to-the-storage-account"></a>Een identiteit toewijzen aan het opslagaccount

Als u door de klant beheerde sleutels voor uw opslagaccount wilt inschakelen, wijst u eerst een door het systeem toegewezen beheerde identiteit toe aan het opslagaccount. U gebruikt deze beheerde identiteit om de opslagaccountmachtigingen te verlenen voor toegang tot de sleutelkluis.

Als u een beheerde identiteit wilt toewijzen met PowerShell, belt u [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount). Vergeet niet om de plaatsaanduidingswaarden tussen haakjes te vervangen door uw eigen waarden.

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Zie [Beheerde identiteiten configureren voor Azure-resources op een Azure VM met PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)voor meer informatie over het configureren van beheerde identiteiten met PowerShell.

## <a name="create-a-new-key-vault"></a>Een nieuwe sleutelkluis maken

Als u een nieuwe sleutelkluis wilt maken met PowerShell, belt u [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). De sleutelkluis die u gebruikt om door de klant beheerde sleutels voor Azure Storage-versleuteling op te slaan, moet twee belangrijke beveiligingsinstellingen hebben ingeschakeld, **Soft Delete** en Do **Not Purge.**

Vergeet niet om de plaatsaanduidingswaarden tussen haakjes te vervangen door uw eigen waarden.

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

Zie de secties getiteld **Soft-delete** en **Inschakelen van verwijdering** [in](../../key-vault/key-vault-soft-delete-powershell.md)een bestaande sleutelkluis met PowerShell voor meer informatie over het inschakelen van **Soft Delete** en **Niet verwijderen** op een bestaande sleutelkluis.

## <a name="configure-the-key-vault-access-policy"></a>Het toegangsbeleid voor belangrijke kluizen configureren

Configureer vervolgens het toegangsbeleid voor de sleutelkluis, zodat het opslagaccount machtigingen heeft om toegang te krijgen. In deze stap gebruikt u de beheerde identiteit die u eerder aan het opslagaccount hebt toegewezen.

Als u het toegangsbeleid voor de sleutelkluis wilt instellen, belt u [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Vergeet niet om de plaatsaanduidingswaarden tussen haakjes te vervangen door uw eigen waarden en de variabelen te gebruiken die in de vorige voorbeelden zijn gedefinieerd.

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

## <a name="create-a-new-key"></a>Een nieuwe sleutel maken

Maak vervolgens een nieuwe sleutel in de sleutelkluis. Als u een nieuwe sleutel wilt maken, belt u [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Vergeet niet om de plaatsaanduidingswaarden tussen haakjes te vervangen door uw eigen waarden en de variabelen te gebruiken die in de vorige voorbeelden zijn gedefinieerd.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

Alleen 2048-bits RSA- en RSA-HSM-sleutels worden ondersteund met Azure Storage-versleuteling. Zie **Key Vault-sleutels** in [Over Azure Key Vault-sleutels, -geheimen en -certificaten](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)voor meer informatie over sleutels.

## <a name="configure-encryption-with-customer-managed-keys"></a>Versleuteling configureren met door de klant beheerde sleutels

Azure Storage-versleuteling maakt standaard gebruik van door Microsoft beheerde sleutels. Configureer in deze stap uw Azure Storage-account om door de klant beheerde sleutels te gebruiken en geef de sleutel op die moet worden gekoppeld aan het opslagaccount.

Bel [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) om de versleutelingsinstellingen van het opslagaccount bij te werken, zoals in het volgende voorbeeld wordt weergegeven. Voeg de optie **-KeyvaultEncryption toe** om door de klant beheerde sleutels voor het opslagaccount in te schakelen. Vergeet niet om de plaatsaanduidingswaarden tussen haakjes te vervangen door uw eigen waarden en de variabelen te gebruiken die in de vorige voorbeelden zijn gedefinieerd.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

## <a name="update-the-key-version"></a>De belangrijkste versie bijwerken

Wanneer u een nieuwe versie van een sleutel maakt, moet u het opslagaccount bijwerken om de nieuwe versie te gebruiken. Bel eerst [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) om de nieuwste versie van de sleutel te krijgen. Bel vervolgens [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) om de versleutelingsinstellingen van het opslagaccount bij te werken om de nieuwe versie van de sleutel te gebruiken, zoals in de vorige sectie wordt weergegeven.

## <a name="use-a-different-key"></a>Een andere sleutel gebruiken

Als u de sleutel wilt wijzigen die wordt gebruikt voor Azure Storage-versleuteling, belt u [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) zoals weergegeven in [Versleuteling configureren met door de klant beheerde sleutels](#configure-encryption-with-customer-managed-keys) en geeft u de nieuwe sleutelnaam en -versie op. Als de nieuwe sleutel zich in een andere sleutelkluis bevindt, werkt u ook de sleutelkluis URI bij.

## <a name="revoke-customer-managed-keys"></a>Door de klant beheerde sleutels intrekken

Als u van mening bent dat een sleutel mogelijk is gecompromitteerd, u door de klant beheerde sleutels intrekken door het toegangsbeleid voor belangrijke kluizen te verwijderen. Als u een door de klant beheerde sleutel wilt intrekken, belt u de opdracht [Remove-AzKeyVaultAccessPolicy,](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy) zoals in het volgende voorbeeld wordt weergegeven. Vergeet niet om de plaatsaanduidingswaarden tussen haakjes te vervangen door uw eigen waarden en de variabelen te gebruiken die in de vorige voorbeelden zijn gedefinieerd.

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

## <a name="disable-customer-managed-keys"></a>Door de klant beheerde sleutels uitschakelen

Wanneer u door klanten beheerde sleutels uitschakelt, wordt uw opslagaccount opnieuw versleuteld met door Microsoft beheerde sleutels. Als u door de klant beheerde sleutels `-StorageEncryption` wilt uitschakelen, belt u [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) met de optie, zoals in het volgende voorbeeld wordt weergegeven. Vergeet niet om de plaatsaanduidingswaarden tussen haakjes te vervangen door uw eigen waarden en de variabelen te gebruiken die in de vorige voorbeelden zijn gedefinieerd.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

## <a name="next-steps"></a>Volgende stappen

- [Azure Storage-versleuteling voor gegevens in rust](storage-service-encryption.md)
- [Wat is Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
