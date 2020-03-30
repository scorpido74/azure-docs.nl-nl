---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 03/25/2020
ms.author: orspodek
ms.openlocfilehash: 081ba777f6ab19be774f127383e359ff761e7f0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297896"
---
## <a name="create-a-new-key-vault"></a>Een nieuwe sleutelkluis maken

Als u een nieuwe sleutelkluis wilt maken met PowerShell, belt u [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). De sleutelkluis die u gebruikt om door de klant beheerde sleutels op te slaan voor Azure Data Explorer-versleuteling, moet twee belangrijke beveiligingsinstellingen hebben ingeschakeld, **Soft Delete** en Do **Not Purge.** Vervang de tijdelijke aanduidingswaarden tussen haakjes met uw eigen waarden in onderstaande voorbeeld.

```azurepowershell-interactive
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>Het toegangsbeleid voor belangrijke kluizen configureren

Configureer vervolgens het toegangsbeleid voor de sleutelkluis, zodat het cluster machtigingen heeft om toegang te krijgen. In deze stap gebruikt u de door het systeem toegewezen beheerde identiteit die u eerder aan het cluster hebt toegewezen. Als u het toegangsbeleid voor de sleutelkluis wilt instellen, belt u [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Vervang de tijdelijke aanduidingswaarden tussen haakjes door uw eigen waarden en gebruik de variabelen die in de vorige voorbeelden zijn gedefinieerd.

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $cluster.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>Een nieuwe sleutel maken

Maak vervolgens een nieuwe sleutel in de sleutelkluis. Als u een nieuwe sleutel wilt maken, belt u [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Vervang de tijdelijke aanduidingswaarden tussen haakjes door uw eigen waarden en gebruik de variabelen die in de vorige voorbeelden zijn gedefinieerd.

```azurepowershell-interactive
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```
