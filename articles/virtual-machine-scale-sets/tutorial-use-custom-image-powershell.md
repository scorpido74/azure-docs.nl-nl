---
title: Zelf studie-een aangepaste VM-installatie kopie gebruiken in een schaalset met Azure PowerShell
description: Informatie over het gebruik van Azure PowerShell voor het maken van een aangepaste VM-installatiekopie die u kunt gebruiken voor het implementeren van een virtuele-machineschaalset
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.topic: tutorial
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 4b072991a86922fe2b4ba5be93b4c96841dc24af
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792765"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-azure-powershell"></a>Zelfstudie: Een aangepaste installatiekopie voor virtuele-machineschaalsets maken en gebruiken met Azure PowerShell

Wanneer u een schaalset maakt, geeft u een installatiekopie op die moet worden gebruikt wanneer de VM-exemplaren zijn geïmplementeerd. Om het aantal taken na de implementatie van VM-exemplaren te verminderen, kunt u een aangepaste VM-installatiekopie gebruiken. Deze aangepaste VM-installatiekopie bevat alle geïnstalleerde toepassingen of configuraties die vereist zijn. Alle VM-exemplaren die in de schaalset zijn gemaakt, gebruiken de aangepaste VM-installatiekopie en zijn gereed voor uw toepassingsverkeer. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een galerie met gedeelde afbeeldingen maken
> * Een definitie van een installatie kopie maken
> * Een versie van een installatie kopie maken
> * Een schaalset maken op basis van een afbeelding 
> * Een galerie met installatie kopieën delen

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="before-you-begin"></a>Voordat u begint

In de onderstaande stappen wordt beschreven hoe u een bestaande virtuele machine maakt en deze inschakelt in een nieuwe, bruikbare aangepaste installatie kopie die u kunt gebruiken om een schaalset te maken.

Om het voorbeeld in deze zelfstudie uit te voeren, moet u een bestaande virtuele machine hebben. Als dat nodig is, kunt u de [Power shell-Snelstartgids](quick-create-powershell.md) bekijken om een virtuele machine te maken die u voor deze zelf studie kunt gebruiken. Wanneer u de zelf studie doorloopt, vervangt u de resource namen waar nodig.

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


## <a name="create-an-image-version"></a>Een versie van een installatie kopie maken

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

## <a name="create-a-scale-set-from-the-image"></a>Een schaalset maken op basis van de afbeelding
Maak nu een schaalset met [New-AzVmss](/powershell/module/az.compute/new-azvmss) die gebruikmaakt van de parameter `-ImageName` voor het definiëren van de aangepaste VM-installatiekopie die in de vorige stap is gemaakt. Om het verkeer te distribueren naar de verschillende VM-exemplaren, wordt er ook een load balancer gemaakt. De load balancer bevat regels voor het distribueren van verkeer op TCP-poort 80, en voor het toestaan van verkeer van Extern bureaublad op TCP-poort 3389 en externe toegang via PowerShell op TCP-poort 5985. Geef desgevraagd uw eigen beheerdersreferenties op voor de VM-exemplaren in de schaalset:

```azurepowershell-interactive
# Define variables for the scale set
$resourceGroupName = "myVMSSRG3"
$scaleSetName = "myScaleSet3"
$location = "East US"

# Create a resource group
New-AzResourceGroup -ResourceGroupName $resourceGroupName -Location $location

# Create a networking pieces
$subnet = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.0.0/24
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $resourceGroupName `
  -Name "myVnet" `
  -Location $location `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnet
$publicIP = New-AzPublicIpAddress `
  -ResourceGroupName $resourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -Name "myPublicIP"
$frontendIP = New-AzLoadBalancerFrontendIpConfig `
  -Name "myFrontEndPool" `
  -PublicIpAddress $publicIP
$backendPool = New-AzLoadBalancerBackendAddressPoolConfig -Name "myBackEndPool"
$inboundNATPool = New-AzLoadBalancerInboundNatPoolConfig `
  -Name "myRDPRule" `
  -FrontendIpConfigurationId $frontendIP.Id `
  -Protocol TCP `
  -FrontendPortRangeStart 50001 `
  -FrontendPortRangeEnd 50010 `
  -BackendPort 3389
# Create the load balancer and health probe
$lb = New-AzLoadBalancer `
  -ResourceGroupName $resourceGroupName `
  -Name "myLoadBalancer" `
  -Location $location `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool `
  -InboundNatPool $inboundNATPool
Add-AzLoadBalancerProbeConfig -Name "myHealthProbe" `
  -LoadBalancer $lb `
  -Protocol TCP `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
Add-AzLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol TCP `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe (Get-AzLoadBalancerProbeConfig -Name "myHealthProbe" -LoadBalancer $lb)
Set-AzLoadBalancer -LoadBalancer $lb

# Create IP address configurations
$ipConfig = New-AzVmssIpConfig `
  -Name "myIPConfig" `
  -LoadBalancerBackendAddressPoolsId $lb.BackendAddressPools[0].Id `
  -LoadBalancerInboundNatPoolsId $inboundNATPool.Id `
  -SubnetId $vnet.Subnets[0].Id

# Create a configuration 
$vmssConfig = New-AzVmssConfig `
    -Location $location `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode "Automatic"

# Reference the image version
Set-AzVmssStorageProfile $vmssConfig `
  -OsDiskCreateOption "FromImage" `
  -ImageReferenceId $galleryImage.Id

# Complete the configuration
 
Add-AzVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name "network-config" `
  -Primary $true `
  -IPConfiguration $ipConfig 

# Create the scale set 
New-AzVmss `
  -ResourceGroupName $resourceGroupName `
  -Name $scaleSetName `
  -VirtualMachineScaleSet $vmssConfig
```

Het duurt enkele minuten om alle schaalsetresources en VM's te maken en te configureren.


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

# Delete the scale set resource group
Remove-AzResourceGroup -Name myResoureceGroup
```

## <a name="azure-image-builder"></a>Azure Image Builder

Azure biedt ook een service, gebouwd op verpakker, [Azure VM Image Builder](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-overview). Geef een beschrijving van uw aanpassingen in een sjabloon, waarna het maken van de installatie kopie wordt afgehandeld. 

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u geleerd hoe u een aangepaste VM-installatiekopie maakt en gebruikt voor uw schaalsets met Azure PowerShell:

> [!div class="checklist"]
> * Een galerie met gedeelde afbeeldingen maken
> * Een definitie van een installatie kopie maken
> * Een versie van een installatie kopie maken
> * Een schaalset maken op basis van een afbeelding 
> * Een galerie met installatie kopieën delen

Ga door naar de volgende zelfstudie voor informatie over het implementeren van toepassingen naar uw schaalset.

> [!div class="nextstepaction"]
> [Toepassingen in uw schaalsets implementeren](tutorial-install-apps-powershell.md)
