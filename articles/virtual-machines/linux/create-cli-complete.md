---
title: Een Linux-omgeving maken met de Azure CLI
description: Maak opslag, een Linux-VM, een virtueel netwerk en subnet, een load balancer, een NIC, een openbare IP en een netwerkbeveiligingsgroep, allemaal vanaf de grond af aan met behulp van de Azure CLI.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 12/14/2017
ms.author: cynthn
ms.openlocfilehash: 7ee4674f5e7c04709256459c3417a1379a65aedc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969566"
---
# <a name="create-a-complete-linux-virtual-machine-with-the-azure-cli"></a>Maak een complete Linux virtuele machine met de Azure CLI
Als u snel een virtuele machine (VM) in Azure wilt maken, u één Azure CLI-opdracht gebruiken die standaardwaarden gebruikt om de vereiste ondersteunende resources te maken. Bronnen zoals een virtueel netwerk, openbaar IP-adres en regels voor netwerkbeveiligingsgroepen worden automatisch gemaakt. Voor meer controle over uw omgeving in het productiegebruik u deze resources van tevoren maken en vervolgens uw VM's eraan toevoegen. In dit artikel u één voor één een VM en elk van de ondersteunende resources maken.

Zorg ervoor dat u de nieuwste [Azure CLI](/cli/azure/install-az-cli2) hebt geïnstalleerd en ingelogd bent op een Azure-account met [az-aanmelding.](/cli/azure/reference-index)

Vervang in de volgende voorbeelden voorbeeldparameternamen door uw eigen waarden. Voorbeelden van parameternamen zijn *myResourceGroup,* *myVnet*en *myVM*.

## <a name="create-resource-group"></a>Een resourcegroep maken
Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Er moet een resourcegroep worden gemaakt voor een virtuele machine en virtuele netwerkbronnen ondersteunen. Maak de resourcegroep met [de AZ-groep maken](/cli/azure/group). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* op de *locatie Eastus geaald:*

```azurecli
az group create --name myResourceGroup --location eastus
```

Standaard is de uitvoer van Azure CLI-opdrachten in JSON (JavaScript Object Notation). Als u de standaarduitvoer wilt wijzigen in een lijst of tabel, gebruikt u bijvoorbeeld [az configureren --uitvoer](/cli/azure/reference-index). U `--output` ook aan elke opdracht toevoegen voor een eenmalige wijziging in de uitvoerindeling. In het volgende voorbeeld wordt `az group create` de JSON-uitvoer van de opdracht weergegeven:

```json                       
{
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "location": "eastus",
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-a-virtual-network-and-subnet"></a>Een virtueel netwerk en een subnet maken
Vervolgens maakt u een virtueel netwerk in Azure en een subnet waarin u uw VM's maken. Gebruik [vnet create van az-netwerk](/cli/azure/network/vnet) om een virtueel netwerk met de naam *myVnet* te maken met het adresvoorvoegsel *192.168.0.0/16.* U voegt ook een subnet met de naam *mySubnet* toe met het adresvoorvoegsel van *192.168.1.0/24:*

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

De uitvoer geeft aan dat het subnet logisch is gemaakt binnen het virtuele netwerk:

```json
{
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "location": "eastus",
  "name": "myVnet",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "ed62fd03-e9de-430b-84df-8a3b87cacdbb",
  "subnets": [
    {
      "addressPrefix": "192.168.1.0/24",
      "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
      "ipConfigurations": null,
      "name": "mySubnet",
      "networkSecurityGroup": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "resourceNavigationLinks": null,
      "routeTable": null
    }
  ],
  "tags": {},
  "type": "Microsoft.Network/virtualNetworks",
  "virtualNetworkPeerings": null
}
```


## <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken
Laten we nu een openbaar IP-adres maken met [het AZ-netwerk public-ip create.](/cli/azure/network/public-ip) Met dit openbare IP-adres u verbinding maken met uw VM's via internet. Omdat het standaardadres dynamisch is, maakt `--domain-name-label` u een benoemde DNS-vermelding met de parameter. In het volgende voorbeeld wordt een openbaar IP met de naam *myPublicIP* gemaakt met de DNS-naam van *mypublicdns*. Omdat de DNS-naam uniek moet zijn, geeft u uw eigen unieke DNS-naam op:

```azurecli
az network public-ip create \
    --resource-group myResourceGroup \
    --name myPublicIP \
    --dns-name mypublicdns
