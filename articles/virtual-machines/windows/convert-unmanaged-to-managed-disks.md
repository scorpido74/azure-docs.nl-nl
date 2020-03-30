---
title: Een virtuele Windows-machine van niet-beheerde schijven converteren naar beheerde schijven
description: Een Windows-vm converteren van niet-beheerde schijven naar beheerde schijven met PowerShell in het implementatiemodel Resource Manager
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: rogarana
ms.openlocfilehash: 8c180cfc597c0ade27b1fe8cca5a8751176ea12e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75460129"
---
# <a name="convert-a-windows-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Een virtuele Windows-machine van niet-beheerde schijven converteren naar beheerde schijven

Als u bestaande Virtuele Windows-machines (VM's) hebt die onbeheerde schijven gebruiken, u de VM's converteren naar beheerde schijven via de [Azure Managed Disks-service.](managed-disks-overview.md) Hiermee wordt zowel de osschijf als eventuele gekoppelde gegevensschijven geconverteerd.

 

## <a name="before-you-begin"></a>Voordat u begint


* Plan [voor de migratie naar beheerde schijven bekijken](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).

* Bekijk [de veelgestelde vragen over migratie naar beheerde schijven](faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]

* De oorspronkelijke VHD's en het opslagaccount die vóór de conversie werden gebruikt door de VM worden niet verwijderd. Hiervoor worden nog altijd kosten in rekening gebracht. Als u wilt voorkomen dat er kosten worden doorberekend voor de artefacten, verwijdert u de oorspronkelijke VHD-blobs nadat u hebt gecontroleerd of de conversie is voltooid. Als u deze niet-gekoppelde schijven moet vinden om ze te verwijderen, raadpleegt u ons artikel [Ongekoppelde Azure-beheerde en onbeheerde schijven zoeken en verwijderen.](find-unattached-disks.md)


## <a name="convert-single-instance-vms"></a>VM's met één instantie converteren
In deze sectie wordt uitgelegd hoe u Azure VM's met één instantie converteert van niet-beheerde schijven naar beheerde schijven. (Als uw VM's zich in een beschikbaarheidsset bevinden, raadpleegt u de volgende sectie.) 

1. Deallocate de VM met behulp van de [Stop-AzVM-cmdlet.](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) In het volgende voorbeeld wordt `myVM` de VM `myResourceGroup`toegewezen die is vernoemd in de resourcegroep met de naam : 

   ```azurepowershell-interactive
   $rgName = "myResourceGroup"
   $vmName = "myVM"
   Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
   ```

2. Converteer de VM naar beheerde schijven met de cmdlet [ConvertTo-AzVMManagedDisk.](https://docs.microsoft.com/powershell/module/az.compute/convertto-azvmmanageddisk) Met het volgende proces wordt de vorige vm, inclusief de osschijf en eventuele gegevensschijven, geconverteerd en wordt de virtuele machine gestart:

   ```azurepowershell-interactive
   ConvertTo-AzVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
   ```



## <a name="convert-vms-in-an-availability-set"></a>VM's converteren in een beschikbaarheidsset

Als de VM's die u wilt converteren naar beheerde schijven zich in een beschikbaarheidsset bevinden, moet u eerst de beschikbaarheidsset converteren naar een beheerde beschikbaarheidsset.

1. Converteer de beschikbaarheidsset met de cmdlet [Update-AzAvailabilitySet.](https://docs.microsoft.com/powershell/module/az.compute/update-azavailabilityset) In het volgende voorbeeld wordt `myAvailabilitySet` de beschikbaarheidsset bijgewerkt met de naam resourcegroep met de naam: `myResourceGroup`

   ```azurepowershell-interactive
   $rgName = 'myResourceGroup'
   $avSetName = 'myAvailabilitySet'

   $avSet = Get-AzAvailabilitySet -ResourceGroupName $rgName -Name $avSetName
   Update-AzAvailabilitySet -AvailabilitySet $avSet -Sku Aligned 
   ```

   Als het gebied waar uw beschikbaarheidsset zich bevindt slechts 2 beheerde foutdomeinen heeft, maar het aantal onbeheerde foutdomeinen 3 is, wordt in deze opdracht een fout weergegeven die vergelijkbaar is met 'Het aantal opgegeven foutdomeinen 3 moet in het bereik 1 tot en met 2 vallen'. Als u de fout wilt oplossen, `Sku` werkt `Aligned` u het foutdomein bij naar 2 en werkt u het volgende bij:

   ```azurepowershell-interactive
   $avSet.PlatformFaultDomainCount = 2
   Update-AzAvailabilitySet -AvailabilitySet $avSet -Sku Aligned
   ```

2. Detoewijzing en converteer de VM's in de beschikbaarheidsset. In het volgende script wordt elke VM gedereerd met behulp van de [Stop-AzVM-cmdlet,](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) wordt deze geconverteerd met [convertto-AzVMManagedDisk](https://docs.microsoft.com/powershell/module/az.compute/convertto-azvmmanageddisk)en wordt deze automatisch gestart als afgezien van het conversieproces:

   ```azurepowershell-interactive
   $avSet = Get-AzAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

   foreach($vmInfo in $avSet.VirtualMachinesReferences)
   {
     $vm = Get-AzVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}
     Stop-AzVM -ResourceGroupName $rgName -Name $vm.Name -Force
     ConvertTo-AzVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
   }
   ```


## <a name="troubleshooting"></a>Problemen oplossen

Als er een fout optreedt tijdens de conversie of als een vm in een `ConvertTo-AzVMManagedDisk` mislukte staat is als gevolg van problemen in een eerdere conversie, voert u de cmdlet opnieuw uit. Een eenvoudige poging deblokkeert meestal de situatie.
Voordat u converteert, controleert u of alle VM-extensies in de status 'Geslaagd inrichten' staan of dat de conversie mislukt met de foutcode 409.

## <a name="convert-using-the-azure-portal"></a>Converteren met de Azure-portal

U ook onbeheerde schijven converteren naar beheerde schijven met behulp van de Azure-portal.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer de VM in de lijst met VM's in de portal.
3. Selecteer **Schijven** in het menu in het blad voor de virtuele machine.
4. Selecteer Migreren **naar beheerde schijven**boven aan het **hoofdvan schijven** .
5. Als uw vm zich in een beschikbaarheidsset bevindt, wordt er een waarschuwing weergegeven op het blade **Migreren naar beheerde schijven** die u eerst moet converteren naar de beschikbaarheidsset. De waarschuwing moet een koppeling hebben waarop u klikken om de beschikbaarheidsset om te zetten. Zodra de beschikbaarheidsset is geconverteerd of als uw vm zich niet in een beschikbaarheidsset bevindt, klikt u op **Migreren** om het proces van het migreren van uw schijven naar beheerde schijven te starten.

De VM wordt gestopt en opnieuw gestart nadat de migratie is voltooid.

## <a name="next-steps"></a>Volgende stappen

[Standaard beheerde schijven converteren naar premium](convert-disk-storage.md)

Neem een alleen-lezen kopie van een vm met behulp van [snapshots](snapshot-copy-managed-disk.md).

