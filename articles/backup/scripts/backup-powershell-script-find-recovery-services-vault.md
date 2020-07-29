---
title: 'PowerShell-script: kluis voor opslagaccount vinden'
description: Lees hoe u met een Azure PowerShell-script de Recovery Services-kluis kunt vinden waarbij uw opslagaccount is geregistreerd.
ms.topic: sample
ms.date: 1/28/2020
ms.openlocfilehash: b343b2f93ed439188c5c0238bf108064f6e132c1
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86513248"
---
# <a name="powershell-script-to-find-the-recovery-services-vault-where-a-storage-account-is-registered"></a>PowerShell-script voor het vinden van de Recovery Services-kluis waarbij uw opslagaccount is geregistreerd

Dit script helpt om de Recovery Services-kluis te vinden waarbij uw opslagaccount is geregistreerd.

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

1. Sla het bovenstaande script op de computer op met een zelfgekozen naam. In dit voorbeeld hebben we het script opgeslagen als *FindRegisteredStorageAccount.ps1*.
2. Voer het script uit door de volgende parameters op te geven:

    * **-ResourceGroupName**: de resourcegroep van het opslagaccount
    * **-StorageAccountName**: de naam van opslagaccount
    * **-SubscriptionID**: de id van het abonnement waarin het opslagaccount zich bevindt

In het volgende voorbeeld wordt gezocht naar de Recovery Services-kluis waarbij het opslagaccount *afsaccount* is geregistreerd:

```powershell
.\FindRegisteredStorageAccount.ps1 -ResourceGroupName AzureFiles -StorageAccountName afsaccount -SubscriptionId ef4ad5a7-c2c0-4304-af80-af49f49af3d1
```

## <a name="output"></a>Uitvoer

De uitvoer bestaat uit het volledige pad van de Recovery Services-kluis waarbij het opslagaccount is geregistreerd. Hier volgt een voorbeeld van uitvoer:

```output
Found Storage account afsaccount registered in vault: /subscriptions/ ef4ad5a7-c2c0-4304-af80-af49f49af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault123
```

## <a name="next-steps"></a>Volgende stappen

Lees meer over het [maken van back-ups van Azure-bestandsshares via de Azure-portal](../backup-afs.md).
