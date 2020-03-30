---
title: Een openbaar IP-adres koppelen aan een virtuele machine
titlesuffix: Azure Virtual Network
description: Meer informatie over het koppelen van een openbaar IP-adres aan een virtuele machine.
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: kumud
ms.openlocfilehash: 5acda69ce08bc493d5349b084d1cfafc8432145b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647455"
---
# <a name="associate-a-public-ip-address-to-a-virtual-machine"></a>Een openbaar IP-adres koppelen aan een virtuele machine

In dit artikel leert u hoe u een openbaar IP-adres koppelt aan een bestaande virtuele machine (VM). Als u vanaf internet verbinding wilt maken met een virtuele machine, moet de virtuele machine een openbaar IP-adres hebben dat eraan is gekoppeld. Als u een nieuwe virtuele machine met een openbaar IP-adres wilt maken, u dit doen met de [Azure-portal](virtual-network-deploy-static-pip-arm-portal.md), de [Azure command-line interface (CLI)](virtual-network-deploy-static-pip-arm-cli.md)of [PowerShell](virtual-network-deploy-static-pip-arm-ps.md). Openbare IP-adressen hebben een nominale vergoeding. Zie prijzen [voor](https://azure.microsoft.com/pricing/details/ip-addresses/)meer informatie. Er is een limiet aan het aantal openbare IP-adressen dat u per abonnement gebruiken. Zie limieten [voor](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#publicip-address)meer informatie.

U de [Azure-portal](#azure-portal), de Azure [command-line interface](#azure-cli) (CLI) of [PowerShell](#powershell) gebruiken om een openbaar IP-adres aan een VM te koppelen.

## <a name="azure-portal"></a>Azure Portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Blader naar of zoek naar de virtuele machine waaraan u het openbare IP-adres wilt toevoegen en selecteer deze vervolgens.
3. Selecteer **onder Instellingen**de optie **Netwerken**en selecteer vervolgens de netwerkinterface waaraan u het openbare IP-adres wilt toevoegen, zoals in de volgende afbeelding wordt weergegeven:

   ![Netwerkinterface selecteren](./media/associate-public-ip-address-vm/select-nic.png)

   > [!NOTE]
   > Openbare IP-adressen zijn gekoppeld aan netwerkinterfaces die zijn gekoppeld aan een virtuele machine. In de vorige afbeelding heeft de VM slechts één netwerkinterface. Als de VM meerdere netwerkinterfaces had, worden ze allemaal weergegeven en selecteert u de netwerkinterface waaraan u het openbare IP-adres wilt koppelen.

4. Selecteer **IP-configuraties** en selecteer vervolgens een IP-configuratie, zoals in de volgende afbeelding wordt weergegeven:

   ![IP-configuratie selecteren](./media/associate-public-ip-address-vm/select-ip-configuration.png)

   > [!NOTE]
   > Openbare IP-adressen zijn gekoppeld aan IP-configuraties voor een netwerkinterface. In de vorige afbeelding heeft de netwerkinterface één IP-configuratie. Als de netwerkinterface meerdere IP-configuraties had, worden deze allemaal in de lijst weergegeven en selecteert u de IP-configuratie waaraan u het openbare IP-adres wilt koppelen.

5. Selecteer **Ingeschakeld**en selecteer **vervolgens IP-adres (*Vereiste instellingen configureren)***. Kies een bestaand openbaar IP-adres, waarmee het vak **Openbaar IP-adres kiezen** automatisch wordt gesloten. Als u geen openbare IP-adressen hebt die beschikbaar zijn, moet u er een maken. Zie [Een openbaar IP-adres maken](virtual-network-public-ip-address.md#create-a-public-ip-address)voor meer informatie. Selecteer **Opslaan**, zoals weergegeven in de afbeelding die volgt, en sluit het vakvoor de IP-configuratie.

   ![Openbaar IP-adres inschakelen](./media/associate-public-ip-address-vm/enable-public-ip-address.png)

   > [!NOTE]
   > De openbare IP-adressen die worden weergegeven, zijn die welke in dezelfde regio als de vm bestaan. Als u meerdere openbare IP-adressen hebt die in de regio zijn gemaakt, worden ze hier weergegeven. Als deze grijs zijn, is dat omdat het adres al is gekoppeld aan een andere resource.

6. Bekijk het openbare IP-adres dat is toegewezen aan de IP-configuratie, zoals weergegeven in de afbeelding die volgt. Het kan enkele seconden duren voordat een IP-adres wordt weergegeven.

   ![Toegewezen openbaar IP-adres weergeven](./media/associate-public-ip-address-vm/view-assigned-public-ip-address.png)

   > [!NOTE]
   > Het adres wordt toegewezen vanuit een groep adressen die in elke Azure-regio worden gebruikt. Zie [Microsoft Azure Datacenter IP-bereiken](https://www.microsoft.com/download/details.aspx?id=41653)voor een lijst met adresgroepen die in elke regio worden gebruikt. Het toegewezen adres kan elk adres zijn in de groepen die voor de regio worden gebruikt. Als u het adres wilt toegewezen krijgen uit een specifieke groep in de regio, gebruikt u een [voorvoegsel openbaar IP-adres](public-ip-address-prefix.md).

7. [Netwerkverkeer toestaan aan de VM](#allow-network-traffic-to-the-vm) met beveiligingsregels in een netwerkbeveiligingsgroep.

## <a name="azure-cli"></a>Azure-CLI

Installeer de [Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)of gebruik de Azure Cloud Shell. De Azure Cloud Shell is een gratis Bash-shell die u rechtstreeks in Azure Portal kunt uitvoeren. In deze shell is de Azure CLI vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Selecteer de knop **Probeer het** in de volgende CLI-opdrachten. Als u **Try it** selecteert, wordt een Cloud Shell aangeroepen waarmee u zich aanmelden bij uw Azure-account.

1. Als u de CLI lokaal in Bash `az login`gebruikt, meldt u zich aan bij Azure met .
2. Een openbaar IP-adres is gekoppeld aan een IP-configuratie van een netwerkinterface die is gekoppeld aan een vm. Gebruik de opdracht [voor nic-ip-config-update van het AZ-netwerk](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update) om een openbaar IP-adres te koppelen aan een IP-configuratie. In het volgende voorbeeld wordt een bestaand openbaar IP-adres met de naam *myVMPublicIP* gekoppeld aan de IP-configuratie met de naam *ipconfigmyVM* van een bestaande netwerkinterface met de naam *myVMVMNic* die bestaat in een brongroep met de naam *myResourceGroup.*
  
   ```azurecli-interactive
   az network nic ip-config update \
     --name ipconfigmyVM \
     --nic-name myVMVMNic \
     --resource-group myResourceGroup \
     --public-ip-address myVMPublicIP
   ```

   - Als u geen bestaand openbaar IP-adres hebt, gebruikt u de opdracht [openbaar-ip-maken van het AZ-netwerk](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) om er een te maken. Met de volgende opdracht wordt bijvoorbeeld een openbaar IP-adres met de naam *myVMPublicIP* gemaakt in een resourcegroep met de naam *myResourceGroup*.
  
     ```azurecli-interactive
     az network public-ip create --name myVMPublicIP --resource-group myResourceGroup
     ```

     > [!NOTE]
     > Met de vorige opdracht wordt een openbaar IP-adres gemaakt met standaardwaarden voor verschillende instellingen die u mogelijk wilt aanpassen. Zie Een openbaar IP-adres maken voor meer informatie over alle instellingen voor openbare [IP-adres.](virtual-network-public-ip-address.md#create-a-public-ip-address) Het adres wordt toegewezen vanuit een groep openbare IP-adressen die voor elke Azure-regio worden gebruikt. Zie [Microsoft Azure Datacenter IP-bereiken](https://www.microsoft.com/download/details.aspx?id=41653)voor een lijst met adresgroepen die in elke regio worden gebruikt.

   - Als u de naam van een netwerkinterface die aan uw vm is gekoppeld, niet weet, gebruikt u de opdracht [az vm nic-lijst](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list) om deze te bekijken. In de volgende opdracht worden bijvoorbeeld de namen weergegeven van de netwerkinterfaces die zijn gekoppeld aan een VM met de naam *myVM* in een resourcegroep met de naam *myResourceGroup:*

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     De uitvoer bevat een of meer regels die vergelijkbaar zijn met het volgende voorbeeld:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     In het vorige voorbeeld is *myVMVMNic* de naam van de netwerkinterface.

   - Als u de naam van een IP-configuratie voor een netwerkinterface niet kent, gebruikt u de opdracht van de [az-netwerknic ip-config-lijst](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list) om deze op te halen. In de volgende opdracht worden bijvoorbeeld de namen van de IP-configuraties voor een netwerkinterface met de naam *myVMVMNic* weergegeven in een resourcegroep met de naam *myResourceGroup:*

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

3. Bekijk het openbare IP-adres dat is toegewezen aan de IP-configuratie met de opdracht [az vm list-ip-adressen.](/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) In het volgende voorbeeld worden de IP-adressen weergegeven die zijn toegewezen aan een bestaande VM met de naam *myVM* in een resourcegroep met de naam *myResourceGroup*.

   ```azurecli-interactive
   az vm list-ip-addresses --name myVM --resource-group myResourceGroup --out table
   ```

   > [!NOTE]
   > Het adres wordt toegewezen vanuit een groep adressen die in elke Azure-regio worden gebruikt. Zie [Microsoft Azure Datacenter IP-bereiken](https://www.microsoft.com/download/details.aspx?id=41653)voor een lijst met adresgroepen die in elke regio worden gebruikt. Het toegewezen adres kan elk adres zijn in de groepen die voor de regio worden gebruikt. Als u het adres wilt toegewezen krijgen uit een specifieke groep in de regio, gebruikt u een [voorvoegsel openbaar IP-adres](public-ip-address-prefix.md).

4. [Netwerkverkeer toestaan aan de VM](#allow-network-traffic-to-the-vm) met beveiligingsregels in een netwerkbeveiligingsgroep.

## <a name="powershell"></a>PowerShell

Installeer [PowerShell](/powershell/azure/install-az-ps)of gebruik de Azure Cloud Shell. De Azure Cloud Shell is een gratis shell die u rechtstreeks vanuit Azure Portal kunt uitvoeren. Het heeft PowerShell vooraf geïnstalleerd en geconfigureerd om te gebruiken met uw account. Selecteer de knop **Proberen in** de powershell-opdrachten die volgen. Als u **Try it** selecteert, wordt een Cloud Shell aangeroepen waarmee u zich aanmelden bij uw Azure-account.

1. Als u PowerShell lokaal gebruikt, `Connect-AzAccount`meldt u zich aan bij Azure met .
2. Een openbaar IP-adres is gekoppeld aan een IP-configuratie van een netwerkinterface die is gekoppeld aan een vm. Gebruik de [commando's Get-AzVirtualNetwork](/powershell/module/Az.Network/Get-AzVirtualNetwork) en [Get-AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Get-AzVirtualNetworkSubnetConfig) om het virtuele netwerk en subnet waarin de netwerkinterface zich bevindt te krijgen. Gebruik vervolgens de opdracht [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) om een netwerkinterface en de opdracht [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) te krijgen om een bestaand openbaar IP-adres te krijgen. Gebruik vervolgens de opdracht [Set-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/Set-AzNetworkInterfaceIpConfig) om het openbare IP-adres te koppelen aan de IP-configuratie en de opdracht [Set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) om de nieuwe IP-configuratie naar de netwerkinterface te schrijven.

   In het volgende voorbeeld wordt een bestaand openbaar IP-adres met de naam *myVMPublicIP* gekoppeld aan de IP-configuratie met de naam *ipconfigmyVM* van een bestaande netwerkinterface met de naam *myVMVMNic* die bestaat in een subnet met de naam *myVMSubnet* in een virtueel netwerk met de naam *myVMVNet*. Alle resources bevinden zich in een resourcegroep met de naam *myResourceGroup*.
  
   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name myVMVNet -ResourceGroupName myResourceGroup
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name myVMSubnet -VirtualNetwork $vnet
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $pip = Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup
   $nic | Set-AzNetworkInterfaceIpConfig -Name ipconfigmyVM -PublicIPAddress $pip -Subnet $subnet
   $nic | Set-AzNetworkInterface
   ```

   - Als u geen bestaand openbaar IP-adres hebt, gebruikt u de opdracht [Nieuw-AzPublicIpAddress](/powershell/module/Az.Network/New-AzPublicIpAddress) om er een te maken. Met de volgende opdracht wordt bijvoorbeeld een *dynamisch* openbaar IP-adres met de naam *myVMPublicIP* gemaakt in een resourcegroep met de naam *myResourceGroup* in de *eastusregio.*
  
     ```azurepowershell-interactive
     New-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup -AllocationMethod Dynamic -Location eastus
     ```

     > [!NOTE]
     > Met de vorige opdracht wordt een openbaar IP-adres gemaakt met standaardwaarden voor verschillende instellingen die u mogelijk wilt aanpassen. Zie Een openbaar IP-adres maken voor meer informatie over alle instellingen voor openbare [IP-adres.](virtual-network-public-ip-address.md#create-a-public-ip-address) Het adres wordt toegewezen vanuit een groep openbare IP-adressen die voor elke Azure-regio worden gebruikt. Zie [Microsoft Azure Datacenter IP-bereiken](https://www.microsoft.com/download/details.aspx?id=41653)voor een lijst met adresgroepen die in elke regio worden gebruikt.

   - Als u de naam van een netwerkinterface die aan uw VM is gekoppeld, niet weet, gebruikt u de opdracht [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) om deze te bekijken. In de volgende opdracht worden bijvoorbeeld de namen weergegeven van de netwerkinterfaces die zijn gekoppeld aan een VM met de naam *myVM* in een resourcegroep met de naam *myResourceGroup:*

     ```azurepowershell-interactive
     $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
     $vm.NetworkProfile
     ```

     De uitvoer bevat een of meer regels die vergelijkbaar zijn met het voorbeeld dat volgt. In het voorbeeld uitvoer, *myVMVMNic* is de naam van de netwerkinterface.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Als u de naam van het virtuele netwerk of subnet waarin de `Get-AzNetworkInterface` netwerkinterface zich bevindt, niet weet, gebruikt u de opdracht om de informatie weer te geven. Met de volgende opdracht wordt bijvoorbeeld de virtuele netwerk- en subnetgegevens voor een netwerkinterface met de naam *myVMVMNic* in een resourcegroep met de naam *myResourceGroup:*

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $ipConfigs = $nic.IpConfigurations
     $ipConfigs.Subnet | Select Id
     ```

     De uitvoer bevat een of meer regels die vergelijkbaar zijn met het voorbeeld dat volgt. In het voorbeeld uitvoer, *myVMVNET* is de naam van het virtuele netwerk en *myVMSubnet* is de naam van het subnet.
  
     ```
     "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVMVNET/subnets/myVMSubnet",
     ```

   - Als u de naam van een IP-configuratie voor een netwerkinterface niet kent, gebruikt u de opdracht [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) om deze op te halen. In de volgende opdracht worden bijvoorbeeld de namen van de IP-configuraties voor een netwerkinterface met de naam *myVMVMNic* weergegeven in een resourcegroep met de naam *myResourceGroup:*

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations
     ```

     De uitvoer bevat een of meer regels die vergelijkbaar zijn met het voorbeeld dat volgt. In het voorbeeld uitvoer, *ipconfigmyVM* is de naam van een IP-configuratie.
  
     ```
     Id     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM
     ```

3. Bekijk het openbare IP-adres dat is toegewezen aan de IP-configuratie met de opdracht [Get-AzPublicIpAddress.](/powershell/module/az.network/get-azpublicipaddress) In het volgende voorbeeld wordt het adres weergegeven dat is toegewezen aan een openbaar IP-adres met de naam *myVMPublicIP* in een resourcegroep met de naam *myResourceGroup*.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup | Select IpAddress
   ```

   Als u de naam van het openbare IP-adres dat is toegewezen aan een IP-configuratie niet weet, voert u de volgende opdrachten uit om het te krijgen:

   ```azurepowershell-interactive
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $nic.IPConfigurations
   $address = $nic.IPConfigurations.PublicIpAddress
   $address | Select Id
   ```

   De uitvoer bevat een of meer regels die vergelijkbaar zijn met het voorbeeld dat volgt. In de voorbeelduitvoer is *myVMPublicIP* de naam van het openbare IP-adres dat is toegewezen aan de IP-configuratie.

   ```
   "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP"
   ```

   > [!NOTE]
   > Het adres wordt toegewezen vanuit een groep adressen die in elke Azure-regio worden gebruikt. Zie [Microsoft Azure Datacenter IP-bereiken](https://www.microsoft.com/download/details.aspx?id=41653)voor een lijst met adresgroepen die in elke regio worden gebruikt. Het toegewezen adres kan elk adres zijn in de groepen die voor de regio worden gebruikt. Als u het adres wilt toegewezen krijgen uit een specifieke groep in de regio, gebruikt u een [voorvoegsel openbaar IP-adres](public-ip-address-prefix.md).

4. [Netwerkverkeer toestaan aan de VM](#allow-network-traffic-to-the-vm) met beveiligingsregels in een netwerkbeveiligingsgroep.

## <a name="allow-network-traffic-to-the-vm"></a>Netwerkverkeer toestaan aan de VM

Voordat u vanaf internet verbinding maken met het openbare IP-adres, moet u ervoor zorgen dat u de benodigde poorten hebt geopend in een netwerkbeveiligingsgroep die u mogelijk hebt gekoppeld aan de netwerkinterface, het subnet waarin de netwerkinterface zich bevindt of beide. Hoewel beveiligingsgroepen verkeer filteren naar het privé-IP-adres van de netwerkinterface, vertaalt Azure, zodra binnenkomend internetverkeer op het openbare IP-adres aankomt, het openbare adres naar het privé-IP-adres, dus als een netwerkbeveiligingsgroep de verkeersstroom, mislukt de communicatie met het openbare IP-adres. U de effectieve beveiligingsregels voor een netwerkinterface en het subnet bekijken met behulp van de [Portal,](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal) [CLI](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli)of [PowerShell](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell).

## <a name="next-steps"></a>Volgende stappen

Gebruik binnenkomend internetverkeer naar uw VM met een netwerkbeveiligingsgroep. Zie [Werken met netwerkbeveiligingsgroepen](manage-network-security-group.md#work-with-network-security-groups)voor meer informatie over het maken van een netwerkbeveiligingsgroep. Zie [Beveiligingsgroepen](security-overview.md)voor meer informatie over netwerkbeveiligingsgroepen.
