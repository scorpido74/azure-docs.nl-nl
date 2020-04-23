---
title: Ongekoppelde Azure-beheerde en onbeheerde schijven zoeken en verwijderen
description: Onbeheerde en onbeheerde (VHD's/paginablobs) schijven zoeken en verwijderen met Azure PowerShell.
author: roygara
ms.service: virtual-machines-windows
ms.topic: how-to
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: cac192186c91259a5573dc27442137729816991a
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869600"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Ongekoppelde Azure-beheerde en onbeheerde schijven zoeken en verwijderen

Wanneer u een virtuele machine (VM) in Azure verwijdert, worden standaard geen schijven verwijderd die aan de vm zijn gekoppeld. Deze functie helpt om gegevensverlies te voorkomen als gevolg van het onbedoeld verwijderen van VM's. Nadat een virtuele machine is verwijderd, blijft u betalen voor niet-gekoppelde schijven. In dit artikel ziet u hoe u niet-gekoppelde schijven vinden en verwijderen en onnodige kosten verlagen.

## <a name="managed-disks-find-and-delete-unattached-disks"></a>Beheerde schijven: niet-gekoppelde schijven zoeken en verwijderen

In het volgende script wordt gezocht naar niet-gekoppelde [beheerde schijven](managed-disks-overview.md) door de waarde van de eigenschap **ManagedBy te** onderzoeken. Wanneer een beheerde schijf is gekoppeld aan een vm, bevat de eigenschap **ManagedBy** de bron-id van de vm. Wanneer een beheerde schijf niet is gekoppeld, is de eigenschap **ManagedBy** null. Het script onderzoekt alle beheerde schijven in een Azure-abonnement. Wanneer het script een beheerde schijf met de eigenschap **ManagedBy** op null lokaliseert, bepaalt het script dat de schijf niet is gekoppeld.

>[!IMPORTANT]
>Voer eerst het script uit door de variabele **deleteUnattachedDisks** in te stellen op 0. Met deze actie u alle niet-gekoppelde beheerde schijven vinden en bekijken.
>
>Nadat u alle niet-gekoppelde schijven hebt bekeken, voert u het script opnieuw uit en stelt u de variabele **deleteUnattachedDisks** in op 1. Met deze actie u alle niet-gekoppelde beheerde schijven verwijderen.

```azurepowershell-interactive
# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
$deleteUnattachedDisks=0
$managedDisks = Get-AzDisk
foreach ($md in $managedDisks) {
    # ManagedBy property stores the Id of the VM to which Managed Disk is attached to
    # If ManagedBy property is $null then it means that the Managed Disk is not attached to a VM
    if($md.ManagedBy -eq $null){
        if($deleteUnattachedDisks -eq 1){
            Write-Host "Deleting unattached Managed Disk with Id: $($md.Id)"
            $md | Remove-AzDisk -Force
            Write-Host "Deleted unattached Managed Disk with Id: $($md.Id) "
        }else{
            $md.Id
        }
    }
 }
```

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Niet-beheerde schijven: niet-gekoppelde schijven zoeken en verwijderen

Onbeheerde schijven zijn VHD-bestanden die zijn opgeslagen als [paginablobs](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) in [Azure-opslagaccounts.](../../storage/common/storage-create-storage-account.md) In het volgende script wordt gezocht naar niet-gekoppelde niet-beheerde schijven (paginablobs) door de waarde van de eigenschap **LeaseStatus** te onderzoeken. Wanneer een niet-beheerde schijf is gekoppeld aan een vm, is de eigenschap **LeaseStatus** ingesteld op **Vergrendeld**. Wanneer een niet-beheerde schijf niet is gekoppeld, is de eigenschap **LeaseStatus** ingesteld op **Ontgrendeld**. Het script onderzoekt alle niet-beheerde schijven in alle Azure-opslagaccounts in een Azure-abonnement. Wanneer het script een onbeheerde schijf met een **eigenschap LeaseStatus** op **Ontgrendeld**vindt, bepaalt het script dat de schijf niet is gekoppeld.

>[!IMPORTANT]
>Voer eerst het script uit door de variabele **deleteUnattachedVHDs** in te stellen op 0. Met deze actie u alle niet-gekoppelde onbeheerde VHD's vinden en bekijken.
>
>Nadat u alle niet-gekoppelde schijven hebt bekeken, voert u het script opnieuw uit en stelt u de variabele **deleteUnattachedVHDs** in op 1. Met deze actie u alle niet-gekoppelde onbeheerde VHD's verwijderen.

```azurepowershell-interactive
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the Uri of the unattached VHDs
$deleteUnattachedVHDs=0
$storageAccounts = Get-AzStorageAccount
foreach($storageAccount in $storageAccounts){
    $storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.StorageAccountName)[0].Value
    $context = New-AzStorageContext -StorageAccountName $storageAccount.StorageAccountName -StorageAccountKey $storageKey
    $containers = Get-AzStorageContainer -Context $context
    foreach($container in $containers){
        $blobs = Get-AzStorageBlob -Container $container.Name -Context $context
        #Fetch all the Page blobs with extension .vhd as only Page blobs can be attached as disk to Azure VMs
        $blobs | Where-Object {$_.BlobType -eq 'PageBlob' -and $_.Name.EndsWith('.vhd')} | ForEach-Object { 
            #If a Page blob is not attached as disk then LeaseStatus will be unlocked
            if($_.ICloudBlob.Properties.LeaseStatus -eq 'Unlocked'){
                    if($deleteUnattachedVHDs -eq 1){
                        Write-Host "Deleting unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"
                        $_ | Remove-AzStorageBlob -Force
                        Write-Host "Deleted unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"
                    }
                    else{
                        $_.ICloudBlob.Uri.AbsoluteUri
                    }
            }
        }
    }
}
```

## <a name="next-steps"></a>Volgende stappen

Zie [Opslagaccount verwijderen](../../storage/common/storage-create-storage-account.md) en [Zwevende schijven identificeren met PowerShell](https://blogs.technet.microsoft.com/ukplatforms/2018/02/21/azure-cost-optimisation-series-identify-orphaned-disks-using-powershell/) voor meer informatie
