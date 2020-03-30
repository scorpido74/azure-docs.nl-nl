---
title: Een vm maken met een statisch openbaar IP-adres - PowerShell | Microsoft Documenten
description: Meer informatie over het maken van een vm met een statisch openbaar IP-adres met PowerShell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ad975ab9-d69f-45c1-9e45-0d3f0f51e87e
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: kumud
ms.openlocfilehash: 0eb4f86a2484486658171ab4b099794e4ba3e4bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76043396"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-powershell"></a>Een virtuele machine maken met een statisch openbaar IP-adres met PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U een virtuele machine maken met een statisch openbaar IP-adres. Met een openbaar IP-adres u vanaf het internet communiceren met een virtuele machine. Wijs een statisch openbaar IP-adres toe, in plaats van een dynamisch adres, om ervoor te zorgen dat het adres nooit verandert. Meer informatie over [statische openbare IP-adressen](virtual-network-ip-addresses-overview-arm.md#allocation-method). Zie [IP-adressen toevoegen, wijzigen of verwijderen](virtual-network-network-interface-addresses.md)als u een openbaar IP-adres wilt wijzigen dat is toegewezen aan een bestaande virtuele machine van dynamisch naar statisch, of om met privé-IP-adressen te werken. Openbare IP-adressen hebben een [nominale vergoeding](https://azure.microsoft.com/pricing/details/ip-addresses)en er is een [limiet](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) aan het aantal openbare IP-adressen dat u per abonnement gebruiken.

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

U de volgende stappen uitvoeren vanaf uw lokale computer of met de Azure Cloud Shell. Als u uw lokale computer wilt gebruiken, moet u ervoor zorgen dat de [Azure PowerShell is geïnstalleerd.](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json) Als u de Azure Cloud Shell wilt gebruiken, selecteert **u Probeer het** in de rechterbovenhoek van een opdrachtvak dat volgt. De Cloud Shell ondertekent u in Azure.

1. Als u de Cloud Shell gebruikt, gaat u naar stap 2. Open een opdrachtsessie en `Connect-AzAccount`meld u aan bij Azure met .
2. Maak een resourcegroep met de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). In het volgende voorbeeld wordt een resourcegroep in de Azure-regio Oost-VS gezoend:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myResourceGroup -Location EastUS
   ```

3. Maak een virtuele machine met de opdracht [Nieuw-AzVM.](/powershell/module/az.Compute/New-azVM) De `-AllocationMethod "Static"` optie wijst een statisch openbaar IP-adres toe aan de virtuele machine. In het volgende voorbeeld wordt een virtuele Windows Server-machine gemaakt met een statisch, eenvoudig SKU-openbaar IP-adres met de naam *myPublicIpAddress*. Geef desgevraagd een gebruikersnaam en wachtwoord op om te worden gebruikt als aanmeldingsreferenties voor de virtuele machine:

   ```azurepowershell-interactive
   New-AzVm `
     -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "East US" `
     -PublicIpAddressName "myPublicIpAddress" `
     -AllocationMethod "Static"
   ```

   Als het openbare IP-adres een standaard SKU moet zijn, moet u [een openbaar IP-adres maken,](virtual-network-public-ip-address.md#create-a-public-ip-address) [een netwerkinterface maken,](virtual-network-network-interface.md#create-a-network-interface) [het openbare IP-adres toewijzen aan de netwerkinterface](virtual-network-network-interface-addresses.md#add-ip-addresses)en vervolgens [een virtuele machine maken met de netwerkinterface,](virtual-network-network-interface-vm.md#add-existing-network-interfaces-to-a-new-vm)in afzonderlijke stappen. Meer informatie over [SKU's voor openbaar IP-adres](virtual-network-ip-addresses-overview-arm.md#sku). Als de virtuele machine wordt toegevoegd aan de back-endpool van een openbare Azure Load Balancer, moet de SKU van het openbare IP-adres van de virtuele machine overeenkomen met de SKU van het openbare IP-adres van de load balancer. Zie Azure [Load Balancer](../load-balancer/concepts-limitations.md#skus)voor meer informatie.

4. Bekijk het toegewezen openbare IP-adres en bevestig dat het is gemaakt als een statisch adres, met [Get-AzPublicIpAddress:](/powershell/module/az.network/get-azpublicipaddress)

   ```azurepowershell-interactive
   Get-AzPublicIpAddress `
     -ResourceGroupName "myResourceGroup" `
     -Name "myPublicIpAddress" `
     | Select "IpAddress", "PublicIpAllocationMethod" `
     | Format-Table
   ```

   Azure heeft een openbaar IP-adres toegewezen aan adressen die worden gebruikt in de regio waarin u de virtuele machine hebt gemaakt. U kunt de lijst met bereiken (voorvoegsels) downloaden voor de Azure-clouds [Openbaar](https://www.microsoft.com/download/details.aspx?id=56519), [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) en [Duitsland](https://www.microsoft.com/download/details.aspx?id=57064).

> [!WARNING]
> Wijzig de IP-adresinstellingen in het besturingssysteem van de virtuele machine niet. Het besturingssysteem is niet op de hoogte van openbare IP-adressen van Azure. Hoewel u privé-IP-adresinstellingen aan het besturingssysteem toevoegen, raden we u aan dit niet te doen tenzij dat nodig is, en pas na het lezen [een privé-IP-adres toevoegen aan een besturingssysteem.](virtual-network-network-interface-addresses.md#private)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer dit niet meer nodig is, u [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) gebruiken om de brongroep en alle bronnen die deze bevat te verwijderen:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [openbare IP-adressen](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) in Azure
- Meer informatie over alle [instellingen voor openbare IP-adres](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Meer informatie over [privé-IP-adressen](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) en het toewijzen van een [statisch privé-IP-adres](virtual-network-network-interface-addresses.md#add-ip-addresses) aan een virtuele Azure-machine
- Meer informatie over [het](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) maken van virtuele Linux- en [Windows-machines](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