```

Uitvoer:

```json
{
  "publicIp": {
    "dnsSettings": {
      "domainNameLabel": "mypublicdns",
      "fqdn": "mypublicdns.eastus.cloudapp.azure.com",
      "reverseFqdn": null
    },
    "etag": "W/\"2632aa72-3d2d-4529-b38e-b622b4202925\"",
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": null,
    "ipConfiguration": null,
    "location": "eastus",
    "name": "myPublicIP",
    "provisioningState": "Succeeded",
    "publicIpAddressVersion": "IPv4",
    "publicIpAllocationMethod": "Dynamic",
    "resourceGroup": "myResourceGroup",
    "resourceGuid": "4c65de38-71f5-4684-be10-75e605b3e41f",
    "tags": null,
    "type": "Microsoft.Network/publicIPAddresses"
  }
}
```


## <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken
Als u de verkeersstroom in en uit uw VM's wilt beheren, past u een netwerkbeveiligingsgroep toe op een virtueel NIC of subnet. In het volgende voorbeeld wordt gebruik gemaakt van [az-netwerk nsg maken om](/cli/azure/network/nsg) een netwerkbeveiligingsgroep genaamd *myNetworkSecurityGroup*te maken:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

U definieert regels die specifiek verkeer toestaan of weigeren. Als u inkomende verbindingen op poort 22 wilt toestaan (om SSH-toegang in te schakelen), maakt u een inkomende regel met [nsg-regel van het AZ-netwerk.](/cli/azure/network/nsg/rule) In het volgende voorbeeld wordt een regel met de naam *myNetworkSecurityGroupRuleSSH:*

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleSSH \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 22 \
    --access allow
```

Als u binnenkomende verbindingen op poort 80 (voor webverkeer) wilt toestaan, voegt u een andere regel voor netwerkbeveiligingsgroepen toe. In het volgende voorbeeld wordt een regel met de naam *myNetworkSecurityGroupRuleHTTP:*

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleWeb \
    --protocol tcp \
    --priority 1001 \
    --destination-port-range 80 \
    --access allow
