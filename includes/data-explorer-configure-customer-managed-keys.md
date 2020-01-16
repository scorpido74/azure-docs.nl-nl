---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 01/07/2020
ms.author: orspodek
ms.openlocfilehash: 5443ee6912c30b89cee6fdb43f84f3bc1fbcfe68
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76020889"
---
Azure Data Explorer versleutelt alle gegevens in een opslag account in rust. Standaard worden gegevens versleuteld met door micro soft beheerde sleutels. Voor extra controle over versleutelings sleutels kunt u door de klant beheerde sleutels leveren voor het gebruik van gegevens versleuteling. Door de klant beheerde sleutels moeten worden opgeslagen in een [Azure Key Vault](/azure/key-vault/key-vault-overview). U kunt uw eigen sleutels maken en deze opslaan in een sleutel kluis, maar u kunt ook een Azure Key Vault-API gebruiken om sleutels te genereren. Het Azure Data Explorer-cluster en de sleutel kluis moeten zich in dezelfde regio bevinden, maar ze kunnen zich in verschillende abonnementen bevinden. Voor een gedetailleerde uitleg over door de klant beheerde sleutels, Zie [door de klant beheerde sleutels met Azure Key Vault](/azure/storage/common/storage-service-encryption). Dit artikel laat u zien hoe u door de klant beheerde sleutels kunt configureren.

> [!Note]
> Als u door de klant beheerde sleutels wilt configureren met Azure Data Explorer, moet u [twee eigenschappen instellen op de sleutel kluis](/azure/key-vault/key-vault-ovw-soft-delete): **voorlopig verwijderen** en **niet wissen**. Deze eigenschappen zijn niet standaard ingeschakeld. Als u deze eigenschappen wilt inschakelen, gebruikt u [Power shell](/azure/key-vault/key-vault-soft-delete-powershell) of [Azure cli](/azure/key-vault/key-vault-soft-delete-cli). Alleen RSA-sleutels en sleutel grootte 2048 worden ondersteund.

## <a name="assign-an-identity-to-the-cluster"></a>Een identiteit aan het cluster toewijzen

Als u door de klant beheerde sleutels voor uw cluster wilt inschakelen, wijst u eerst een door het systeem toegewezen beheerde identiteit toe aan het cluster. U gebruikt deze beheerde identiteit om de cluster machtigingen te verlenen voor toegang tot de sleutel kluis. Zie [beheerde identiteiten](/azure/data-explorer/managed-identities)om door het systeem toegewezen beheerde identiteiten te configureren.

## <a name="create-a-new-key-vault"></a>Een nieuwe sleutel kluis maken

Als u een nieuwe sleutel kluis wilt maken met behulp van Power shell, roept u [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault)aan. De sleutel kluis die u gebruikt voor het opslaan van door de klant beheerde sleutels voor Azure Data Explorer versleuteling, moet twee instellingen voor sleutel beveiliging hebben ingeschakeld, **verwijderen** en **niet wissen**. Vervang de waarden van de tijdelijke aanduidingen tussen vier Kante haken door uw eigen waarden in het onderstaande voor beeld.

```azurepowershell-interactive
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>Het toegangs beleid voor de sleutel kluis configureren

Configureer vervolgens het toegangs beleid voor de sleutel kluis zodat het cluster machtigingen heeft voor toegang. In deze stap gebruikt u de door het systeem toegewezen beheerde identiteit die u eerder aan het cluster hebt toegewezen. Om het toegangs beleid voor de sleutel kluis in te stellen, roept u [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Vervang de waarden van de tijdelijke aanduiding tussen vier Kante haken door uw eigen waarden en gebruik de variabelen die zijn gedefinieerd in de voor gaande voor beelden.

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $cluster.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>Een nieuwe sleutel maken

Maak vervolgens een nieuwe sleutel in de sleutel kluis. Als u een nieuwe sleutel wilt maken, roept u [add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey)aan. Vervang de waarden van de tijdelijke aanduiding tussen vier Kante haken door uw eigen waarden en gebruik de variabelen die zijn gedefinieerd in de voor gaande voor beelden.

```azurepowershell-interactive
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```
