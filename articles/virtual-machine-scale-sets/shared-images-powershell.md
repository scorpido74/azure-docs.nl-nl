---
title: Gedeelde VM-afbeeldingen gebruiken om een schaalset in Azure te maken
description: Meer informatie over het gebruik van de Azure PowerShell om gedeelde VM-afbeeldingen te maken die u gebruiken voor het implementeren van virtuele machineschaalsets in Azure.
author: axayjo
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.openlocfilehash: 5f4eca88614a98f0caf87d04847029328042edd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368737"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-powershell"></a>Gedeelde afbeeldingen maken en gebruiken voor virtuele machineschaalsets met de Azure PowerShell

Wanneer u een schaalset maakt, geeft u een installatiekopie op die moet worden gebruikt wanneer de VM-exemplaren zijn geïmplementeerd. De shared image gallery-service vereenvoudigt het delen van aangepaste afbeeldingen in uw hele organisatie sterk. Aangepaste installatiekopieën zijn soortgelijk aan Marketplace-installatiekopieën, maar u kunt deze zelf maken. Aangepaste installatiekopieën kunnen worden gebruikt voor het opstarten van configuraties, zoals het vooraf laden van toepassingen, toepassingsconfiguraties en andere besturingssysteemconfiguraties. 

Met de gedeelde afbeeldingsgalerie u uw aangepaste VM-afbeeldingen delen met anderen in uw organisatie, binnen of tussen regio's, binnen een AAD-tenant. Kies in welke afbeeldingen u wilt delen, in welke regio's u ze beschikbaar wilt maken en met wie u ze wilt delen. U meerdere galerieën maken, zodat u op logischmogelijke wijze gedeelde afbeeldingen groeperen. 

De galerie is een bron op het hoogste niveau die volledige role-based access control (RBAC) biedt. Afbeeldingen kunnen worden geversioneerd en u ervoor kiezen om elke afbeeldingsversie te repliceren naar een andere set Azure-regio's. De galerie werkt alleen met Beheerde afbeeldingen. 

De functie Gedeelde afbeeldingsgalerie heeft meerdere resourcetypen. We zullen deze gebruiken of bouwen in dit artikel:

| Resource | Beschrijving|
|----------|------------|
| **Beheerde afbeelding** | Dit is een basisafbeelding die alleen kan worden gebruikt of kan worden gebruikt om een **afbeeldingsversie** in een afbeeldingsgalerie te maken. Beheerde afbeeldingen worden gemaakt op gealgemene VM's. Een beheerde afbeelding is een speciaal type VHD dat kan worden gebruikt om meerdere VM's te maken en kan nu worden gebruikt om versies van gedeelde afbeeldingen te maken. |
| **Afbeeldingsgalerie** | Net als de Azure Marketplace is een **afbeeldingsgalerie** een opslagplaats voor het beheren en delen van afbeeldingen, maar u bepaalt wie toegang heeft. |
| **Afbeeldingsdefinitie** | Afbeeldingen worden gedefinieerd in een galerie en bevatten informatie over de afbeelding en vereisten voor het intern gebruiken ervan. Dit omvat of de afbeelding Windows of Linux is, release notes en minimale en maximale geheugenvereisten. Het is een definitie van een type beeld. |
| **Versie van installatiekopie** | Een **afbeeldingsversie** is wat u gebruikt om een vm te maken wanneer u een galerie gebruikt. U meerdere versies van een afbeelding hebben als dat nodig is voor uw omgeving. Net als een beheerde afbeelding wordt de afbeeldingsversie gebruikt om nieuwe schijven voor de virtuele machine te maken wanneer u een **afbeeldingsversie** gebruikt om een afbeeldingsversie te maken. Afbeeldingsversies kunnen meerdere keren worden gebruikt. |

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Voordat u begint

In de onderstaande stappen wordt gedetailleerd beschreven hoe u van een bestaande virtuele machine een herbruikbare aangepaste installatiekopie maakt die u kunt gebruiken om nieuwe virtuele machines te maken.

Als u het voorbeeld in dit artikel wilt voltooien, moet u een bestaande beheerde afbeelding hebben. U [Zelfstudie volgen: Maak en gebruik een aangepaste afbeelding voor virtuele machineschaalsets met Azure PowerShell](tutorial-use-custom-image-powershell.md) om er indien nodig een te maken. Als de beheerde afbeelding een gegevensschijf bevat, mag de grootte van de gegevensschijf niet meer dan 1 TB bedragen.

Wanneer u het artikel doorloopt, vervangt u de resourcegroep en VM-namen waar nodig.


[!INCLUDE [virtual-machines-common-shared-images-ps](../../includes/virtual-machines-common-shared-images-powershell.md)]

## <a name="create-a-scale-set-from-the-shared-image-version"></a>Een schaalset maken op basis van de gedeelde afbeeldingsversie

Maak een virtuele-machineschaalset met behulp van [New-AzVmss](/powershell/module/az.compute/new-azvmss). In het volgende voorbeeld wordt een schaalset gemaakt van de nieuwe afbeeldingsversie in het datacenter *in South Central US.* Stel desgevraagd uw eigen beheerdersreferenties in voor de VM-exemplaren in de schaalset:


```azurepowershell-interactive
# Define variables
$resourceGroupName = "myVMSSRG"
$scaleSetName = "myScaleSet"
$location = "South Central US"
$cred = Get-Credential

# Create a resource group
New-AzResourceGroup -ResourceGroupName $resourceGroupName -Location $location

# Create a netowrking pieces
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
  -ImageReferenceId $imageVersion.Id

# Complete the configuration
Set-AzVmssOsProfile $vmssConfig `
  -AdminUsername $cred.UserName `
  -AdminPassword $cred.Password `
  -ComputerNamePrefix "myVM"
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

[!INCLUDE [virtual-machines-common-gallery-list-ps](../../includes/virtual-machines-common-gallery-list-ps.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-ps](../../includes/virtual-machines-common-shared-images-update-delete-ps.md)]


## <a name="next-steps"></a>Volgende stappen

U ook bronnen voor Shared Image Gallery maken met sjablonen. Er zijn verschillende Azure Quickstart-sjablonen beschikbaar: 

- [Een gedeelde afbeeldingsgalerie maken](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Een afbeeldingsdefinitie maken in een gedeelde afbeeldingsgalerie](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Een afbeeldingsversie maken in een gedeelde afbeeldingsgalerie](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Een VM maken op basis van afbeeldingsversie](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Zie het [overzicht](shared-image-galleries.md)voor meer informatie over gedeelde galerieën. Zie [Problemen oplossen met gedeelde afbeeldingsgalerieën](troubleshooting-shared-images.md)als u problemen ondervindt.