```

Bekijk de netwerkbeveiligingsgroep en de regels met [het AZ-netwerk nsg show:](/cli/azure/network/nsg)

```azurecli
az network nsg show --resource-group myResourceGroup --name myNetworkSecurityGroup
```

Uitvoer:

```json
{
  "defaultSecurityRules": [
    {
      "access": "Allow",
      "description": "Allow inbound traffic from all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetInBound",
      "name": "AllowVnetInBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow inbound traffic from azure load balancer",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowAzureLoadBalancerInBou",
      "name": "AllowAzureLoadBalancerInBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "AzureLoadBalancer",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all inbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllInBound",
      "name": "DenyAllInBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetOutBound",
      "name": "AllowVnetOutBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to Internet",
      "destinationAddressPrefix": "Internet",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowInternetOutBound",
      "name": "AllowInternetOutBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all outbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllOutBound",
      "name": "DenyAllOutBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
  "location": "eastus",
  "name": "myNetworkSecurityGroup",
  "networkInterfaces": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "47a9964e-23a3-438a-a726-8d60ebbb1c3c",
  "securityRules": [
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "22",
      "direction": "Inbound",
      "etag": "W/\"9e344b60-0daa-40a6-84f9-0ebbe4a4b640\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleSSH",
      "name": "myNetworkSecurityGroupRuleSSH",
      "priority": 1000,
      "protocol": "Tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "80",
      "direction": "Inbound",
      "etag": "W/\"9e344b60-0daa-40a6-84f9-0ebbe4a4b640\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleWeb",
      "name": "myNetworkSecurityGroupRuleWeb",
      "priority": 1001,
      "protocol": "Tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "subnets": null,
  "tags": null,
  "type": "Microsoft.Network/networkSecurityGroups"
}
```

## <a name="create-a-virtual-nic"></a>Een virtuele NIC maken
Virtuele netwerkinterfacekaarten (NIC's) zijn programmatisch beschikbaar omdat u regels toepassen op het gebruik ervan. Afhankelijk van de [VM-grootte](sizes.md)u meerdere virtuele NIC's aan een VM koppelen. In de volgende [opdracht az-netwerk nic create](/cli/azure/network/nic) maak je een NIC met de naam *myNic* en koppel je deze aan je netwerkbeveiligingsgroep. Het openbare IP-adres *myPublicIP* is ook gekoppeld aan de virtuele NIC.

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --public-ip-address myPublicIP \
    --network-security-group myNetworkSecurityGroup
```

Uitvoer:

```json
{
  "NewNIC": {
    "dnsSettings": {
      "appliedDnsServers": [],
      "dnsServers": [],
      "internalDnsNameLabel": null,
      "internalDomainNameSuffix": "brqlt10lvoxedgkeuomc4pm5tb.bx.internal.cloudapp.net",
      "internalFqdn": null
    },
    "enableAcceleratedNetworking": false,
    "enableIpForwarding": false,
    "etag": "W/\"04b5ab44-d8f4-422a-9541-e5ae7de8466d\"",
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
    "ipConfigurations": [
      {
        "applicationGatewayBackendAddressPools": null,
        "etag": "W/\"04b5ab44-d8f4-422a-9541-e5ae7de8466d\"",
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic/ipConfigurations/ipconfig1",
        "loadBalancerBackendAddressPools": null,
        "loadBalancerInboundNatRules": null,
        "name": "ipconfig1",
        "primary": true,
        "privateIpAddress": "192.168.1.4",
        "privateIpAddressVersion": "IPv4",
        "privateIpAllocationMethod": "Dynamic",
        "provisioningState": "Succeeded",
        "publicIpAddress": {
          "dnsSettings": null,
          "etag": null,
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
          "idleTimeoutInMinutes": null,
          "ipAddress": null,
          "ipConfiguration": null,
          "location": null,
          "name": null,
          "provisioningState": null,
          "publicIpAddressVersion": null,
          "publicIpAllocationMethod": null,
          "resourceGroup": "myResourceGroup",
          "resourceGuid": null,
          "tags": null,
          "type": null
        },
        "resourceGroup": "myResourceGroup",
        "subnet": {
          "addressPrefix": null,
          "etag": null,
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
          "ipConfigurations": null,
          "name": null,
          "networkSecurityGroup": null,
          "provisioningState": null,
          "resourceGroup": "myResourceGroup",
          "resourceNavigationLinks": null,
          "routeTable": null
        }
      }
    ],
    "location": "eastus",
    "macAddress": null,
    "name": "myNic",
    "networkSecurityGroup": {
      "defaultSecurityRules": null,
      "etag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
      "location": null,
      "name": null,
      "networkInterfaces": null,
      "provisioningState": null,
      "resourceGroup": "myResourceGroup",
      "resourceGuid": null,
      "securityRules": null,
      "subnets": null,
      "tags": null,
      "type": null
    },
    "primary": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "myResourceGroup",
    "resourceGuid": "b3dbaa0e-2cf2-43be-a814-5cc49fea3304",
    "tags": null,
    "type": "Microsoft.Network/networkInterfaces",
    "virtualMachine": null
  }
}
```


## <a name="create-an-availability-set"></a>Een beschikbaarheidsset maken
Beschikbaarheidssets helpen uw VM's te verspreiden over foutdomeinen en bij te werken domeinen. Hoewel u op dit moment slechts één virtuele machine maakt, is het de beste manier om beschikbaarheidssets te gebruiken om het in de toekomst gemakkelijker uit te breiden. 

Foutdomeinen definiëren een groepering van virtuele machines die een gemeenschappelijke stroombron en netwerkswitch delen. Standaard worden de virtuele machines die zijn geconfigureerd binnen uw beschikbaarheidsset gescheiden over maximaal drie foutdomeinen. Een hardwareprobleem in een van deze foutdomeinen heeft geen invloed op elke vm waarop uw app wordt uitgevoerd.

Updatedomeinen geven groepen virtuele machines en onderliggende fysieke hardware aan die tegelijkertijd opnieuw kunnen worden opgestart. Tijdens gepland onderhoud is de volgorde waarin updatedomeinen opnieuw worden opgestart mogelijk niet opeenvolgend, maar wordt slechts één updatedomein tegelijk opnieuw opgestart.

Azure distribueert automatisch VM's over de fout en werkt domeinen bij wanneer deze in een beschikbaarheidsset worden geplaatst. Zie [het beheren van de beschikbaarheid van VM's voor](manage-availability.md)meer informatie.

Maak een beschikbaarheidsset voor uw VM met [de beschikbaarheidsset van AZ VM.](/cli/azure/vm/availability-set) In het volgende voorbeeld wordt een beschikbaarheidsset met de naam *myAvailabilitySet* gemaakt:

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet
```

De domeinen van de uitvoernoteert foutdomeinen en updatedomeinen:

```json
{
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/availabilitySets/myAvailabilitySet",
  "location": "eastus",
  "managed": null,
  "name": "myAvailabilitySet",
  "platformFaultDomainCount": 2,
  "platformUpdateDomainCount": 5,
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": null,
    "managed": true,
    "tier": null
  },
  "statuses": null,
  "tags": {},
  "type": "Microsoft.Compute/availabilitySets",
  "virtualMachines": []
}
```


## <a name="create-a-vm"></a>Een virtuele machine maken
U hebt de netwerkbronnen gemaakt om internetgevoelige VM's te ondersteunen. Maak nu een VM en beveilig deze met een SSH-toets. Laten we in dit voorbeeld een Ubuntu VM maken op basis van de meest recente LTS. U aanvullende afbeeldingen vinden met [de AZ VM-afbeeldingslijst,](/cli/azure/vm/image)zoals beschreven bij [het vinden van Azure VM-afbeeldingen.](cli-ps-findimage.md)

Geef een SSH-sleutel op die u wilt gebruiken voor verificatie. Als u geen SSH-sleutelpaar hebt, u `--generate-ssh-keys` deze [maken](mac-create-ssh-keys.md) of de parameter gebruiken om ze voor u te maken. Als u al een sleutelpaar hebt, `~/.ssh`gebruikt deze parameter bestaande sleutels in .

Maak de VM door alle bronnen en informatie samen te brengen met de opdracht [AZ VM create.](/cli/azure/vm) In het volgende voorbeeld wordt een VM met de naam *myVM gemaakt:*

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location eastus \
    --availability-set myAvailabilitySet \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH naar uw VM met de DNS-vermelding die u hebt opgegeven toen u het openbare IP-adres aanmaakte. Dit `fqdn` wordt weergegeven in de uitvoer terwijl u uw VM maakt:

```json
{
  "fqdns": "mypublicdns.eastus.cloudapp.azure.com",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-13-71-C8",
  "powerState": "VM running",
  "privateIpAddress": "192.168.1.5",
  "publicIpAddress": "13.90.94.252",
  "resourceGroup": "myResourceGroup"
}
```

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Uitvoer:

```bash
The authenticity of host 'mypublicdns.eastus.cloudapp.azure.com (13.90.94.252)' can't be established.
ECDSA key fingerprint is SHA256:SylINP80Um6XRTvWiFaNz+H+1jcrKB1IiNgCDDJRj6A.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'mypublicdns.eastus.cloudapp.azure.com,13.90.94.252' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.11.0-1016-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    https://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.


The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

azureuser@myVM:~$
```

U NGINX installeren en de verkeersstroom naar de VM bekijken. Installeer NGINX als volgt:

```bash
sudo apt-get install -y nginx
```

Als u de standaard NGINX-site in actie wilt zien, opent u uw webbrowser en voert u uw FQDN in:

![Standaard NGINX-site op uw VM](media/create-cli-complete/nginx.png)

## <a name="export-as-a-template"></a>Exporteren als sjabloon
Wat gebeurt er als u nu een extra ontwikkelomgeving wilt maken met dezelfde parameters of een productieomgeving die daarop past? Resource Manager gebruikt JSON-sjablonen die alle parameters voor uw omgeving definiëren. U bouwt hele omgevingen uit door naar deze JSON-sjabloon te verwijzen. U [JSON-sjablonen handmatig bouwen](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) of een bestaande omgeving exporteren om de JSON-sjabloon voor u te maken. Gebruik [de export van az-groepen](/cli/azure/group) om uw resourcegroep als volgt te exporteren:

```azurecli
az group export --name myResourceGroup > myResourceGroup.json
```

Met deze `myResourceGroup.json` opdracht wordt het bestand gemaakt in uw huidige werkmap. Wanneer u een omgeving maakt op basis van deze sjabloon, wordt u gevraagd om alle resourcenamen. U deze namen in uw `--include-parameter-default-value` sjabloonbestand `az group export` invullen door de parameter aan de opdracht toe te voegen. Bewerk uw JSON-sjabloon om de resourcenamen op te geven of [maak een parameters.json-bestand](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) dat de bronnamen opgeeft.

Als u een omgeving wilt maken op basis van uw sjabloon, gebruikt u [de implementatie van AZ-groepen als](/cli/azure/group/deployment) volgt:

```azurecli
az group deployment create \
    --resource-group myNewResourceGroup \
    --template-file myResourceGroup.json
```

Misschien wilt u meer lezen [over het implementeren van sjablonen.](../../resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Meer informatie over het stapsgewijs bijwerken van omgevingen, het gebruik van het parametersbestand en toegang tot sjablonen vanaf één opslaglocatie.

## <a name="next-steps"></a>Volgende stappen
Nu bent u klaar om te beginnen met meerdere netwerkcomponenten en VM's. U deze voorbeeldomgeving gebruiken om uw toepassing uit te bouwen met behulp van de kerncomponenten die hier zijn geïntroduceerd.
