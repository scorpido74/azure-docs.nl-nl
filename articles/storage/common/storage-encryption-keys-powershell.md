---
title: Door de klant beheerde sleutels configureren voor het Azure Storage versleutelen van Power shell
description: Meer informatie over het gebruik van Power shell voor het configureren van door de klant beheerde sleutels voor Azure Storage versleuteling. Door de klant beheerde sleutels bieden u de mogelijkheid om toegangs beheer te maken, te draaien, uit te scha kelen en in te trekken.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/16/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 1b94f1686c2893c1c69bc846d3a0d29084975b7a
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002183"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-powershell"></a>Door de klant beheerde sleutels configureren voor het Azure Storage versleutelen van Power shell

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

In dit artikel wordt beschreven hoe u een sleutel kluis kunt configureren met door de klant beheerde sleutels met behulp van Power shell.

> [!IMPORTANT]
> Het gebruik van door de klant beheerde sleutels met Azure Storage versleuteling vereist dat er voor de sleutel kluis twee vereiste eigenschappen zijn geconfigureerd, worden **verwijderd** en kunnen **niet worden**verwijderd. Deze eigenschappen zijn standaard ingeschakeld wanneer u een nieuwe sleutel kluis maakt in de Azure Portal. Als u deze eigenschappen echter wilt inschakelen voor een bestaande sleutel kluis, moet u Power shell of Azure CLI gebruiken.
> Alleen RSA-sleutels en sleutel grootte 2048 worden ondersteund.

## <a name="assign-an-identity-to-the-storage-account"></a>Een identiteit toewijzen aan het opslag account

Als u door de klant beheerde sleutels voor uw opslag account wilt inschakelen, moet u eerst een door het systeem toegewezen beheerde identiteit toewijzen aan het opslag account. U gebruikt deze beheerde identiteit om de machtigingen voor het opslag account te verlenen voor toegang tot de sleutel kluis.

Als u een beheerde identiteit wilt toewijzen met behulp van Power shell, roept u [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount). Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden.

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Voor meer informatie over het configureren van door het systeem toegewezen beheerde identiteiten met Power shell raadpleegt u [Managed Identities voor Azure resources configureren op een Azure VM met behulp van Power shell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Een nieuwe sleutel kluis maken

Als u een nieuwe sleutel kluis wilt maken met behulp van Power shell, roept u [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault)aan. De sleutel kluis die u gebruikt voor het opslaan van door de klant beheerde sleutels voor Azure Storage versleuteling moet twee sleutel beveiligings instellingen hebben ingeschakeld, **verwijderen** en **niet wissen**. 

Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden. 

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>Het toegangs beleid voor de sleutel kluis configureren

Configureer vervolgens het toegangs beleid voor de sleutel kluis zodat het opslag account toegang heeft tot het. In deze stap gebruikt u de beheerde identiteit die u eerder aan het opslag account hebt toegewezen.

Om het toegangs beleid voor de sleutel kluis in te stellen, roept u [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden en de variabelen te gebruiken die in de voor gaande voor beelden zijn gedefinieerd.

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>Een nieuwe sleutel maken

Maak vervolgens een nieuwe sleutel in de sleutel kluis. Als u een nieuwe sleutel wilt maken, roept u [add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey)aan. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden en de variabelen te gebruiken die in de voor gaande voor beelden zijn gedefinieerd.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

## <a name="configure-encryption-with-customer-managed-keys"></a>Versleuteling configureren met door de klant beheerde sleutels

Azure Storage versleuteling maakt standaard gebruik van door micro soft beheerde sleutels. In deze stap configureert u uw Azure Storage-account voor het gebruik van door de klant beheerde sleutels en geeft u de sleutel op die u wilt koppelen aan het opslag account.

Roep [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) aan om de versleutelings instellingen van het opslag account bij te werken. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden en de variabelen te gebruiken die in de voor gaande voor beelden zijn gedefinieerd.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

## <a name="update-the-key-version"></a>De sleutel versie bijwerken

Wanneer u een nieuwe versie van een sleutel maakt, moet u het opslag account bijwerken voor gebruik van de nieuwe versie. Roep eerst [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) aan om de meest recente versie van de sleutel op te halen. Roep vervolgens [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) aan om de versleutelings instellingen van het opslag account bij te werken voor het gebruik van de nieuwe versie van de sleutel, zoals wordt weer gegeven in de vorige sectie.

## <a name="next-steps"></a>Volgende stappen

- [Azure Storage versleuteling voor Data-at-rest](storage-service-encryption.md) 
- [Wat is Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
