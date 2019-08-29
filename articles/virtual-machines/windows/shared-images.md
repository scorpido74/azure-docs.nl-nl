---
title: Gedeelde VM-installatie kopieën maken met Azure PowerShell | Microsoft Docs
description: Meer informatie over het gebruik van Azure PowerShell voor het maken van een installatie kopie van een gedeelde virtuele machine in azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/06/2019
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: 9842f57c7d8d49aa9d1b3d17f82f3519ecead98c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70088596"
---
# <a name="create-a-shared-image-gallery-with-azure-powershell"></a>Een galerie met gedeelde afbeeldingen maken met Azure PowerShell 

Een [Galerie met gedeelde afbeeldingen](shared-image-galleries.md) vereenvoudigt het delen van aangepaste afbeeldingen in uw organisatie. Aangepaste installatiekopieën zijn soortgelijk aan Marketplace-installatiekopieën, maar u kunt deze zelf maken. Aangepaste installatie kopieën kunnen worden gebruikt om implementatie taken te Boots trapn, zoals het vooraf laden van toepassingen, toepassings configuraties en andere besturingssysteem configuraties. 

Met de galerie voor gedeelde afbeeldingen kunt u uw aangepaste VM-installatie kopieën delen met anderen in uw organisatie, binnen of tussen verschillende regio's binnen een AAD-Tenant. Kies welke installatie kopieën u wilt delen, in welke regio's u ze beschikbaar wilt maken en met wie u wilt delen. U kunt meerdere galerieën maken zodat u gedeelde installatie kopieën logisch kunt groeperen. 

De galerie is een resource op het hoogste niveau die volledige op rollen gebaseerd toegangs beheer (RBAC) biedt. Installatie kopieën kunnen versie nummer hebben en u kunt ervoor kiezen om elke installatie kopie versie te repliceren naar een andere set Azure-regio's. De galerie werkt alleen met beheerde installatie kopieën.

De functie gedeelde installatie kopie galerie heeft meerdere bron typen. We gebruiken of maken deze in dit artikel:

| Resource | Description|
|----------|------------|
| **Beheerde installatie kopie** | Dit is een basis installatie kopie die alleen kan worden gebruikt of gebruikt om een **installatie kopie versie** in een galerie met installatie kopieën te maken. Beheerde installatie kopieën worden gemaakt op basis van gegeneraliseerde Vm's. Een beheerde installatie kopie is een speciaal type VHD dat kan worden gebruikt om meerdere virtuele machines te maken en kan nu worden gebruikt om versies van gedeelde installatie kopieën te maken. |
| **Galerie met installatie kopieën** | Net als de Azure Marketplace is een **afbeeldings galerie** een opslag plaats voor het beheren en delen van installatie kopieën, maar u bepaalt wie toegang heeft. |
| **Definitie van installatie kopie** | Installatie kopieën worden in een galerie gedefinieerd en bevatten informatie over de installatie kopie en de vereisten voor het intern gebruik. Dit betekent of de installatie kopie Windows of Linux, release opmerkingen en minimale en maximale geheugen vereisten is. Het is een definitie van een type installatie kopie. |
| **Versie van installatie kopie** | Een **installatie kopie versie** is wat u gebruikt om een virtuele machine te maken wanneer u een galerie gebruikt. U kunt meerdere versies van een installatie kopie naar behoefte hebben voor uw omgeving. Net als bij een beheerde installatie kopie wordt de versie van de installatie kopie gebruikt voor het maken van nieuwe schijven voor de virtuele machine wanneer u een **installatie kopie** gebruikt voor het maken van een virtuele machine. Installatie kopie versies kunnen meerdere keren worden gebruikt. |

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Voordat u begint

Om het voor beeld in dit artikel te volt ooien, moet u een bestaande beheerde installatie kopie hebben. U kunt zelf [studie volgen: Maak een aangepaste installatie kopie van een Azure-VM](tutorial-custom-images.md) met Azure PowerShell om er een te maken, indien nodig. Als de beheerde installatie kopie een gegevens schijf bevat, kan de grootte van de gegevens schijf niet groter zijn dan 1 TB.

Wanneer u dit artikel doorwerkt, moet u de namen van de resource groep en de VM vervangen, indien nodig.

[!INCLUDE [virtual-machines-common-shared-images-powershell](../../../includes/virtual-machines-common-shared-images-powershell.md)]

 
## <a name="create-vms-from-an-image"></a>Vm's maken op basis van een installatie kopie

Zodra de versie van de installatie kopie is voltooid, kunt u een of meer nieuwe virtuele machines maken. Met de cmdlet [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) . 

In dit voor beeld wordt een VM gemaakt met de naam *myVMfromImage*in de *MyResourceGroup* in het *Zuid-Centraal* -Data Center.


```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$location = "South Central US"
$vmName = "myVMfromImage"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Network pieces
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
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageVersion.Id to specify the shared image
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -Id $imageVersion.Id | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

[!INCLUDE [virtual-machines-common-gallery-list-ps](../../../includes/virtual-machines-common-gallery-list-ps.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-ps](../../../includes/virtual-machines-common-shared-images-update-delete-ps.md)]

## <a name="next-steps"></a>Volgende stappen
Met [Azure Image Builder (preview)](image-builder-overview.md) kunt u het maken van de installatie kopie versie automatiseren, maar u kunt deze zelfs gebruiken om [een nieuwe installatie kopie versie te maken op basis van een bestaande versie van de installatie kopie](image-builder-gallery-update-image-version.md). 

U kunt ook een resource voor de galerie met gedeelde afbeeldingen maken met behulp van sjablonen. Er zijn verschillende Azure Quick Start-sjablonen beschikbaar: 

- [Een galerie met gedeelde afbeeldingen maken](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Een definitie van een installatie kopie maken in een galerie met gedeelde afbeeldingen](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Een installatie kopie versie maken in een galerie met gedeelde afbeeldingen](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Een VM maken op basis van de installatie kopie versie](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Zie het [overzicht](shared-image-galleries.md)voor meer informatie over gedeelde afbeeldings galerieën. Als u problemen ondervindt, raadpleegt u [problemen met de galerie met gedeelde afbeeldingen oplossen](troubleshooting-shared-images.md).

