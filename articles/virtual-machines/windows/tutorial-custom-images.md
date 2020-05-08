---
title: 'Zelf studie: aangepaste VM-installatie kopieën maken met Azure PowerShell'
description: In deze zelf studie leert u hoe u Azure PowerShell kunt gebruiken voor het maken van een installatie kopie van een Windows-aangepaste virtuele machine die is opgeslagen in een galerie met gedeelde installatie kopieën van Azure.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 05/01/2020
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 9061cbbae0b30881fffe1762208216cb8009594a
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791575"
---
# <a name="tutorial-create-windows-vm-images-with-azure-powershell"></a>Zelf studie: Windows VM-installatie kopieën maken met Azure PowerShell

Installatie kopieën kunnen worden gebruikt om implementaties te Boots trappen en consistentie tussen meerdere Vm's te garanderen. In deze zelf studie maakt u uw eigen gespecialiseerde installatie kopie van een virtuele Azure-machine met behulp van Power shell en slaat u deze op in een galerie met gedeelde afbeeldingen. Procedures voor:

> [!div class="checklist"]
> * Een gedeelde installatiekopiegalerie maken
> * Een definitie van een installatie kopie maken
> * De versie van een installatiekopie maken
> * Een virtuele machine maken op basis van een installatie kopie 
> * Een galerie met installatie kopieën delen



## <a name="before-you-begin"></a>Voordat u begint

In de onderstaande stappen wordt beschreven hoe u een bestaande virtuele machine maakt en deze inschakelt in een herbruikbare aangepaste installatie kopie die u kunt gebruiken om nieuwe virtuele machines te maken.

Om het voorbeeld in deze zelfstudie uit te voeren, moet u een bestaande virtuele machine hebben. Als dat nodig is, kunt u de [Power shell-Snelstartgids](quick-create-powershell.md) bekijken om een virtuele machine te maken die u voor deze zelf studie kunt gebruiken. Wanneer u de zelf studie doorloopt, vervangt u de resource namen waar nodig.

## <a name="overview"></a>Overzicht

Een [Galerie met gedeelde afbeeldingen](shared-image-galleries.md) vereenvoudigt het delen van aangepaste afbeeldingen in uw organisatie. Aangepaste installatiekopieën zijn soortgelijk aan Marketplace-installatiekopieën, maar u kunt deze zelf maken. Aangepaste installatiekopieën kunnen worden gebruikt voor het opstarten van configuraties, zoals het vooraf laden van toepassingen, toepassingsconfiguraties en andere besturingssysteemconfiguraties. 

Met de galerie gedeelde afbeeldingen kunt u uw aangepaste VM-installatie kopieën delen met anderen. Kies welke installatie kopieën u wilt delen, in welke regio's u ze beschikbaar wilt maken en met wie u wilt delen. 

De functie gedeelde installatie kopie galerie heeft meerdere bron typen:

[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../../includes/virtual-machines-shared-image-gallery-resources.md)]


## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. 

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. U kunt Cloud Shell ook starten op een afzonderlijk browser tabblad door naar te [https://shell.azure.com/powershell](https://shell.azure.com/powershell)gaan. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op Enter om de code uit te voeren.

## <a name="get-the-vm"></a>De virtuele machine ophalen

U kunt een lijst met virtuele machines die beschikbaar zijn in een resource groep weer geven met behulp van [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm). Wanneer u de naam van de virtuele machine en de resource groep weet, `Get-AzVM` kunt u opnieuw gebruiken om het VM-object op te halen en dit op te slaan in een variabele, zodat u deze later kunt gebruiken. In dit voor beeld wordt een virtuele machine met de naam *sourceVM* van de resource groep ' myResourceGroup ' opgehaald en toegewezen aan de variabele *$VM*. 

```azurepowershell-interactive
$sourceVM = Get-AzVM `
   -Name sourceVM `
   -ResourceGroupName myResourceGroup
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep met de opdracht [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup).

Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. In het volgende voor beeld wordt een resource groep met de naam *myGalleryRG* gemaakt in de regio *eastus* :

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'EastUS'
```

## <a name="create-an-image-gallery"></a>Een galerie met installatie kopieën maken 

Een galerie met installatie kopieën is de primaire resource die wordt gebruikt voor het inschakelen van het delen van afbeeldingen. Toegestane tekens voor de naam van de galerie bestaan uit hoofd letters of kleine letters, cijfers, punten en punten. De naam van de galerie mag geen streepjes bevatten. Galerie namen moeten uniek zijn binnen uw abonnement. 

Maak een galerie met installatie kopieën met [New-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/new-azgallery). In het volgende voor beeld wordt een galerie gemaakt met de naam *myGallery* in de resource groep *myGalleryRG* .

```azurepowershell-interactive
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
```


## <a name="create-an-image-definition"></a>Een definitie van een installatie kopie maken 

Afbeeldings definities maken een logische groepering voor installatie kopieën. Ze worden gebruikt voor het beheren van informatie over de installatie kopieën die in deze versies worden gemaakt. Definitie namen van afbeeldingen kunnen bestaan uit hoofd letters, kleine letters, cijfers, punten, streepjes en punten. Zie [afbeeldings definities](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions)voor meer informatie over de waarden die u kunt opgeven voor de definitie van een installatie kopie.

Maak de definitie van de installatie kopie met behulp van [New-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). In dit voor beeld heeft de galerie-afbeelding de naam *myGalleryImage* en wordt deze gemaakt voor een gespecialiseerde afbeelding. 

```azurepowershell-interactive
$galleryImage = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-an-image-version"></a>De versie van een installatiekopie maken

Een installatie kopie versie maken op basis van een virtuele machine met [New-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). 

Toegestane tekens voor de versie van de installatie kopie zijn getallen en punten. Getallen moeten binnen het bereik van een 32-bits geheel getal zijn. Indeling: *MajorVersion*. *MinorVersion*. *Patch*.

In dit voor beeld is de versie van de installatie kopie *1.0.0* en wordt deze gerepliceerd naar zowel VS- *Oost* als *Zuid-Centraal VS* -data centers. Wanneer u doel regio's voor replicatie kiest, moet u de *bron* regio als doel voor replicatie toevoegen.

Als u een installatie kopie versie wilt maken op basis `$vm.Id.ToString()` van de `-Source`virtuele machine, gebruikt u voor de.

```azurepowershell-interactive
   $region1 = @{Name='South Central US';ReplicaCount=1}
   $region2 = @{Name='East US';ReplicaCount=2}
   $targetRegions = @($region1,$region2)

New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -TargetRegion $targetRegions  `
   -Source $vm.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-01'
```

Het kan even duren om de installatie kopie te repliceren naar alle doel regio's.


## <a name="create-a-vm"></a>Een virtuele machine maken 

Zodra u een gespecialiseerde afbeelding hebt, kunt u een of meer nieuwe virtuele machines maken. Met de cmdlet [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) . Als u de installatie kopie wilt gebruiken, gebruikt u '` and set the `set-AzVMSourceImage-id ' in de definitie-id van de installatie kopie ($GalleryImage. id in dit geval) om altijd de nieuwste versie van de installatie kopie te gebruiken. 

Vervang resource namen naar behoefte in dit voor beeld. 

```azurepowershell-interactive
# Create some variables for the new VM.
$resourceGroup = "myResourceGroup"
$location = "South Central US"
$vmName = "mySpecializedVM"

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create the network resources.
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name $vmName -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageVersion.Id to specify the image version.
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMSourceImage -Id $galleryImage.Id | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```


## <a name="share-the-gallery"></a>De galerie delen

U wordt aangeraden toegang te delen op het niveau van de galerie met installatie kopieën. Gebruik een e-mail adres en de cmdlet [Get-AzADUser](/powershell/module/az.resources/get-azaduser) om de object-id voor de gebruiker op te halen en gebruik vervolgens [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) om hen toegang tot de galerie te geven. Vervang de voorbeeld-e alinne_montes@contoso.com -mail in dit voor beeld met uw eigen gegevens.

```azurepowershell-interactive
# Get the object ID for the user
$user = Get-AzADUser -StartsWith alinne_montes@contoso.com
# Grant access to the user for our gallery
New-AzRoleAssignment `
   -ObjectId $user.Id `
   -RoleDefinitionName Reader `
   -ResourceName $gallery.Name `
   -ResourceType Microsoft.Compute/galleries `
   -ResourceGroupName $resourceGroup.ResourceGroupName
```
   
## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u deze niet meer nodig hebt, kunt u de cmdlet [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) gebruiken om de resource groep en alle gerelateerde resources te verwijderen:

```azurepowershell-interactive
# Delete the gallery 
Remove-AzResourceGroup -Name myGalleryRG

# Delete the VM
Remove-AzResourceGroup -Name myResoureceGroup
```

## <a name="azure-image-builder"></a>Azure Image Builder

Azure biedt ook een service, gebouwd op verpakker, [Azure VM Image Builder](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-overview). Geef een beschrijving van uw aanpassingen in een sjabloon, waarna het maken van de installatie kopie wordt afgehandeld. 

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een speciale VM-installatie kopie gemaakt. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een gedeelde installatiekopiegalerie maken
> * Een definitie van een installatie kopie maken
> * De versie van een installatiekopie maken
> * Een virtuele machine maken op basis van een installatie kopie 
> * Een galerie met installatie kopieën delen

Ga naar de volgende zelf studie voor meer informatie over het maken van Maxi maal beschik bare virtuele machines.

> [!div class="nextstepaction"]
> [Virtuele machines met hoge beschikbaarheid maken](tutorial-availability-sets.md)



