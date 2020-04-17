---
title: Algemene PowerShell-opdrachten voor virtuele Azure-machines
description: Algemene PowerShell-opdrachten om u aan de slag te helpen bij het maken en beheren van uw Windows VM's in Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ba3839a2-f3d5-4e19-a5de-95bfb1c0e61e
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: 3d08693b6b07b5a2f1fb40d0c4758ab43729bd76
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81456359"
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Algemene PowerShell-opdrachten voor het maken en beheren van virtuele Azure-machines

In dit artikel vindt u een aantal van de Azure PowerShell-opdrachten die u gebruiken om virtuele machines in uw Azure-abonnement te maken en te beheren.  Voor meer gedetailleerde hulp bij specifieke opdrachtregelswitches en -opties u de *opdracht* **Hulp** krijgen gebruiken.

 

Deze variabelen kunnen nuttig zijn voor u als u meer dan één van de opdrachten in dit artikel uitvoert:

- $location - De locatie van de virtuele machine. U [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) gebruiken om een [geografische regio](https://azure.microsoft.com/regions/) te vinden die voor u werkt.
- $myResourceGroup - De naam van de resourcegroep die de virtuele machine bevat.
- $myVM - De naam van de virtuele machine.

## <a name="create-a-vm---simplified"></a>Een VM maken - vereenvoudigd

| Taak | Opdracht |
| ---- | ------- |
| Een eenvoudige vm maken | [Nieuw-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) -Naam $myVM <BR></BR><BR></BR> Nieuw-AzVM heeft een set *vereenvoudigde* parameters, waarbij alleen een enkele naam nodig is. De waarde voor -Naam wordt gebruikt als de naam voor alle resources die nodig zijn voor het maken van een nieuwe virtuele machine. U meer opgeven, maar dit is alles wat nodig is.|
| Een VM maken van een aangepaste installatiekopie | Nieuw-AzVm -ResourceGroupName $myResourceGroup -Naam $myVM ImageName "myImage" -Locatie $location  <BR></BR><BR></BR>U moet al uw eigen [beheerde afbeelding](capture-image-resource.md)hebben gemaakt. U een afbeelding gebruiken om meerdere, identieke VM's te maken. |



## <a name="create-a-vm-configuration"></a>Een VM-configuratie maken

| Taak | Opdracht |
| ---- | ------- |
| Een VM-configuratie maken |$vm = [Nieuw-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) -VMName $myVM -VMSize "Standard_D1_v1"<BR></BR><BR></BR>De VM-configuratie wordt gebruikt om instellingen voor de VM te definiëren of bij te werken. De configuratie wordt geïnitialiseerd met de naam van de VM en de [grootte](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)ervan . |
| Configuratie-instellingen toevoegen |$vm = [Set-AzVMOperatingSystem](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem) -VM $vm -Windows -ComputerName $myVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate<BR></BR><BR></BR>Besturingssysteeminstellingen inclusief [referenties](https://technet.microsoft.com/library/hh849815.aspx) worden toegevoegd aan het configuratieobject dat u eerder hebt gemaakt met Nieuw-AzVMConfig. |
| Een netwerkinterface toevoegen |$vm = [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVMNetworkInterface) -VM $vm -Id $nic. Id<BR></BR><BR></BR>Een VM moet een [netwerkinterface](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) hebben om te communiceren in een virtueel netwerk. U [Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface) ook gebruiken om een bestaand netwerkinterfaceobject op te halen. |
| Een platformafbeelding opgeven |$vm = [Set-AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage) -VM $vm -PublisherName "publisher_name" -Aanbieding "publisher_offer" -Skus "product_sku" -Versie "laatste"<BR></BR><BR></BR>[Afbeeldingsinformatie](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) wordt toegevoegd aan het configuratieobject dat u eerder hebt gemaakt met Nieuw-AzVMConfig. Het object dat van deze opdracht wordt geretourneerd, wordt alleen gebruikt wanneer u de osschijf instelt op een platformafbeelding. |
| Een virtuele machine maken |[Nieuw-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) -ResourceGroupName $myResourceGroup -Locatie $location -VM $vm<BR></BR><BR></BR>Alle resources worden gemaakt in een [resourcegroep](../../azure-resource-manager/management/manage-resource-groups-powershell.md). Voer voordat u deze opdracht uitvoert, New-AzVMConfig, Set-AzVMOperatingSystem, Set-AzVMSourceImage, Add-AzVMNetworkInterface en Set-AzVMOSDisk uit. |
| Een VM bijwerken |[Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) -ResourceGroupName $myResourceGroup -VM $vm<BR></BR><BR></BR>Download de huidige VM-configuratie met Get-AzVM, wijzig de configuratie-instellingen op het VM-object en voer deze opdracht uit. |

## <a name="get-information-about-vms"></a>Informatie over VM's

| Taak | Opdracht |
| ---- | ------- |
| VM's in een abonnement weergeven |[Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) |
| VM's in een resourcegroep weergeven |Get-AzVM -ResourceGroupName $myResourceGroup<BR></BR><BR></BR>Als u een lijst met brongroepen in uw abonnement wilt krijgen, gebruikt u [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup). |
| Informatie over een VM ophalen |Get-AzVM -ResourceGroupName $myResourceGroup -Naam $myVM |

## <a name="manage-vms"></a>Virtuele machines beheren
| Taak | Opdracht |
| --- | --- |
| Een VM starten |[Start-AzVM](https://docs.microsoft.com/powershell/module/az.compute/start-azvm) -ResourceGroupName $myResourceGroup -Naam $myVM |
| Een VM stoppen |[Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) -ResourceGroupName $myResourceGroup -Naam $myVM |
| Een lopende VM opnieuw starten |[Start-AzVM](https://docs.microsoft.com/powershell/module/az.compute/restart-azvm) -ResourceGroupName $myResourceGroup -Naam $myVM |
| Een VM verwijderen |[Remove-AzVM](https://docs.microsoft.com/powershell/module/az.compute/remove-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |


## <a name="next-steps"></a>Volgende stappen
* Bekijk de basisstappen voor het maken van een virtuele machine in [Een Windows-vm maken met Resource Manager en PowerShell.](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

