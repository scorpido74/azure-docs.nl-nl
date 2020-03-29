---
title: Een momentopname van een virtuele harde schijf maken in Azure
description: Meer informatie over het maken van een kopie van een Azure VM om te gebruiken als back-up of voor het oplossen van problemen.
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 10/08/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: bc74a3eea1f99de6080788d6f3fddcac823092dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75370898"
---
# <a name="create-a-snapshot"></a>Een momentopname maken

Een momentopname is een volledige, alleen-lezen kopie van een virtuele harde schijf (VHD). U een momentopname maken van een VHD-besturingssysteem of gegevensschijf om te gebruiken als back-up of om vm-problemen (virtual machine) op te lossen.

Als u de momentopname gaat gebruiken om een nieuwe virtuele machine te maken, raden we u aan de VM netjes af te sluiten voordat u een momentopname maakt, om alle lopende processen op te ruimen.

## <a name="use-the-azure-portal"></a>De Azure-portal gebruiken 

Voer de volgende stappen uit om een momentopname te maken: 
1.  Selecteer een resource **maken**in de [Azure-portal](https://portal.azure.com).
2. Zoeken naar en selecteer **Momentopname**.
3. Selecteer **in** het venster Momentopname de optie **Maken**. Het **venster Momentopname maken** wordt weergegeven.
4. Voer een **naam** voor de momentopname in.
5. Selecteer een bestaande [resourcegroep](../../azure-resource-manager/management/overview.md#resource-groups) of voer de naam van een nieuwe groep in. 
6. Selecteer de **Locatie** van een Azure-datacenter.  
7. Selecteer **voor de bronschijf**de beheerde schijf naar momentopname.
8. Selecteer het **accounttype** dat u wilt gebruiken om de momentopname op te slaan. Selecteer **Standard_HDD**, tenzij u de momentopname moet opslaan op een goed presterende schijf.
9. Selecteer **Maken**.

## <a name="use-powershell"></a>PowerShell gebruiken

In de volgende stappen wordt uitgelegd hoe u de VHD-schijf kopieert en de momentopnameconfiguratie maakt. U dan een momentopname van de schijf maken met de [nieuw-AzSnapshot-cmdlet.](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot) 

 

1. Stel een aantal parameters in: 

   ```azurepowershell-interactive
   $resourceGroupName = 'myResourceGroup' 
   $location = 'eastus' 
   $vmName = 'myVM'
   $snapshotName = 'mySnapshot'  
   ```

2. Download de VM:

   ```azurepowershell-interactive
   $vm = get-azvm `
   -ResourceGroupName $resourceGroupName 
   -Name $vmName
   ```

3. Maak de momentopnameconfiguratie. In dit voorbeeld is de momentopname van de OS-schijf:

   ```azurepowershell-interactive
   $snapshot =  New-AzSnapshotConfig 
   -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id 
   -Location $location 
   -CreateOption copy
   ```
   
   > [!NOTE]
   > Als u uw momentopname wilt opslaan in zonebestendige opslag, maakt u deze in `-SkuName Standard_ZRS` een regio die [beschikbaarheidszones](../../availability-zones/az-overview.md) ondersteunt en de parameter opneemt.   
   
4. Neem de momentopname:

   ```azurepowershell-interactive
   New-AzSnapshot 
   -Snapshot $snapshot 
   -SnapshotName $snapshotName 
   -ResourceGroupName $resourceGroupName 
   ```


## <a name="next-steps"></a>Volgende stappen

Maak een virtuele machine op basis van een momentopname door een beheerde schijf te maken op basis van een momentopname en vervolgens de nieuwe beheerde schijf als de OS-schijf te bevestigen. Zie het voorbeeld in [Een vm maken op basis van een momentopname met PowerShell](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)voor meer informatie.
