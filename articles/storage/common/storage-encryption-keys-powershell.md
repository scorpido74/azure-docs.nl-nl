---
title: Power shell gebruiken voor het configureren van door de klant beheerde sleutels
titleSuffix: Azure Storage
description: Meer informatie over het gebruik van Power shell voor het configureren van door de klant beheerde sleutels voor Azure Storage versleuteling.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/13/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: a3fdde755a5e024efead5c8861a1d5cd769b6d23
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87036825"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-powershell"></a>Door de klant beheerde sleutels configureren met Azure Key Vault met behulp van Power shell

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

In dit artikel wordt beschreven hoe u een Azure Key Vault configureert met door de klant beheerde sleutels met behulp van Power shell. Voor informatie over het maken van een sleutel kluis met behulp van Azure CLI, Zie [Quick Start: een geheim instellen en ophalen uit Azure Key Vault met behulp van Power shell](../../key-vault/secrets/quick-create-powershell.md).

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

Als u een nieuwe sleutel kluis wilt maken met behulp van Power shell, installeert u versie 2.0.0 of hoger van de [AZ. key kluis](https://www.powershellgallery.com/packages/Az.KeyVault/2.0.0) Power shell-module. Roep vervolgens [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) aan om een nieuwe sleutel kluis te maken.

De sleutel kluis die u gebruikt voor het opslaan van door de klant beheerde sleutels voor Azure Storage versleuteling moet twee sleutel beveiligings instellingen hebben ingeschakeld, **verwijderen** en **niet wissen**. In versie 2.0.0 en hoger van de module AZ. inkluis, wordt zacht verwijderen standaard ingeschakeld wanneer u een nieuwe sleutel kluis maakt.

In het volgende voor beeld wordt een nieuwe sleutel kluis gemaakt met de functie **zacht verwijderen** en **worden eigenschappen niet verwijderd** ingeschakeld. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden.

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnablePurgeProtection
```

Voor meer informatie over het inschakelen van **zacht verwijderen** en het **verwijderen** van een bestaande sleutel kluis met Power shell raadpleegt u de secties met het **inschakelen van zacht** verwijderen en het **inschakelen van beveiliging opschonen** in [het gebruik van voorlopig verwijderen met Power shell](../../key-vault/general/soft-delete-powershell.md).

## <a name="configure-the-key-vault-access-policy"></a>Het toegangs beleid voor de sleutel kluis configureren

Configureer vervolgens het toegangs beleid voor de sleutel kluis zodat het opslag account toegang heeft tot het. In deze stap gebruikt u de beheerde identiteit die u eerder aan het opslag account hebt toegewezen.

Om het toegangs beleid voor de sleutel kluis in te stellen, roept u [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden en de variabelen te gebruiken die in de voor gaande voor beelden zijn gedefinieerd.

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

## <a name="create-a-new-key"></a>Een nieuwe sleutel maken

Maak vervolgens een nieuwe sleutel in de sleutel kluis. Als u een nieuwe sleutel wilt maken, roept u [add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey)aan. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden en de variabelen te gebruiken die in de voor gaande voor beelden zijn gedefinieerd.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

Azure Storage Encryption ondersteunt RSA-en RSA-HSM-sleutels met een grootte van 2048, 3072 en 4096. Zie **Key Vault sleutels** in [over Azure Key Vault sleutels, geheimen en certificaten](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)voor meer informatie over sleutels.

## <a name="configure-encryption-with-customer-managed-keys"></a>Versleuteling configureren met door de klant beheerde sleutels

Azure Storage versleuteling maakt standaard gebruik van door micro soft beheerde sleutels. In deze stap configureert u uw Azure Storage-account voor het gebruik van door de klant beheerde sleutels met Azure Key Vault en geeft u vervolgens de sleutel op die u wilt koppelen aan het opslag account.

Wanneer u versleuteling met door de klant beheerde sleutels configureert, kunt u ervoor kiezen om automatisch de sleutel te draaien die wordt gebruikt voor versleuteling wanneer de versie wordt gewijzigd in de bijbehorende sleutel kluis. U kunt ook expliciet een sleutel versie opgeven die moet worden gebruikt voor versleuteling totdat de sleutel versie hand matig wordt bijgewerkt.

### <a name="configure-encryption-for-automatic-rotation-of-customer-managed-keys"></a>Versleuteling configureren voor automatische rotatie van door de klant beheerde sleutels

Als u versleuteling wilt configureren voor automatische rotatie van door de klant beheerde sleutels, installeert u de module [AZ. Storage](https://www.powershellgallery.com/packages/Az.Storage) , versie 2.0.0 of hoger.

Als u automatisch door de klant beheerde sleutels wilt draaien, laat u de sleutel versie weg wanneer u door de klant beheerde sleutels voor het opslag account configureert. Roep [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) aan om de versleutelings instellingen van het opslag account bij te werken, zoals wordt weer gegeven in het volgende voor beeld, en neem de optie **-KeyvaultEncryption** op om door de klant beheerde sleutels voor het opslag account in te scha kelen. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden en de variabelen te gebruiken die in de voor gaande voor beelden zijn gedefinieerd.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVaultUri $keyVault.VaultUri
```

### <a name="configure-encryption-for-manual-rotation-of-key-versions"></a>Versleuteling configureren voor hand matige draaiing van sleutel versies

Als u expliciet een sleutel versie wilt opgeven die moet worden gebruikt voor versleuteling, geeft u de sleutel versie op wanneer u versleuteling configureert met door de klant beheerde sleutels voor het opslag account. Roep [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) aan om de versleutelings instellingen van het opslag account bij te werken, zoals wordt weer gegeven in het volgende voor beeld, en neem de optie **-KeyvaultEncryption** op om door de klant beheerde sleutels voor het opslag account in te scha kelen. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden en de variabelen te gebruiken die in de voor gaande voor beelden zijn gedefinieerd.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

Wanneer u de sleutel versie hand matig roteert, moet u de versleutelings instellingen van het opslag account bijwerken om de nieuwe versie te gebruiken. Roep eerst [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) aan om de meest recente versie van de sleutel op te halen. Roep vervolgens [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) aan om de versleutelings instellingen van het opslag account bij te werken voor het gebruik van de nieuwe versie van de sleutel, zoals wordt weer gegeven in het vorige voor beeld.

## <a name="use-a-different-key"></a>Een andere sleutel gebruiken

Als u de sleutel wilt wijzigen die wordt gebruikt voor Azure Storage versleuteling, roept u [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) aan zoals weer gegeven in [versleuteling configureren met door de klant beheerde sleutels](#configure-encryption-with-customer-managed-keys) en geeft u de nieuwe sleutel naam en-versie op. Als de nieuwe sleutel zich in een andere sleutel kluis bevindt, moet u ook de sleutel kluis-URI bijwerken.

## <a name="revoke-customer-managed-keys"></a>Door de klant beheerde sleutels intrekken

U kunt door de klant beheerde sleutels intrekken door het toegangs beleid voor de sleutel kluis te verwijderen. Als u een door de klant beheerde sleutel wilt intrekken, roept u de opdracht [Remove-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy) aan, zoals wordt weer gegeven in het volgende voor beeld. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden en de variabelen te gebruiken die in de voor gaande voor beelden zijn gedefinieerd.

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

## <a name="disable-customer-managed-keys"></a>Door de klant beheerde sleutels uitschakelen

Wanneer u door de klant beheerde sleutels uitschakelt, wordt uw opslag account opnieuw versleuteld met door micro soft beheerde sleutels. Als u door de klant beheerde sleutels wilt uitschakelen, roept u [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) aan met de `-StorageEncryption` optie, zoals wordt weer gegeven in het volgende voor beeld. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden en de variabelen te gebruiken die in de voor gaande voor beelden zijn gedefinieerd.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

## <a name="next-steps"></a>Volgende stappen

- [Azure Storage-versleuteling voor inactieve gegevens](storage-service-encryption.md)
- [Wat is Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
