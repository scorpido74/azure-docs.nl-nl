---
title: Een openbaar IP-adres scheiden van een Azure VM
titlesuffix: Azure Virtual Network
description: Meer informatie over het scheiden van een openbaar IP-adres van een virtuele machine
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2019
ms.author: kumud
ms.openlocfilehash: 1c27af30f97ea967d170b2cccaefb2e95f8fedaf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900743"
---
# <a name="dissociate-a-public-ip-address-from-an-azure-vm"></a>Een openbaar IP-adres scheiden van een Azure VM 

In dit artikel leert u hoe u een openbaar IP-adres scheiden van een virtuele Azure-machine (VM).

U de [Azure-portal](#azure-portal), de Azure [command-line interface](#azure-cli) (CLI) of [PowerShell](#powershell) gebruiken om een openbaar IP-adres van een VM te scheiden.

## <a name="azure-portal"></a>Azure Portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Blader naar of zoek naar de virtuele machine waarvan u het openbare IP-adres wilt loskoppelen en selecteer deze vervolgens.
3. Selecteer op de vm-pagina **Overzicht**en selecteer het openbare IP-adres zoals weergegeven in de volgende afbeelding:

   ![Openbaar IP selecteren](./media/remove-public-ip-address/remove-public-ip-address-2.png)

4. Selecteer op de pagina Openbaar IP-adres **overzicht**en selecteer vervolgens **Dissociaat,** zoals in de volgende afbeelding wordt weergegeven:

    ![Openbare IP scheiden](./media/remove-public-ip-address/remove-public-ip-address-3.png)

5. Selecteer **Dissociate public IP address** **Ja**.

## <a name="azure-cli"></a>Azure-CLI

Installeer de [Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)of gebruik de Azure Cloud Shell. De Azure Cloud Shell is een gratis Bash-shell die u rechtstreeks in Azure Portal kunt uitvoeren. In deze shell is de Azure CLI vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Selecteer de knop **Probeer het** in de volgende CLI-opdrachten. Als u **Try it** selecteert, wordt een Cloud Shell aangeroepen waarmee u zich aanmelden bij uw Azure-account.

1. Als u de CLI lokaal in Bash `az login`gebruikt, meldt u zich aan bij Azure met .
2. Een openbaar IP-adres is gekoppeld aan een IP-configuratie van een netwerkinterface die is gekoppeld aan een vm. Gebruik de opdracht [nic-ip-config-update van het AZ-netwerk](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update) om een openbaar IP-adres los te maken van een IP-configuratie. In het volgende voorbeeld wordt een openbaar IP-adres met de naam *myVMPublicIP* losgekoppeld van de IP-configuratie met de naam *ipconfigmyVM* van een bestaande netwerkinterface met de naam *myVMVMNic* die is gekoppeld aan een VM met de naam *myVM* in een brongroep met de naam *myResourceGroup*.
  
   ```azurecli-interactive
    az network nic ip-config update \
    --name ipconfigmyVM \
    --resource-group myResourceGroup \
    --nic-name myVMVMNic \
    --remove PublicIpAddress
   ```

   Als u de naam van een netwerkinterface die aan uw vm is gekoppeld, niet weet, gebruikt u de opdracht [az vm nic-lijst](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list) om deze te bekijken. In de volgende opdracht worden bijvoorbeeld de namen weergegeven van de netwerkinterfaces die zijn gekoppeld aan een VM met de naam *myVM* in een resourcegroep met de naam *myResourceGroup:*

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     De uitvoer bevat een of meer regels die vergelijkbaar zijn met het volgende voorbeeld:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     In het vorige voorbeeld is *myVMVMNic* de naam van de netwerkinterface.

   - Als u de naam van een IP-configuratie voor een netwerkinterface niet kent, gebruikt u de opdracht van de [az-netwerknic ip-config-lijst](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list) om deze op te halen. In de volgende opdracht worden bijvoorbeeld de namen van de openbare IP-configuraties voor een netwerkinterface met de naam *myVMVMNic* weergegeven in een resourcegroep met de naam *myResourceGroup:*

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

   - Als u de naam van een openbare IP-configuratie voor een netwerkinterface niet kent, gebruikt u de opdracht [nic ip-config show van az-netwerk](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-show) om deze op te halen. In de volgende opdracht worden bijvoorbeeld de namen van de openbare IP-configuraties voor een netwerkinterface met de naam *myVMVMNic* weergegeven in een resourcegroep met de naam *myResourceGroup:*

     ```azurecli-interactive
     az network nic ip-config show --name ipconfigmyVM --nic-name myVMVMNic --resource-group myResourceGroup --query publicIPAddress.id
     ```


## <a name="powershell"></a>PowerShell

Installeer [PowerShell](/powershell/azure/install-az-ps)of gebruik de Azure Cloud Shell. De Azure Cloud Shell is een gratis shell die u rechtstreeks vanuit Azure Portal kunt uitvoeren. Het heeft PowerShell vooraf geïnstalleerd en geconfigureerd om te gebruiken met uw account. Selecteer de knop **Proberen in** de powershell-opdrachten die volgen. Als u **Try it** selecteert, wordt een Cloud Shell aangeroepen waarmee u zich aanmelden bij uw Azure-account.

1. Als u PowerShell lokaal gebruikt, `Connect-AzAccount`meldt u zich aan bij Azure met .
2. Een openbaar IP-adres is gekoppeld aan een IP-configuratie van een netwerkinterface die is gekoppeld aan een vm. Gebruik de opdracht [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) om een netwerkinterface te krijgen. Stel de waarde van het openbare IP-adres in op null en gebruik de opdracht [Set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) om de nieuwe IP-configuratie naar de netwerkinterface te schrijven.

   In het volgende voorbeeld wordt een openbaar IP-adres met de naam *myVMPublicIP* losgekoppeld van een netwerkinterface met de naam *myVMVMNic* die is gekoppeld aan een VM met de naam *myVM*. Alle resources bevinden zich in een resourcegroep met de naam *myResourceGroup*.
  
   ```azurepowershell
    $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroup myResourceGroup
    $nic.IpConfigurations.publicipaddress.id = $null
    Set-AzNetworkInterface -NetworkInterface $nic
   ```

  - Als u de naam van een netwerkinterface die aan uw VM is gekoppeld, niet weet, gebruikt u de opdracht [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) om deze te bekijken. In de volgende opdracht worden bijvoorbeeld de namen weergegeven van de netwerkinterfaces die zijn gekoppeld aan een VM met de naam *myVM* in een resourcegroep met de naam *myResourceGroup:*

    ```azurepowershell
    $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
    $vm.NetworkProfile
    ```

     De uitvoer bevat een of meer regels die vergelijkbaar zijn met het voorbeeld dat volgt. In het voorbeeld uitvoer, *myVMVMNic* is de naam van de netwerkinterface.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Als u de naam van een IP-configuratie voor een netwerkinterface niet kent, gebruikt u de opdracht [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) om deze op te halen. In de volgende opdracht worden bijvoorbeeld de namen van de IP-configuraties voor een netwerkinterface met de naam *myVMVMNic* weergegeven in een resourcegroep met de naam *myResourceGroup:*

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations.id
     ```

     De uitvoer bevat een of meer regels die vergelijkbaar zijn met het voorbeeld dat volgt. In het voorbeeld uitvoer, *ipconfigmyVM* is de naam van een IP-configuratie.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM"
     ```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [koppelen van een openbaar IP-adres aan een virtuele machine](associate-public-ip-address-vm.md).
