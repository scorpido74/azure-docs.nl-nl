---
title: 'Zelf studie: hoge Beschik baarheid voor virtuele Windows-machines in azure'
description: In deze zelfstudie leert u hoe u Azure PowerShell gebruikt om maximaal beschikbare virtuele machines in beschikbaarheidssets te implementeren
documentationcenter: ''
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: tutorial
ms.date: 11/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 0f94f4d312cefec80a0f294e256ee1ad908b903c
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74068139"
---
# <a name="tutorial-create-and-deploy-highly-available-virtual-machines-with-azure-powershell"></a>Zelfstudie: Virtuele machines met hoge beschikbaarheid maken en implementeren met Azure PowerShell

In deze zelfstudie leert u hoe u de beschikbaarheid en betrouwbaarheid van uw virtuele machines (VM’s) kunt vergroten met behulp van beschikbaarheidssets. Beschikbaarheidssets zorgen ervoor dat de VM's die u in Azure implementeert, worden verdeeld over meerdere geïsoleerde hardwareknooppunten in een cluster. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een beschikbaarheidsset maken
> * Een VM maken in een beschikbaarheidsset
> * Beschikbare VM-grootten controleren
> * Azure Advisor controleren


## <a name="availability-set-overview"></a>Overzicht beschikbaarheidsset

Een Beschikbaarheidsset is een logische groeperings mogelijkheid voor het isoleren van VM-resources van elkaar wanneer deze worden geïmplementeerd. Azure zorgt ervoor dat de VM's die u in een beschikbaarheidsset plaatst, op meerdere fysieke servers, compute-racks, opslageenheden en netwerkswitches worden uitgevoerd. Als er een hardware- of softwarefout optreedt, heeft dit gevolgen voor slechts een subset van uw VM’s en blijft uw totale oplossing operationeel. Beschikbaarheidssets zijn essentieel voor het bouwen van betrouwbare cloudoplossingen.

Laten we eens kijken naar een typische VM-oplossing met vier front-end webservers en twee VM's in de back-end. Met Azure wilt u twee beschikbaarheidssets definiëren voordat u uw VM's implementeert: eentje voor de web-laag en eentje voor de back-endlaag. Wanneer u een nieuwe VM maakt, geeft u de beschikbaarheidsset op als parameter. Azure zorgt ervoor dat de VM’s worden geïsoleerd over meerdere fysieke hardwareresources. Als er een probleem is met de fysieke hardware waarop een van uw servers draait, weet u dat de andere instanties van uw servers actief blijven, omdat ze worden uitgevoerd op andere hardware.

Gebruik beschikbaarheidssets wanneer u betrouwbare VM-oplossingen wilt implementeren in Azure.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. 

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. U kunt Cloud Shell ook openen in een afzonderlijk browsertabblad door naar [https://shell.azure.com/powershell](https://shell.azure.com/powershell) te gaan. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op Enter om de code uit te voeren.

## <a name="create-an-availability-set"></a>Een beschikbaarheidsset maken

De hardware op een locatie is onderverdeeld in meerdere updatedomeinen en foutdomeinen. Een **updatedomein** is een groep VM's en onderliggende hardware die op hetzelfde moment opnieuw kan worden opgestart. VM's in hetzelfde **foutdomein** delen dezelfde opslag en hebben een gemeenschappelijke voedingsbron en netwerkswitch.  

U kunt een beschikbaarheidsset maken met behulp van [New-AzAvailabilitySet](https://docs.microsoft.com/powershell/module/az.compute/new-azavailabilityset). In dit voorbeeld is *2* het aantal voor zowel de updatedomeinen als de foutdomeinen en is *myAvailabilitySet* de naam van de beschikbaarheidsset.

Maak een resourcegroep.

```azurepowershell-interactive
New-AzResourceGroup `
   -Name myResourceGroupAvailability `
   -Location EastUS
```

Maak een beheerde beschikbaarheidsset met behulp van [New-AzAvailabilitySet](https://docs.microsoft.com/powershell/module/az.compute/new-azavailabilityset) met de parameter `-sku aligned`.

```azurepowershell-interactive
New-AzAvailabilitySet `
   -Location "EastUS" `
   -Name "myAvailabilitySet" `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Sku aligned `
   -PlatformFaultDomainCount 2 `
   -PlatformUpdateDomainCount 2
```

## <a name="create-vms-inside-an-availability-set"></a>VM's maken in een beschikbaarheidsset
VM's moeten binnen de beschikbaarheidsset worden gemaakt, zodat ze correct over de hardware worden verdeeld. U kunt geen bestaande VM toevoegen aan een beschikbaarheidsset nadat deze is gemaakt. 


Wanneer u een VM maakt met [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm), gebruikt u de parameter `-AvailabilitySetName` om de naam van de beschikbaarheidsset op te geven.

Stel eerst een beheerdersnaam en -wachtwoord in voor de virtuele machine met [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Maak nu twee VM's met [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) in de beschikbaarheidsset.

```azurepowershell-interactive
for ($i=1; $i -le 2; $i++)
{
    New-AzVm `
        -ResourceGroupName "myResourceGroupAvailability" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -PublicIpAddressName "myPublicIpAddress$i" `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred
}
```

Het duurt enkele minuten om beide VM's te maken en te configureren. Wanneer dit klaar is, hebt u twee virtuele machines die zijn gedistribueerd over de onderliggende hardware. 

Als u de beschikbaarheidsset in de portal bekijkt door naar **Resourcegroepen** > **myResourceGroupAvailability** > **myAvailabilitySet** te gaan, ziet u hoe de VM's zijn verdeeld over de twee fout- en updatedomeinen.

![Beschikbaarheidsset in de portal](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Beschikbare VM-grootten controleren 

U kunt later meer VM's toevoegen aan de beschikbaarheidsset, maar u moet weten welke VM-grootten beschikbaar zijn op de hardware. Gebruik [Get-AzVMSize](https://docs.microsoft.com/powershell/module/az.compute/get-azvmsize) om een lijst weer te geven met alle beschikbare grootten op het hardwarecluster voor de beschikbaarheidsset.

```azurepowershell-interactive
Get-AzVMSize `
   -ResourceGroupName "myResourceGroupAvailability" `
   -AvailabilitySetName "myAvailabilitySet"
```

## <a name="check-azure-advisor"></a>Azure Advisor controleren 

U kunt ook Azure Advisor gebruiken om meer informatie te krijgen over het verbeteren van de beschikbaarheid van uw VM's. Azure Advisor analyseert uw configuratie en gebruikstelemetrie, en raadt vervolgens oplossingen aan die u kunnen helpen de kosteneffectiviteit, prestaties, beschikbaarheid en beveiliging van uw Azure-resources te verbeteren.

Meld u aan bij [Azure Portal](https://portal.azure.com), selecteer **Alle services** en typ **Advisor**. Op het Advisor-dashboard worden de gepersonaliseerde aanbevelingen voor het geselecteerde abonnement weergegeven. Zie [Aan de slag met Azure Advisor](../../advisor/advisor-get-started.md) voor meer informatie.


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Een beschikbaarheidsset maken
> * Een VM maken in een beschikbaarheidsset
> * Beschikbare VM-grootten controleren
> * Azure Advisor controleren

Ga naar de volgende zelfstudie voor meer informatie over virtuele-machineschaalsets.

> [!div class="nextstepaction"]
> [Een VM-schaalset maken](tutorial-create-vmss.md)


