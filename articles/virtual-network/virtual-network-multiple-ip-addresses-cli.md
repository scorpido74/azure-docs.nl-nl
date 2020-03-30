---
title: VM met meerdere IP-adressen met de Azure CLI
titlesuffix: Azure Virtual Network
description: Meer informatie over het toewijzen van meerdere IP-adressen aan een virtuele machine met behulp van de Azure command-line interface (CLI).
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: kumud
ms.openlocfilehash: 144f30463adb3dfbce1717e06548baccc8286f8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240223"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-cli"></a>Meerdere IP-adressen toewijzen aan virtuele machines met behulp van de Azure CLI

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

In dit artikel wordt uitgelegd hoe u een virtuele machine (VM) maakt via het Azure Resource Manager-implementatiemodel met behulp van azure cli. Meerdere IP-adressen kunnen niet worden toegewezen aan resources die zijn gemaakt via het klassieke implementatiemodel. Lees het artikel [Implementatiemodellen begrijpen](../resource-manager-deployment-model.md) voor meer informatie over Azure-implementatiemodellen.

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Een virtuele machine met meerdere IP-adressen maken

In de volgende stappen wordt uitgelegd hoe u een voorbeeldvirtuele machine maakt met meerdere IP-adressen, zoals beschreven in het scenario. Wijzig variabele waarden in "" en IP-adrestypen, indien nodig, voor uw implementatie. 

