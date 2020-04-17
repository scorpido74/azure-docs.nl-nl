---
title: Gedeelde VM-afbeeldingen maken met Azure PowerShell
description: Meer informatie over het gebruik van Azure PowerShell om een gedeelde virtuele machineafbeelding in Azure te maken
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/06/2019
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: cfaf545ad2388688666a04076559ff82b7a5d120
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458059"
---
# <a name="create-a-shared-image-gallery-with-azure-powershell"></a>Een gedeelde afbeeldingsgalerie maken met Azure PowerShell 

Een [gedeelde afbeeldingsgalerie](shared-image-galleries.md) vereenvoudigt het delen van aangepaste afbeeldingen in uw hele organisatie. Aangepaste installatiekopieën zijn soortgelijk aan Marketplace-installatiekopieën, maar u kunt deze zelf maken. Aangepaste afbeeldingen kunnen worden gebruikt om implementatietaken op te start zetten, zoals het vooraf laden van toepassingen, toepassingsconfiguraties en andere OS-configuraties. 

Met de gedeelde afbeeldingsgalerie u uw aangepaste VM-afbeeldingen delen met anderen in uw organisatie, binnen of tussen regio's, binnen een AAD-tenant. Kies in welke afbeeldingen u wilt delen, in welke regio's u ze beschikbaar wilt maken en met wie u ze wilt delen. U meerdere galerieën maken, zodat u op logischmogelijke wijze gedeelde afbeeldingen groeperen. 

De galerie is een bron op het hoogste niveau die volledige role-based access control (RBAC) biedt. Afbeeldingen kunnen worden geversioneerd en u ervoor kiezen om elke afbeeldingsversie te repliceren naar een andere set Azure-regio's. De galerie werkt alleen met Beheerde afbeeldingen.

De functie Gedeelde afbeeldingsgalerie heeft meerdere resourcetypen. We zullen deze gebruiken of bouwen in dit artikel:

| Resource | Beschrijving|
|----------|------------|
| **Beheerde afbeelding** | Dit is een basisafbeelding die alleen kan worden gebruikt of kan worden gebruikt om een **afbeeldingsversie** in een afbeeldingsgalerie te maken. Beheerde afbeeldingen worden gemaakt op gealgemene VM's. Een beheerde afbeelding is een speciaal type VHD dat kan worden gebruikt om meerdere VM's te maken en kan nu worden gebruikt om versies van gedeelde afbeeldingen te maken. |
| **Afbeeldingsgalerie** | Net als de Azure Marketplace is een **afbeeldingsgalerie** een opslagplaats voor het beheren en delen van afbeeldingen, maar u bepaalt wie toegang heeft. |
| **Afbeeldingsdefinitie** | Afbeeldingen worden gedefinieerd in een galerie en bevatten informatie over de afbeelding en vereisten voor het intern gebruiken ervan. Dit omvat of de afbeelding Windows of Linux is, release notes en minimale en maximale geheugenvereisten. Het is een definitie van een type beeld. |
| **Versie van installatiekopie** | Een **afbeeldingsversie** is wat u gebruikt om een vm te maken wanneer u een galerie gebruikt. U meerdere versies van een afbeelding hebben als dat nodig is voor uw omgeving. Net als een beheerde afbeelding wordt de afbeeldingsversie gebruikt om nieuwe schijven voor de virtuele machine te maken wanneer u een **afbeeldingsversie** gebruikt om een afbeeldingsversie te maken. Afbeeldingsversies kunnen meerdere keren worden gebruikt. |

Voor elke 20 VM's die u gelijktijdig maakt, raden we u aan één replica te bewaren. Als u bijvoorbeeld tegelijkertijd 120 VM's maakt met dezelfde afbeelding in een regio, raden we u aan ten minste 6 replica's van uw afbeelding te bewaren. Zie [Schalen voor](/azure/virtual-machines/windows/shared-image-galleries#scaling)meer informatie.

## <a name="before-you-begin"></a>Voordat u begint

Als u het voorbeeld in dit artikel wilt voltooien, moet u een bestaande beheerde afbeelding hebben. U [Zelfstudie volgen: maak een aangepaste afbeelding van een Azure VM met Azure PowerShell](tutorial-custom-images.md) om er indien nodig een te maken. Als de beheerde afbeelding een gegevensschijf bevat, mag de grootte van de gegevensschijf niet meer dan 1 TB bedragen.

Wanneer u dit artikel doorloopt, vervangt u de resourcegroep en VM-namen waar nodig.

[!INCLUDE [virtual-machines-common-shared-images-powershell](../../../includes/virtual-machines-common-shared-images-powershell.md)]

 
## <a name="create-vms-from-an-image"></a>VM's maken op basis van een afbeelding

Zodra de afbeeldingsversie is voltooid, u een of meer nieuwe VM's maken. Met behulp van de [Nieuw-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) cmdlet. 

In dit voorbeeld wordt een VM met de naam *myVMfromImage*gemaakt in de *myResourceGroup* in het datacenter in South *Central US.*


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
[Azure Image Builder (preview)](image-builder-overview.md) kan helpen bij het automatiseren van het maken van afbeeldingsversies, u het zelfs gebruiken om een nieuwe afbeeldingsversie bij te werken en [te maken vanuit een bestaande afbeeldingsversie.](image-builder-gallery-update-image-version.md) 

U ook bronnen voor Shared Image Gallery maken met sjablonen. Er zijn verschillende Azure Quickstart-sjablonen beschikbaar: 

- [Een gedeelde afbeeldingsgalerie maken](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Een afbeeldingsdefinitie maken in een gedeelde afbeeldingsgalerie](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Een afbeeldingsversie maken in een gedeelde afbeeldingsgalerie](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Een VM maken op basis van afbeeldingsversie](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Zie het [overzicht](shared-image-galleries.md)voor meer informatie over gedeelde galerieën. Zie [Problemen oplossen met gedeelde afbeeldingsgalerieën](troubleshooting-shared-images.md)als u problemen ondervindt.

