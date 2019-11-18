---
title: Een bastion-host maken met Azure Power shell | Microsoft Docs
description: In dit artikel leest u hoe u een Azure bastion-host maakt
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: 6cae6d258da2ddf0c3bfaade65ae74f1201b67b7
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74121070"
---
# <a name="create-an-azure-bastion-host-using-azure-powershell"></a>Een Azure bastion-host maken met behulp van Azure PowerShell

In dit artikel wordt beschreven hoe u een Azure bastion-host maakt. Zodra u de Azure Bastion-service in uw virtuele netwerk hebt ingericht, is de naadloze RDP/SSH-ervaring beschikbaar voor al uw virtuele machines in hetzelfde virtuele netwerk. Deze implementatie is per virtueel netwerk, niet per abonnement/account of virtuele machine.

## <a name="before-you-begin"></a>Voordat u begint

Controleer of u een Azure-abonnement hebt. Als u nog geen Azure-abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) of [u aanmelden voor een gratis account](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="createhost"></a>Een bastion-host maken

Deze sectie helpt u bij het maken van een nieuwe Azure Bastion-resource met behulp van Azure PowerShell.

1. Maak een virtueel netwerk en een Azure Bastion-subnet. U moet het Azure Bastion-subnet maken met de naam waarde **AzureBastionSubnet**. Met deze waarde kan Azure weten met welk subnet de Bastion-resources moeten worden geïmplementeerd. Dit wijkt af van een gateway-subnet. U moet een subnet van Mini maal een/27 of groter subnet (/27,/26, enzovoort) gebruiken. Maak de **AzureBastionSubnet** zonder route tabellen of delegaties. Wanneer u netwerk beveiligings groepen op de **AzureBastionSubnet**gebruikt, raadpleegt u [werken met nsg's](bastion-nsg.md).

   ```azurepowershell-interactive
   $subnetName = "AzureBastionSubnet"
   $subnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
   $vnet = New-AzVirtualNetwork -Name "myVnet" -ResourceGroupName " myBastionRG " -Location "westeurope" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
   ```

2. Maak een openbaar IP-adres voor Azure Bastion. De open bare IP is het open bare IP-adres van de Bastion-bron waarop RDP/SSH wordt gebruikt (via poort 443). Het open bare IP-adres moet zich in dezelfde regio bevinden als de Bastion-resource die u maakt.

   ```azurepowershell-interactive
   $publicip = New-AzPublicIpAddress -ResourceGroupName "myBastionRG" -name "myPublicIP" -location "westeurope" -AllocationMethod Static -Sku Standard
   ```

3. Maak een nieuwe Azure Bastion-resource in de AzureBastionSubnet van uw virtuele netwerk. Het duurt ongeveer 5 minuten voordat de Bastion-resource wordt gemaakt en geïmplementeerd.

   ```azurepowershell-interactive
   $bastion = New-AzBastion -ResourceGroupName " myBastionRG " -Name "myBastion" -PublicIpAddress $publicip -VirtualNetwork $vnet
   ```

## <a name="next-steps"></a>Volgende stappen

Lees de [Veelgestelde vragen over Bastion](bastion-faq.md).
