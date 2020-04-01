---
title: PowerShell Script - find Vault for Storage Account
description: Meer informatie over het gebruik van een Azure PowerShell-script om de kluis van herstelservices te vinden waar uw opslagaccount is geregistreerd.
ms.topic: sample
ms.date: 1/28/2020
ms.openlocfilehash: 786420ec8cef6516f7261c71b40641693efece07
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76775359"
---
# <a name="powershell-script-to-find-the-recovery-services-vault-where-a-storage-account-is-registered"></a>Powershell Script om de Vault voor Herstelservices te vinden waarin een opslagaccount is geregistreerd

Met dit script u de kluis van herstelservices vinden waar uw opslagaccount is geregistreerd.

## <a name="sample-script"></a>Voorbeeldscript

```powershell
Param(
        [Parameter(Mandatory=$True)][System.String] $ResourceGroupName,
        [Parameter(Mandatory=$True)][System.String] $StorageAccountName,
        [Parameter(Mandatory=$True)][System.String] $SubscriptionId
    )

Connect-AzAccount
Select-AzSubscription -Subscription $SubscriptionId
$vaults = Get-AzRecoveryServicesVault
$found = $false
foreach($vault in $vaults)
{
  Write-Verbose "Checking vault: $($vault.Id)" -Verbose
  
  $containers = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -FriendlyName $StorageAccountName -ResourceGroupName $ResourceGroupName -VaultId $vault.Id -Status Registered
  
  if($containers -ne $null)
  {
    $found = $True
    Write-Information "Found Storage account $StorageAccountName registered in vault: $($vault.Id)" -InformationAction Continue
    break;
  }
}

if(!$found)
{
     Write-Information "Storage account: $StorageAccountName is not registered in any vault of this subscription" -InformationAction Continue
}
```

## <a name="how-to-execute-the-script"></a>Het script uitvoeren

1. Sla het bovenstaande script op uw machine op met een naam naar keuze. In dit voorbeeld hebben we het opgeslagen als *FindRegisteredStorageAccount.ps1*.
2. Voer het script uit door de volgende parameters op te geven:

    * **-ResourceGroupName** - Resourcegroep van het opslagaccount
    * **-StorageAccountName** - Naam van opslagaccount
    * **-SubscriptionID** - De ID van het abonnement waar het opslagaccount aanwezig is.

In het volgende voorbeeld wordt geprobeerd de kluis van herstelservices te vinden waar het *account voor opslag van afsaccount* is geregistreerd:

```powershell
.\FindRegisteredStorageAccount.ps1 -ResourceGroupName AzureFiles -StorageAccountName afsaccount -SubscriptionId ef4ad5a7-c2c0-4304-af80-af49f49af3d1
```

## <a name="output"></a>Uitvoer

De uitvoer geeft het volledige pad weer van de kluis van herstelservices waar het opslagaccount is geregistreerd. Hier volgt een voorbeeld van uitvoer:

```output
Found Storage account afsaccount registered in vault: /subscriptions/ ef4ad5a7-c2c0-4304-af80-af49f49af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault123
```

## <a name="next-steps"></a>Volgende stappen

Informatie over het [maken van back-ups van Azure-bestandsshares vanuit de Azure-portal](https://docs.microsoft.com/azure/backup/backup-afs)
