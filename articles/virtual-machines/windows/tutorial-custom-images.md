---
title: Zelfstudie - Aangepaste VM-installatiekopieën maken met Azure PowerShell | Microsoft Docs
description: In deze zelfstudie leert u hoe u Azure PowerShell gebruikt om een aangepaste installatiekopie van een virtuele machine te maken in Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 1c216e5a572a36d2306326dd0dd6e1b7ed586de8
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350846"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-azure-powershell"></a>Zelfstudie: Een aangepaste installatiekopie van een Azure-VM maken met Azure PowerShell

Aangepaste installatiekopieën zijn soortgelijk aan Marketplace-installatiekopieën, maar u kunt deze zelf maken. Aangepaste installatiekopieën kunnen worden gebruikt als een bootstrap voor implementaties en zorgen voor consistentie tussen verschillende virtuele machines. In deze zelfstudie maakt u uw eigen aangepaste installatiekopie van een virtuele Azure-machine met behulp van PowerShell. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Systeem voorbereiden en virtuele machines generaliseren
> * Een aangepaste installatiekopie maken
> * Een VM maken van een aangepaste installatiekopie
> * Alle installatiekopieën in uw abonnement weergeven
> * Een aangepaste installatiekopie verwijderen

In de open bare preview hebben we de [Azure VM Image Builder](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-overview) -service nodig. Geef een beschrijving van uw aanpassingen in een sjabloon en de stappen voor het maken van de installatie kopie worden verwerkt in dit artikel. [Probeer Azure Image Builder (preview)](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder).

## <a name="before-you-begin"></a>Voordat u begint

In de onderstaande stappen wordt gedetailleerd beschreven hoe u van een bestaande virtuele machine een herbruikbare aangepaste installatiekopie maakt die u kunt gebruiken om nieuwe virtuele machines te maken.

