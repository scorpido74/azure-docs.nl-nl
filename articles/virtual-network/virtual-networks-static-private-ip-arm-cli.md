---
title: Privé-IP-adressen configureren voor VM's - Azure CLI
description: Meer informatie over het configureren van privé-IP-adressen voor virtuele machines met behulp van de Azure command-line interface (CLI).
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
ms.openlocfilehash: f4643aae0b28861f4ddb99d8dace749e62f930b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78199475"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-cli"></a>Privé-IP-adressen configureren voor een virtuele machine met behulp van de Azure CLI


[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

> [!NOTE]
> De volgende voorbeeldopdrachten van Azure CLI verwachten een bestaande eenvoudige omgeving. Als u de opdrachten wilt uitvoeren zoals deze in dit document worden weergegeven, bouwt u eerst de testomgeving die is beschreven in [een vnet maken.](quick-create-cli.md)

## <a name="specify-a-static-private-ip-address-when-creating-a-vm"></a>Een statisch privé-IP-adres opgeven bij het maken van een vm

Als u een VM met de naam *DNS01* wilt maken in het *FrontEnd-subnet* van een VNet met de naam *TestVNet* met een statisch privé-IP van *192.168.1.101,* voert u de volgende stappen uit:

1. Als u dit nog niet hebt gedaan, installeert en configureert u de nieuwste [Azure CLI](/cli/azure/install-azure-cli) en logt u in bij een Azure-account met behulp van [az-aanmelding.](/cli/azure/reference-index)

2. Maak een openbaar IP voor de VM met de opdracht [public-ip-maken van het AZ-netwerk.](/cli/azure/network/public-ip) De lijst die na de uitvoer wordt weergegeven, beschrijft de gebruikte parameters.

    > [!NOTE]
    > Mogelijk wilt of moet u verschillende waarden gebruiken voor uw argumenten in deze en volgende stappen, afhankelijk van uw omgeving.

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

   * `--resource-group`: Naam van de resourcegroep waarin het openbare IP-adres kan worden gemaakt.
   * `--name`: Naam van het openbare IP.
   * `--location`: Azure-regio waarin het openbare IP-adres kan worden gemaakt.

3. Voer de opdracht [nic create van het AZ-netwerk uit](/cli/azure/network/nic) om een NIC met een statisch privé-IP te maken. De lijst die na de uitvoer wordt weergegeven, beschrijft de gebruikte parameters. 
   
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

    * `--private-ip-address`: Statisch privé-IP-adres voor de NIC.
    * `--vnet-name`: Naam van de VNet waarin de NIC te maken.
    * `--subnet`: Naam van het subnet waarin de NIC kan worden gemaakt.

4. Voer de opdracht [azure vm-maak uit](/cli/azure/vm/nic) om de VM te maken met behulp van het openbare IP- en NIC dat eerder is gemaakt. De lijst die na de uitvoer wordt weergegeven, beschrijft de gebruikte parameters.
   
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
   
   Andere parameters dan de [basisvan AZ vm maken](/cli/azure/vm) parameters.

   * `--nics`: Naam van de NIC waaraan de VM is gekoppeld.
   
Het wordt aanbevolen dat u het privé-IP-adres dat is toegewezen aan de virtuele Azure-machine niet statisch toewijst in het besturingssysteem van een vm, tenzij dat nodig is, bijvoorbeeld bij [het toewijzen van meerdere IP-adressen aan een Windows VM.](virtual-network-multiple-ip-addresses-cli.md) Als u het privé-IP-adres in het besturingssysteem handmatig instelt, moet u ervoor zorgen dat het hetzelfde adres is als het privé-IP-adres dat is toegewezen aan de [Azure-netwerkinterface,](virtual-network-network-interface-addresses.md#change-ip-address-settings)of u de verbinding met de virtuele machine verliezen. Meer informatie over [instellingen voor privé-IP-adres.](virtual-network-network-interface-addresses.md#private)

## <a name="retrieve-static-private-ip-address-information-for-a-vm"></a>Statische privé-IP-adresgegevens voor een virtuele- of-privé-adresgegevens ophalen

Voer de volgende opdracht Azure CLI uit om de waarden voor *private IP-alloc-methode* en *privé-IP-adres*in acht te nemen:

```azurecli
az vm show -g TestRG -n DNS01 --show-details --query 'privateIps'
```

Verwachte uitvoer:

```json
"192.168.1.101"
```

Als u de specifieke IP-informatie van de NIC voor die vm wilt weergeven, zoekt u specifiek de NIC op:

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

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>Een statisch privé-IP-adres verwijderen uit een virtuele machine

U een statisch privé-IP-adres niet verwijderen uit een NIC in Azure CLI voor Azure Resource Manager-implementaties. U moet:
- Een nieuwe NIC maken die een dynamisch IP-adres gebruikt
- Stel de NIC op de VM doe de nieuw gemaakte NIC. 

Voer de volgende stappen uit om de NIC voor de VM te wijzigen die in de vorige opdrachten is gebruikt:

1. Voer de opdracht **azure-netwerknic-maken uit** om een nieuwe NIC te maken met dynamische IP-toewijzing met een nieuw IP-adres. Omdat er geen IP-adres is opgegeven, is de toewijzingsmethode **Dynamisch**.

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

2. Voer de opdracht **azure vm-set uit** om de NIC te wijzigen die door de VM wordt gebruikt.
   
    ```azurecli
   az vm nic set --resource-group TestRG --vm-name DNS01 --nics TestNIC2
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
    > Als de VM groot genoeg is om meer dan één NIC te hebben, voert u de opdracht **voor het verwijderen van azure-netwerknic uit** om de oude NIC te verwijderen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het beheren van [IP-adresinstellingen](virtual-network-network-interface-addresses.md).
