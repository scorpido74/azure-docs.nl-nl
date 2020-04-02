---
title: Een Bastion-host maken met Azure Powershell | Microsoft Documenten
description: In dit artikel vindt u meer informatie over het maken van een Azure Bastion-host
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 43d834f0c834696cd4a836466c9663fe7c31a392
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520503"
---
# <a name="create-an-azure-bastion-host-using-azure-powershell"></a>Een Azure Bastion-host maken met Azure PowerShell

In dit artikel ziet u hoe u een Azure Bastion-host maakt met PowerShell. Zodra u de Azure Bastion-service in uw virtuele netwerk indient, is de naadloze RDP/SSH-ervaring beschikbaar voor alle VM's in hetzelfde virtuele netwerk. Azure Bastion-implementatie is per virtueel netwerk, niet per abonnement/account of virtuele machine.

Optioneel u een Azure Bastion-host maken met behulp van de [Azure-portal.](bastion-create-host-portal.md)

## <a name="before-you-begin"></a>Voordat u begint

Controleer of u een Azure-abonnement hebt. Als u nog geen Azure-abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) of [u aanmelden voor een gratis account](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Een Bastion-host maken

Met deze sectie u een nieuwe Azure Bastion-bron maken met Azure PowerShell.

1. Maak een virtueel netwerk en een Subnet Azure Bastion. U moet het subnet Azure Bastion maken met de naamwaarde **AzureBastionSubnet**. Met deze waarde weet Azure naar welk subnet de Bastion-resources moeten worden geïmplementeerd. Dit is anders dan een Gateway-subnet. U moet een subnet van ten minste /27 of groter subnet gebruiken (/27, /26, enzovoort). Maak het **AzureBastionSubnet** zonder routetabellen of delegaties. Als u netwerkbeveiligingsgroepen op het **AzureBastionSubnet**gebruikt, raadpleegt u het artikel [Werken met NSGs.](bastion-nsg.md)

   ```azurepowershell-interactive
   $subnetName = "AzureBastionSubnet"
   $subnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
   $vnet = New-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myBastionRG" -Location "westeurope" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
   ```

2. Maak een openbaar IP-adres voor Azure Bastion. Het openbare IP-adres is het openbare IP-adres van de Bastion-bron waarop RDP/SSH zal worden geopend (via poort 443). Het openbare IP-adres moet zich in dezelfde regio bevinden als de Bastion-bron die u maakt.

   ```azurepowershell-interactive
   $publicip = New-AzPublicIpAddress -ResourceGroupName "myBastionRG" -name "myPublicIP" -location "westeurope" -AllocationMethod Static -Sku Standard
   ```

3. Maak een nieuwe Azure Bastion-bron in het AzureBastionSubnet van uw virtuele netwerk. Het duurt ongeveer 5 minuten voordat de Bastion-bron is gemaakt en geïmplementeerd.

   ```azurepowershell-interactive
   $bastion = New-AzBastion -ResourceGroupName "myBastionRG" -Name "myBastion" -PublicIpAddress $publicip -VirtualNetwork $vnet
   ```

## <a name="next-steps"></a>Volgende stappen

* Lees de [Veelgestelde vragen over bastion](bastion-faq.md) voor meer informatie.

* Zie [Werken met NSGs](bastion-nsg.md)als u netwerkbeveiligingsgroepen wilt gebruiken met het subnet Azure Bastion.