Om het voorbeeld in deze zelfstudie uit te voeren, moet u een bestaande virtuele machine hebben. Indien nodig kan dit [voorbeeldscript](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) er een voor u maken. Vervang tijdens het volgen van de zelfstudie de namen van de resourcegroep en de virtuele machine waar nodig.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. 

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. U kunt Cloud Shell ook openen in een afzonderlijk browsertabblad door naar [https://shell.azure.com/powershell](https://shell.azure.com/powershell) te gaan. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op Enter om de code uit te voeren.

## <a name="prepare-vm"></a>VM voorbereiden

Voor het maken van een installatiekopie van een virtuele machine moet u de bron-VM voorbereiden door deze te generaliseren, de toewijzing ervan ongedaan te maken en de bron-VM vervolgens met Azure als gegeneraliseerd te markeren.

### <a name="generalize-the-windows-vm-using-sysprep"></a>De Windows VM generaliseren met behulp van Sysprep

Sysprep verwijdert onder meer al uw persoonlijke accountinformatie en de machine wordt voorbereid om als een installatiekopie te worden gebruikt. Zie voor meer informatie over [Sysprep gebruiken: een inleiding](https://technet.microsoft.com/library/bb457073.aspx).


1. Maak verbinding met de virtuele machine.
2. Open het venster met de opdrachtprompt als beheerder. Wijzig de directory in *%windir%\system32\sysprep* en voer dan `sysprep.exe`run uit.
3. In het dialoogvenster **Hulpprogramma voor systeemvoorbereiding** selecteert u **OOBE (Out-of-Box Experience) van systeem starten** en zorgt u dat het selectievakje **Generaliseren** is ingeschakeld.
4. In **Opties voor afsluiten** selecteert u **Afsluiten** en klikt u vervolgens op **OK**.
5. Wanneer Sysprep is voltooid, wordt de virtuele machine afgesloten. **Start de VM niet opnieuw**.

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>De toewijzing van de virtuele machine ongedaan maken en de virtuele machine markeren als gegeneraliseerd

Voor het maken van een installatiekopie moet de toewijzing van de virtuele machine ongedaan worden gemaakt en moet de VM worden gemarkeerd als gegeneraliseerd in Azure.

Hef de toewijzing van de virtuele machine op met behulp van [AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm).

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName myResourceGroup `
   -Name myVM -Force
```

Stel de status van de virtuele machine in op `-Generalized` met [Set-AzVm](https://docs.microsoft.com/powershell/module/az.compute/set-azvm). 
   
```azurepowershell-interactive
Set-AzVM `
   -ResourceGroupName myResourceGroup `
   -Name myVM -Generalized
```


## <a name="create-the-image"></a>De installatiekopie maken

Maak nu een installatiekopie van de VM met [New-AzImageConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azimageconfig) en [New-AzImage](https://docs.microsoft.com/powershell/module/az.compute/new-azimage). In het volgende voorbeeld wordt een installatiekopie met de naam *myImage* gemaakt van een virtuele machine met de naam *myVM*.

Haal de virtuele machine op. 

```azurepowershell-interactive
$vm = Get-AzVM `
   -Name myVM `
   -ResourceGroupName myResourceGroup
```

Maak de configuratie van installatiekopie.

```azurepowershell-interactive
$image = New-AzImageConfig `
   -Location EastUS `
   -SourceVirtualMachineId $vm.ID 
```

Maak de installatiekopie.

```azurepowershell-interactive
New-AzImage `
   -Image $image `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
``` 

 
## <a name="create-vms-from-the-image"></a>VM's maken van de installatiekopie

Nu u een installatiekopie hebt gemaakt, kunt u een of meer nieuwe VM's van de installatiekopie maken met behulp. Het maken van een VM op basis van een aangepaste installatiekopie is vergelijkbaar met het maken van een VM met behulp van een Marketplace-installatiekopie. Wanneer u een Marketplace-installatiekopie gebruikt, hebt u de informatie over de installatiekopie, de leverancier van de installatiekopie, de aanbieding, de SKU en de versie. Met de vereenvoudigde parameterset voor de cmdlet [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) hoeft u alleen de naam op te geven van de aangepaste installatiekopie, zolang deze zich in dezelfde resourcegroep bevindt. 

In dit voorbeeld wordt een VM met de naam *myVMfromImage* gemaakt van een installatiekopie met de naam *myImage* in *myResourceGroup*.


```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVMfromImage" `
    -ImageName "myImage" `
    -Location "East US" `
    -VirtualNetworkName "myImageVnet" `
    -SubnetName "myImageSubnet" `
    -SecurityGroupName "myImageNSG" `
    -PublicIpAddressName "myImagePIP" `
    -OpenPorts 3389
```

We raden u aan om het aantal gelijktijdige implementaties te beperken tot 20 virtuele machines uit één installatie kopie. Als u grootschalige, gelijktijdige implementaties van meer dan 20 Vm's van dezelfde aangepaste installatie kopie plant, moet u een galerie met [gedeelde afbeeldingen](shared-image-galleries.md) gebruiken met meerdere installatie kopieën van replica's. 

## <a name="image-management"></a>Installatiekopiebeheer 

Hier volgen enkele voorbeelden van algemene taken voor beheerde installatiekopieën en hoe u deze kunt uitvoeren met PowerShell.

Geef een lijst weer van alle installatiekopieën gesorteerd op naam.

```azurepowershell-interactive
$images = Get-AzResource -ResourceType Microsoft.Compute/images 
$images.name
```

Een installatiekopie verwijderen. In dit voorbeeld wordt de installatiekopie met de naam *myImage* verwijderd uit *myResourceGroup*.

```azurepowershell-interactive
Remove-AzImage `
    -ImageName myImage `
    -ResourceGroupName myResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een aangepaste installatiekopie voor een virtuele machine gemaakt. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Systeem voorbereiden en virtuele machines generaliseren
> * Een aangepaste installatiekopie maken
> * Een VM maken van een aangepaste installatiekopie
> * Alle installatiekopieën in uw abonnement weergeven
> * Een aangepaste installatiekopie verwijderen

Ga naar de volgende zelf studie voor meer informatie over het maken van Maxi maal beschik bare virtuele machines.

> [!div class="nextstepaction"]
> [Virtuele machines met hoge beschikbaarheid maken](tutorial-availability-sets.md)



