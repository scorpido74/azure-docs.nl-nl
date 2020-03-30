---
title: Een VM maken met een statisch privé-IP-adres - Azure PowerShell
description: Meer informatie over het maken van een virtuele machine met een privé-IP-adres met PowerShell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: d5f18929-15e3-40a2-9ee3-8188bc248ed8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2019
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 1745ca176fac18b4903686cb556670531ee40a1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244756"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-powershell"></a>Een virtuele machine maken met een statisch privé-IP-adres met PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U een virtuele machine (VM) maken met een statisch privé IP-adres. Wijs een statisch privé IP-adres toe, in plaats van een dynamisch adres, als u wilt selecteren welk adres uit een subnet aan een vm is toegewezen. Meer informatie over [statische privé-IP-adressen](virtual-network-ip-addresses-overview-arm.md#allocation-method). Zie [IP-adressen toevoegen, wijzigen of verwijderen](virtual-network-network-interface-addresses.md)als u een privé-IP-adres wilt wijzigen dat is toegewezen aan een bestaande virtuele- tot statische virtuele virtuele id, of dat u wilt werken met openbare IP-adressen.

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

U de volgende stappen uitvoeren vanaf uw lokale computer of met de Azure Cloud Shell. Als u uw lokale computer wilt gebruiken, moet u ervoor zorgen dat de [Azure PowerShell is geïnstalleerd.](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json) Als u de Azure Cloud Shell wilt gebruiken, selecteert **u Probeer het** in de rechterbovenhoek van een opdrachtvak dat volgt. De Cloud Shell ondertekent u in Azure.

1. Als u de Cloud Shell gebruikt, gaat u naar stap 2. Open een opdrachtsessie en `Connect-AzAccount`meld u aan bij Azure met .
2. Maak een resourcegroep met de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). In het volgende voorbeeld wordt een resourcegroep in de Azure-regio Oost-VS gezoend:

   ```azurepowershell-interactive
   $RgName = "myResourceGroup"
   $Location = "eastus"
   New-AzResourceGroup -Name $RgName -Location $Location
   ```

3. Maak een subnetconfiguratie en virtueel netwerk met de opdrachten [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) en [New-AzVirtualNetwork:](/powershell/module/az.network/new-azvirtualnetwork)

   ```azurepowershell-interactive
   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name MySubnet `
   -AddressPrefix 10.0.0.0/24

   # Create a virtual network
   $VNet = New-AzVirtualNetwork `
   -ResourceGroupName $RgName `
   -Location $Location `
   -Name MyVNet `
   -AddressPrefix 10.0.0.0/16 `
   -Subnet $subnetConfig

   # Get the subnet object for use in a later step.
   $Subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetConfig.Name -VirtualNetwork $VNet
   ```

4. Maak een netwerkinterface in het virtuele netwerk en wijs een privé-IP-adres van het subnet toe aan de netwerkinterface met de opdrachten [New-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/New-AzNetworkInterfaceIpConfig) en [New-AzNetworkInterface:](/powershell/module/az.network/new-aznetworkinterface)

   ```azurepowershell-interactive
   $IpConfigName1 = "IPConfig-1"
   $IpConfig1     = New-AzNetworkInterfaceIpConfig `
     -Name $IpConfigName1 `
     -Subnet $Subnet `
     -PrivateIpAddress 10.0.0.4 `
     -Primary

   $NIC = New-AzNetworkInterface `
     -Name MyNIC `
     -ResourceGroupName $RgName `
     -Location $Location `
     -IpConfiguration $IpConfig1
   ```

5. Maak een [VM-configuratie met Nieuw-AzVMConfig](/powershell/module/Az.Compute/New-AzVMConfig)en maak vervolgens de VM met [Nieuw-AzVM](/powershell/module/az.Compute/New-azVM). Geef desgevraagd een gebruikersnaam en wachtwoord op om te worden gebruikt als aanmeldingsreferenties voor de VM:

   ```azurepowershell-interactive
   $VirtualMachine = New-AzVMConfig -VMName MyVM -VMSize "Standard_DS3"
   $VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName MyServerVM -ProvisionVMAgent -EnableAutoUpdate
   $VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
   $VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest
   New-AzVM -ResourceGroupName $RgName -Location $Location -VM $VirtualMachine -Verbose
   ```

> [!WARNING]
> Hoewel u privé-IP-adresinstellingen aan het besturingssysteem toevoegen, raden we u aan dit niet te doen totdat u [een privé-IP-adres aan een besturingssysteem](virtual-network-network-interface-addresses.md#private)hebt toegevoegd.
> 
> 
> <a name = "change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface"></a>
> 
> [!IMPORTANT]
> Als u de VM vanaf internet wilt openen, moet u een openbaar IP-adres aan de virtuele machine toewijzen. U ook een dynamische privé-IP-adrestoewijzing wijzigen in een statische toewijzing. Zie [IP-adressen toevoegen of wijzigen](virtual-network-network-interface-addresses.md)voor meer informatie. Daarnaast wordt aanbevolen dat u het netwerkverkeer beperkt tot uw VM door een netwerkbeveiligingsgroep te koppelen aan de netwerkinterface, het subnet waarin u de netwerkinterface hebt gemaakt of beide. Zie [Netwerkbeveiligingsgroepen beheren](manage-network-security-group.md)voor meer informatie .

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer dit niet meer nodig is, u [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) gebruiken om de brongroep en alle bronnen die deze bevat te verwijderen:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [privé-IP-adressen](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) en het toewijzen van een [statisch privé-IP-adres](virtual-network-network-interface-addresses.md#add-ip-addresses) aan een virtuele Azure-machine.
- Meer informatie over [het](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) maken van virtuele Linux- en [Windows-machines.](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
