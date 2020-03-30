---
title: Opslagblobs verwijderen voor netwerkbeveiligingsgroepstroomlogboeken in Azure Network Watcher | Microsoft Documenten
description: In dit artikel wordt uitgelegd hoe u de logboekopslagblobs voor netwerkbeveiligingen die buiten hun bewaarbeleidsperiode vallen, verwijderen in Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2019
ms.author: damendo
ms.openlocfilehash: 6d535bcc2e0831baae658796f76c8087d74c6a85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587206"
---
# <a name="delete-network-security-group-flow-log-storage-blobs-in-network-watcher"></a>Blobs voor netwerkbeveiliginggroepstroomlogboekopslag verwijderen in Network Watcher

Momenteel is er een probleem waarbij [netwerkbeveiligingsgroep (NSG)-stroomlogboeken](network-watcher-nsg-flow-logging-overview.md) voor Network Watcher niet automatisch worden verwijderd uit Blob-opslag op basis van bewaarbeleidsinstellingen. U moet nu een PowerShell-script uitvoeren om de stroomlogboeken handmatig uit uw opslagaccount te verwijderen, zoals beschreven in dit artikel.

## <a name="run-powershell-script-to-delete-nsg-flow-logs"></a>PowerShell-script uitvoeren om NSG-stroomlogboeken te verwijderen
 
Kopieer en sla het volgende script op naar een locatie zoals uw huidige werkmap. 

```powershell
# This powershell script deletes all NSG flow log blobs that should not be retained anymore as per configured retention policy.
# While configuring NSG flow logs on Azure portal, the user configures the retention period of NSG flow log blobs in
# their storage account (in days).
# This script reads all blobs and deletes blobs that are not to be retained (outside retention window)
# if the retention days are zero; all blobs are retained forever and hence no blobs are deleted.
#
#

param (
        [string] [Parameter(Mandatory=$true)]  $SubscriptionId,
        [string] [Parameter(Mandatory=$true)]  $Location,
        [switch] [Parameter(Mandatory=$false)] $Confirm
    )

Login-AzAccount

$SubId = Get-AzSubscription| Where-Object {$_.Id.contains($SubscriptionId.ToLower())}

if ($SubId.Count -eq 0)
{
    Write-Error 'The SubscriptionId does not exist' -ErrorAction Stop
}

Set-AzContext -SubscriptionId $SubscriptionId

$NsgList = Get-AzNetworkSecurityGroup | Where-Object {$_.Location -eq $Location}
$NW = Get-AzNetworkWatcher | Where-Object {$_.Location -eq $Location}

$FlowLogsList = @()
foreach ($Nsg in $NsgList)
{
    # Query Flow Log Status which are enabled
    $NsgFlowLog = Get-AzNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $Nsg.Id | Where-Object {$_.Enabled -eq "True"}
    if ($NsgFlowLog.Count -gt 0)
    {
        $FlowLogsList +=  $NsgFlowLog
        Write-Output ('Enabled NSG found: ' +  $NsgFlowLog.TargetResourceId)
    }
}

foreach ($Psflowlog in $FlowLogsList)
{
    $RetentionDays = $Psflowlog.RetentionPolicy.Days
    if ($RetentionDays -le 0)
    {
        continue
    }

    $Strings = $Psflowlog.StorageId -split '/'
    $RGName = $Strings[4]
    $StorageAccountName = $Strings[-1]

    $Key = (Get-AzStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName).Value[1]
    $StorageAccount = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $Key

    $ContainerName = 'insights-logs-networksecuritygroupflowevent'  
    $BLobsList = Get-AzStorageBlob -Container $ContainerName -Context $StorageAccount.Context

    $TargetBLobsList = $BLobsList | Where-Object {$_.Name.contains($Psflowlog.TargetResourceId.ToUpper())}

    $RetentionDate = Get-Date
    $RetentionDate = $RetentionDate.AddDays(-1*$RetentionDays)
    $RetentionDateInUTC = $RetentionDate.ToUniversalTime()

    foreach ($Blob in $TargetBLobsList)
    {
        $BlobLastModifietedDTinUTC = [datetime]$Blob.LastModified.UtcDateTime

        if ($BlobLastModifietedDTinUTC -ge  $RetentionDateInUTC)
        {
            Write-Output ($Blob.Name + '===>' + $BlobLastModifietedDTinUTC  + ' ===> RETAINED')
            continue
        }

        if ($Confirm)
        {
            Write-Output (Blob to be deleted: $Blob.Name)
            $Confirmation = Read-Host "Are you sure you want to remove this blob (Y/N)?"
        }

        if ((-not $Confirm) -or ($Confirmation -eq 'Y'))
        {
            Write-Output ($Blob.Name + '===>' + $BlobLastModifietedDTinUTC  + ' ===> DELETED')
            Remove-AzStorageBlob -Container $ContainerName -Context $StorageAccount.Context -Blob $Blob.Name
        }
        else
        {
            Write-Output ($Blob.Name + '===>' + $BlobLastModifietedDTinUTC  + ' ===> RETAINED')
        }
    }
}

Write-Output ('Retention policy for all NSGs evaluated and completed successfully')
```

1. Voer destijds de volgende parameters in het script in:
   - **SubscriptionId** [Verplicht]: de abonnements-ID van waaruit u NSG Flow Log blobs wilt verwijderen.
   - **Locatie** [Verplicht]: de _locatiereeks_ van het gebied van de NSG's waarvoor u NSG Flow Log-blobs wilt verwijderen. U deze informatie bekijken op de Azure-portal of op [GitHub.](https://github.com/Azure/azure-extensions-cli/blob/beb3d3fe984cfa9c7798cb11a274c5337968cbc5/regions.go#L23)
   - **Bevestigen** [Optioneel]: Geef de bevestigende vlag door als u de verwijdering van elke opslagblob handmatig wilt bevestigen.

1. Voer het opgeslagen script uit zoals in het volgende voorbeeld, waarbij het scriptbestand is opgeslagen als **Delete-NsgFlowLogsBlobs.ps1:**
   ```
   .\Delete-NsgFlowLogsBlobs.ps1 -SubscriptionId <subscriptionId> -Location  <location> -Confirm
   ```
    
## <a name="next-steps"></a>Volgende stappen
- Klanten kunnen het uitvoeren van het script automatiseren met [Azure Logic Apps](../logic-apps/logic-apps-overview.md) of Azure [Automation](https://azure.microsoft.com/services/automation/)
- Zie [Azure Monitor-logboeken voor netwerkbeveiligingsgroepen (NSG's) voor](../virtual-network/virtual-network-nsg-manage-log.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)meer informatie over NSG-logboekregistratie.