1. Installeer de [Azure CLI](/cli/azure/install-azure-cli) als u deze nog niet hebt geïnstalleerd.
2. Maak een SSH-openbaar en privésleutelpaar voor Linux-VM's door de stappen in het [openbare en private sleutelpaar voor Linux-vm's maken.](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
3. Log in een opdrachtopdracht `az login` in met de opdracht en selecteer het abonnement dat u gebruikt.
4. Maak de VM door het script uit te voeren dat volgt op een Linux- of Mac-computer. Het script maakt een resourcegroep, één virtueel netwerk (VNet), één NIC met drie IP-configuraties en een VM met de twee NIC's eraan. De NIC-, openbare IP-adres-, virtuele netwerk- en VM-bronnen moeten allemaal op dezelfde locatie en een abonnement bestaan. Hoewel de resources niet allemaal in dezelfde resourcegroep hoeven te bestaan, doen ze dat in het volgende script.

```bash
    
#!/bin/sh
    
RgName="myResourceGroup"
Location="westcentralus"
az group create --name $RgName --location $Location
    
# Create a public IP address resource with a static IP address using the `--allocation-method Static` option. If you
# do not specify this option, the address is allocated dynamically. The address is assigned to the resource from a pool
# of IP addresses unique to each Azure region. Download and view the file from
# https://www.microsoft.com/en-us/download/details.aspx?id=41653 that lists the ranges for each region.

PipName="myPublicIP"

# This name must be unique within an Azure location.
DnsName="myDNSName"

az network public-ip create \
--name $PipName \
--resource-group $RgName \
--location $Location \
--dns-name $DnsName\
--allocation-method Static

# Create a virtual network with one subnet

VnetName="myVnet"
VnetPrefix="10.0.0.0/16"
VnetSubnetName="mySubnet"
VnetSubnetPrefix="10.0.0.0/24"

az network vnet create \
--name $VnetName \
--resource-group $RgName \
--location $Location \
--address-prefix $VnetPrefix \
--subnet-name $VnetSubnetName \
--subnet-prefix $VnetSubnetPrefix

# Create a network interface connected to the subnet and associate the public IP address to it. Azure will create the
# first IP configuration with a static private IP address and will associate the public IP address resource to it.

NicName="MyNic1"
az network nic create \
--name $NicName \
--resource-group $RgName \
--location $Location \
--subnet $VnetSubnet1Name \
--private-ip-address 10.0.0.4
--vnet-name $VnetName \
--public-ip-address $PipName
    
# Create a second public IP address, a second IP configuration, and associate it to the NIC. This configuration has a
# static public IP address and a static private IP address.

az network public-ip create \
--resource-group $RgName \
--location $Location \
--name myPublicIP2 \
--dns-name mypublicdns2 \
--allocation-method Static

az network nic ip-config create \
--resource-group $RgName \
--nic-name $NicName \
--name IPConfig-2 \
--private-ip-address 10.0.0.5 \
--public-ip-name myPublicIP2

# Create a third IP configuration, and associate it to the NIC. This configuration has  static private IP address and   # no public IP address.

az network nic ip-config create \
--resource-group $RgName \
--nic-name $NicName \
--private-ip-address 10.0.0.6 \
--name IPConfig-3

# Note: Though this article assigns all IP configurations to a single NIC, you can also assign multiple IP configurations
# to any NIC in a VM. To learn how to create a VM with multiple NICs, read the Create a VM with multiple NICs 
# article: https://docs.microsoft.com/azure/virtual-network/virtual-network-deploy-multinic-arm-cli.

# Create a VM and attach the NIC.

VmName="myVm"

# Replace the value for the following **VmSize** variable with a value from the
# https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes article. The script fails if the VM size
# is not supported in the location you select. Run the `azure vm sizes --location eastcentralus` command to get a full list
# of VMs in US West Central, for example.

VmSize="Standard_DS1"

# Replace the value for the OsImage variable value with a value for *urn* from the output returned by entering the
# `az vm image list` command.

OsImage="credativ:Debian:8:latest"

Username="adminuser"

# Replace the following value with the path to your public key file. If you're creating a Windows VM, remove the following
# line and you'll be prompted for the password you want to configure for the VM.

SshKeyValue="~/.ssh/id_rsa.pub"

az vm create \
--name $VmName \
--resource-group $RgName \
--image $OsImage \
--location $Location \
--size $VmSize \
--nics $NicName \
--admin-username $Username \
--ssh-key-value $SshKeyValue
```

Naast het maken van een VM met een NIC met 3 IP-configuraties, maakt het script:

- Standaard één premium beheerde schijf, maar u hebt andere opties voor het schijftype dat u maken. Lees de [Linux-vm maken met het Azure CLI-artikel](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) voor meer informatie.
- Een virtueel netwerk met één subnet en twee openbare IP-adressen. U ook *bestaande* bronnen voor virtueel netwerk, subnet, NIC of openbare IP-adres gebruiken. Voer enter . `az vm create -h`

Openbare IP-adressen hebben een nominale vergoeding. Lees de pagina IP-adresprijzen voor meer informatie over de prijzen van [IP-adres.](https://azure.microsoft.com/pricing/details/ip-addresses) Er is een limiet aan het aantal openbare IP-adressen dat in een abonnement kan worden gebruikt. Lees voor meer informatie over de limieten het artikel [Azure-limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

Nadat de VM is `az network nic show --name MyNic1 --resource-group myResourceGroup` gemaakt, voert u de opdracht in om de NIC-configuratie weer te geven. Voer `az network nic ip-config list --nic-name MyNic1 --resource-group myResourceGroup --output table` de lijst in met de IP-configuraties die aan de NIC zijn gekoppeld.

Voeg de privé-IP-adressen toe aan het VM-besturingssysteem door de stappen voor uw besturingssysteem in het [gedeelte IP-adressen toevoegen aan een vm-besturingssysteemgedeelte](#os-config) van dit artikel in te vullen.

## <a name="add-ip-addresses-to-a-vm"></a><a name="add"></a>IP-adressen toevoegen aan een virtuele machine

U extra privé- en openbare IP-adressen toevoegen aan een bestaande Azure-netwerkinterface door de volgende stappen uit te voeren. De voorbeelden bouwen voort op het [scenario](#scenario) beschreven in dit artikel.

1. Open een opdrachtshell en voltooi de resterende stappen in deze sectie binnen één sessie. Als Azure CLI nog niet is geïnstalleerd en geconfigureerd, voert u de stappen in het `az-login` [azure CLI-installatieartikel](/cli/azure/install-az-cli2?toc=%2fazure%2fvirtual-network%2ftoc.json) uit en meldt u zich met de opdracht aan bij uw Azure-account.

2. Voer de stappen uit in een van de volgende secties, op basis van uw vereisten:

    **Een privé-IP-adres toevoegen**
    
    Als u een privé-IP-adres aan een NIC wilt toevoegen, moet u een IP-configuratie maken met de opdracht die volgt. Het statische IP-adres moet een ongebruikt adres voor het subnet zijn.

    ```azurecli
    az network nic ip-config create \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --private-ip-address 10.0.0.7 \
    --name IPConfig-4
    ```
    
    Maak zoveel configuraties als u wilt, met behulp van unieke configuratienamen en privé-IP-adressen (voor configuraties met statische IP-adressen).

    **Een openbaar IP-adres toevoegen**
    
    Een openbaar IP-adres wordt toegevoegd door het te koppelen aan een nieuwe IP-configuratie of een bestaande IP-configuratie. Voer de stappen uit in een van de volgende secties, zoals u dat wilt.

    Openbare IP-adressen hebben een nominale vergoeding. Lees de pagina IP-adresprijzen voor meer informatie over de prijzen van [IP-adres.](https://azure.microsoft.com/pricing/details/ip-addresses) Er is een limiet aan het aantal openbare IP-adressen dat in een abonnement kan worden gebruikt. Lees voor meer informatie over de limieten het artikel [Azure-limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

    - **De bron koppelen aan een nieuwe IP-configuratie**
    
        Wanneer u een openbaar IP-adres toevoegt in een nieuwe IP-configuratie, moet u ook een privé-IP-adres toevoegen, omdat alle IP-configuraties een privé-IP-adres moeten hebben. U een bestaande openbare IP-adresbron toevoegen of een nieuwe bron maken. Als u een nieuwe wilt maken, voert u de volgende opdracht in:
    
        ```azurecli
        az network public-ip create \
        --resource-group myResourceGroup \
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3
        ```

        Als u een nieuwe IP-configuratie wilt maken met een statisch privé-IP-adres en de bijbehorende ip-adresbron *myPublicIP3,* voert u de volgende opdracht in:

        ```azurecli
        az network nic ip-config create \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-5 \
        --private-ip-address 10.0.0.8
        --public-ip-address myPublicIP3
        ```

    - **De bron koppelen aan een bestaande IP-configuratie** Een openbare IP-adresbron kan alleen worden gekoppeld aan een IP-configuratie die nog niet is gekoppeld. U bepalen of een IP-configuratie een gekoppeld openbaar IP-adres heeft door de volgende opdracht in te voeren:

        ```azurecli
        az network nic ip-config list \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --query "[?provisioningState=='Succeeded'].{ Name: name, PublicIpAddressId: publicIpAddress.id }" --output table
        ```

        Geretourneerde uitvoer:
    
            Name        PublicIpAddressId
            
            ipconfig1   /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
            IPConfig-2  /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
            IPConfig-3

        Aangezien de kolom **PublicIpAddressId** voor *IpConfig-3* leeg is in de uitvoer, is er momenteel geen openbare IP-adresbron aan gekoppeld. U een bestaande openbare IP-adresbron toevoegen aan IpConfig-3 of de volgende opdracht invoeren om er een te maken:

        ```azurecli
        az network public-ip create \
        --resource-group  myResourceGroup
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3 \
        --allocation-method Static
        ```
    
        Voer de volgende opdracht in om de openbare IP-adresbron te koppelen aan de bestaande IP-configuratie met de naam *IPConfig-3:*
    
        ```azurecli
        az network nic ip-config update \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-3 \
        --public-ip myPublicIP3
        ```

3. Bekijk de privé-IP-adressen en de openbare IP-adresbron-id's die aan de NIC zijn toegewezen door de volgende opdracht in te voeren:

    ```azurecli
    az network nic ip-config list \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --query "[?provisioningState=='Succeeded'].{ Name: name, PrivateIpAddress: privateIpAddress, PrivateIpAllocationMethod: privateIpAllocationMethod, PublicIpAddressId: publicIpAddress.id }" --output table
    ```

    Geretourneerde uitvoer: <br>
    
        Name        PrivateIpAddress    PrivateIpAllocationMethod   PublicIpAddressId
        
        ipconfig1   10.0.0.4            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
        IPConfig-2  10.0.0.5            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
        IPConfig-3  10.0.0.6            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP3
    

4. Voeg de privé-IP-adressen toe die u aan het NIC-besturingssysteem hebt toegevoegd door de instructies in het [gedeelte IP-adressen toevoegen aan een vm-besturingssysteemgedeelte](#os-config) van dit artikel te volgen. Voeg de openbare IP-adressen niet toe aan het besturingssysteem.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
