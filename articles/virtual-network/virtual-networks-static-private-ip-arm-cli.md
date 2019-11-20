---
title: Privé-IP-adressen voor Vm's configureren-Azure CLI
description: Meer informatie over het configureren van privé-IP-adressen voor virtuele machines met behulp van de Azure-opdracht regel interface (CLI).
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: 40b03a1a-ea00-454c-b716-7574cea49ac0
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: kumud
ms.openlocfilehash: 5734b96466801efaa991a971bd87f60aafc9df32
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196616"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-cli"></a>Privé IP-adressen configureren voor een virtuele machine met behulp van de Azure CLI

[!INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Dit artikel is van toepassing op het Resource Manager-implementatiemodel. U kunt ook [een statisch privé-IP-adres beheren in het klassieke implementatie model](virtual-networks-static-private-ip-classic-cli.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

> [!NOTE]
> In het volgende voor beeld van Azure CLI-opdrachten wordt een bestaande eenvoudige omgeving verwacht. Als u wilt dat de opdrachten uitvoeren zoals ze worden weergegeven in dit document, eerst samenstellen van de testomgeving die wordt beschreven [een vnet maken](quick-create-cli.md).

## <a name="specify-a-static-private-ip-address-when-creating-a-vm"></a>Een statisch privé-IP-adres opgeven tijdens het maken van een VM

Als u een virtuele machine met de naam *DNS01* in het *frontend* -subnet van een VNet met de naam *TestVNet* met een statisch privé-IP van *192.168.1.101*wilt maken, voert u de volgende stappen uit:

1. Als u dit nog niet hebt gedaan, installeert en configureert u de nieuwste [Azure cli](/cli/azure/install-azure-cli) en meldt u zich aan bij een Azure-account met behulp van [AZ login](/cli/azure/reference-index).

2. Maak een openbaar IP-adres voor de virtuele machine met de opdracht [AZ Network Public-IP Create](/cli/azure/network/public-ip) . De lijst die na de uitvoer wordt weergegeven, beschrijft de gebruikte parameters.

    > [!NOTE]
    > U kunt in deze en de volgende stappen verschillende waarden voor uw argumenten gebruiken, afhankelijk van uw omgeving.

    ```azurecli
    az network public-ip create \
    --name TestPIP \
    --resource-group TestRG \
    --location centralus \
    --allocation-method Static
    ```

    Verwachte uitvoer:

   ```json
   {
        "publicIp": {
            "idleTimeoutInMinutes": 4,
            "ipAddress": "52.176.43.167",
            "provisioningState": "Succeeded",
            "publicIPAllocationMethod": "Static",
            "resourceGuid": "79e8baa3-33ce-466a-846c-37af3c721ce1"
        }
    }
    ```

   * `--resource-group`: de naam van de resource groep waarin het open bare IP-adres moet worden gemaakt.
   * `--name`: de naam van het open bare IP-adres.
   * `--location`: Azure-regio waarin het open bare IP-adres moet worden gemaakt.

3. Voer de opdracht [AZ Network NIC Create](/cli/azure/network/nic) uit om een NIC met een statisch privé-IP-adres te maken. De lijst die na de uitvoer wordt weergegeven, beschrijft de gebruikte parameters. 
   
    ```azurecli
    az network nic create \
    --resource-group TestRG \
    --name TestNIC \
    --location centralus \
    --subnet FrontEnd \
    --private-ip-address 192.168.1.101 \
    --vnet-name TestVNet
    ```

    Verwachte uitvoer:
   
    ```json
    {
        "newNIC": {
            "dnsSettings": {
            "appliedDnsServers": [],
            "dnsServers": []
            },
            "enableIPForwarding": false,
            "ipConfigurations": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/ipconfig1",
                "name": "ipconfig1",
                "properties": {
                "primary": true,
                "privateIPAddress": "192.168.1.101",
                "privateIPAllocationMethod": "Static",
                "provisioningState": "Succeeded",
                "subnet": {
                    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "resourceGroup": "TestRG"
                }
                },
                "resourceGroup": "TestRG"
            }
            ],
            "provisioningState": "Succeeded",
            "resourceGuid": "<guid>"
        }
    }
    ```
    
    Parameters:

    * `--private-ip-address`: statisch persoonlijk IP-adres voor de NIC.
    * `--vnet-name`: de naam van het VNet waarin de NIC moet worden gemaakt.
    * `--subnet`: de naam van het subnet waarin de NIC moet worden gemaakt.

4. Voer de opdracht [Azure VM Create](/cli/azure/vm/nic) uit om de virtuele machine te maken met behulp van de open bare IP en NIC die eerder zijn gemaakt. De lijst die na de uitvoer wordt weergegeven, beschrijft de gebruikte parameters.
   
    ```azurecli
    az vm create \
    --resource-group TestRG \
    --name DNS01 \
    --location centralus \
    --image Debian \
    --admin-username adminuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics TestNIC
    ```

    Verwachte uitvoer:
   
    ```json
    {
        "fqdns": "",
        "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01",
        "location": "centralus",
        "macAddress": "00-0D-3A-92-C1-66",
        "powerState": "VM running",
        "privateIpAddress": "192.168.1.101",
        "publicIpAddress": "",
        "resourceGroup": "TestRG"
    }
    ```
   
   Andere para meters dan de Basic [AZ VM Create](/cli/azure/vm) para meters.

   * `--nics`: de naam van de NIC waaraan de virtuele machine is gekoppeld.
   
Het is raadzaam dat u het privé-IP-adres dat is toegewezen aan de virtuele machine van Azure niet statisch toewijst in het besturings systeem van een VM, tenzij dit nodig is, bijvoorbeeld wanneer [u meerdere IP-adressen toewijst aan een Windows-VM](virtual-network-multiple-ip-addresses-cli.md). Als u het privé-IP-adres hand matig instelt in het besturings systeem, moet u ervoor zorgen dat het hetzelfde adres is als het privé-IP-adres dat is toegewezen aan de Azure- [netwerk interface](virtual-network-network-interface-addresses.md#change-ip-address-settings), of u kunt de verbinding met de virtuele machine verliezen. Meer informatie over instellingen voor [privé-IP-adressen](virtual-network-network-interface-addresses.md#private) .

## <a name="retrieve-static-private-ip-address-information-for-a-vm"></a>Informatie over statisch privé-IP-adres ophalen voor een virtuele machine

Voer de volgende Azure CLI-opdracht uit om de waarden voor de *persoonlijke IP-toewijzings methode* en het *privé-IP-adres*te bekijken:

```azurecli
az vm show -g TestRG -n DNS01 --show-details --query 'privateIps'
```

Verwachte uitvoer:

```json
"192.168.1.101"
```

Als u de specifieke IP-gegevens van de NIC voor die VM wilt weer geven, kunt u het volgende in een query uitvoeren op de NIC:

```azurecli
az network nic show \
-g testrg \
-n testnic \
--query 'ipConfigurations[0].{PrivateAddress:privateIpAddress,IPVer:privateIpAddressVersion,IpAllocMethod:p
rivateIpAllocationMethod,PublicAddress:publicIpAddress}'
```

De uitvoer ziet er ongeveer zo uit:

```json
{
    "IPVer": "IPv4",
    "IpAllocMethod": "Static",
    "PrivateAddress": "192.168.1.101",
    "PublicAddress": null
}
```

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>Een statisch privé-IP-adres uit een virtuele machine verwijderen

U kunt een statisch privé-IP-adres niet verwijderen uit een NIC in azure CLI voor Azure Resource Manager-implementaties. U moet het volgende doen:
- Een nieuwe NIC maken die gebruikmaakt van een dynamisch IP-adres
- Stel de NIC op de virtuele machine in op de zojuist gemaakte NIC. 

Voer de volgende stappen uit om de NIC voor de virtuele machine te wijzigen die wordt gebruikt in de vorige opdrachten:

1. Voer de opdracht **Azure Network NIC Create** uit om een nieuwe NIC te maken met behulp van dynamische IP-toewijzing met een nieuw IP-adres. Omdat er geen IP-adres is opgegeven, is de toewijzings methode **dynamisch**.

    ```azurecli
    az network nic create     \
    --resource-group TestRG     \
    --name TestNIC2     \
    --location centralus     \
    --subnet FrontEnd    \
    --vnet-name TestVNet
    ```        
   
    Verwachte uitvoer:

    ```json
    {
        "newNIC": {
            "dnsSettings": {
            "appliedDnsServers": [],
            "dnsServers": []
            },
            "enableIPForwarding": false,
            "ipConfigurations": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2/ipConfigurations/ipconfig1",
                "name": "ipconfig1",
                "properties": {
                "primary": true,
                "privateIPAddress": "192.168.1.4",
                "privateIPAllocationMethod": "Dynamic",
                "provisioningState": "Succeeded",
                "subnet": {
                    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "resourceGroup": "TestRG"
                }
                },
                "resourceGroup": "TestRG"
            }
            ],
            "provisioningState": "Succeeded",
            "resourceGuid": "0808a61c-476f-4d08-98ee-0fa83671b010"
        }
    }
    ```

2. Voer de **Azure VM set** -opdracht uit om de NIC te wijzigen die door de virtuele machine wordt gebruikt.
   
    ```azurecli
    azure vm set -g TestRG -n DNS01 -N TestNIC2
    ```

    Verwachte uitvoer:
   
    ```json
    [
        {
            "id": "/subscriptions/0e220bf6-5caa-4e9f-8383-51f16b6c109f/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC3",
            "primary": true,
            "resourceGroup": "TestRG"
        }
    ]
    ```

    > [!NOTE]
    > Als de virtuele machine groot genoeg is om meer dan één NIC te hebben, voert u de opdracht **Azure Network NIC verwijderen** uit om de oude NIC te verwijderen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het beheren van [IP-adres instellingen](virtual-network-network-interface-addresses.md).
