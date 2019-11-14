---
title: Een volledige Linux-omgeving maken met de klassieke Azure-CLI
description: Maak opslag, een virtuele Linux-machine, een virtueel netwerk en een subnet, een load balancer, een NIC, een openbaar IP-adres en een netwerk beveiligings groep, helemaal vanaf het begin met behulp van de klassieke CLI van Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 4ba4060b-ce95-4747-a735-1d7c68597a1a
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/09/2017
ms.author: cynthn
ms.openlocfilehash: 1ee89ce18600685f3f82bfb49d4d8ecbaf192b04
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036530"
---
# <a name="create-a-complete-linux-environment-with-the-azure-classic-cli"></a>Een volledige Linux-omgeving maken met de klassieke Azure-CLI
In dit artikel maken we een eenvoudig netwerk met een load balancer en een combi natie van Vm's die handig zijn voor ontwikkelings-en eenvoudige computing. De opdracht proces wordt door lopen totdat u twee werk, beveiligde Linux-Vm's hebt waarmee u overal verbinding kunt maken via internet. Vervolgens kunt u overstappen op complexere netwerken en omgevingen.

Op de hoogte van de afhankelijkheids hiërarchie van het Resource Manager-implementatie model beschikt u over de mogelijkheid om te zien hoeveel energie het biedt. Nadat u hebt zien hoe het systeem is gebouwd, kunt u het sneller opnieuw opbouwen met behulp van [Azure Resource Manager sjablonen](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Nadat u hebt gezien hoe de onderdelen van uw omgeving samen passen, is het eenvoudiger om sjablonen te maken om ze te automatiseren.

De omgeving bevat:

* Twee virtuele machines in een beschikbaarheidsset.
* Een load balancer met een regel voor taak verdeling op poort 80.
* NSG-regels (netwerk beveiligings groep) om uw virtuele machine te beschermen tegen ongewenst verkeer.

Als u deze aangepaste omgeving wilt maken, hebt u de nieuwste [Azure Classic cli](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nodig in de Resource Manager-modus (`azure config mode arm`). U hebt ook een hulp programma voor JSON-parsering nodig. In dit voor beeld wordt [JQ](https://stedolan.github.io/jq/)gebruikt.


## <a name="cli-versions-to-complete-the-task"></a>CLI-versies om de taak uit te voeren
U kunt de taak uitvoeren met behulp van een van de volgende CLI-versies:

- [Klassieke Azure-cli](#quick-commands) : onze CLI voor het klassieke en Resource Management-implementatie model (dit artikel)
- [Azure cli](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) : onze CLI van de volgende generatie voor het Resource Management-implementatie model


## <a name="quick-commands"></a>Snelle opdrachten
Als u de taak snel moet uitvoeren, wordt in de volgende sectie de basis opdrachten voor het uploaden van een VM naar Azure beschreven. Meer gedetailleerde informatie en context voor elke stap vindt u in de rest van het document, beginnend [hier](#detailed-walkthrough).

Zorg ervoor dat u [de klassieke Azure-cli](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) hebt aangemeld en gebruikmaakt van de modus Resource Manager:

```azurecli
azure config mode arm
```

Vervang in de volgende voor beelden voorbeeld parameter namen door uw eigen waarden. Voor beelden van parameter namen zijn `myResourceGroup`, `mystorageaccount`en `myVM`.

Maak de resourcegroep. In het volgende voorbeeld wordt een resourcegroep met de naam `myResourceGroup` gemaakt op de locatie `westeurope`:

```azurecli
azure group create -n myResourceGroup -l westeurope
```

Controleer de resource groep met behulp van de JSON-parser:

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Maak het opslag account. In het volgende voor beeld wordt een opslag account gemaakt met de naam `mystorageaccount`. (De naam van het opslag account moet uniek zijn, dus geef uw eigen unieke naam op.)

```azurecli
azure storage account create -g myResourceGroup -l westeurope \
  --kind Storage --sku-name GRS mystorageaccount
```

Controleer het opslag account met behulp van de JSON-parser:

```azurecli
azure storage account show -g myResourceGroup mystorageaccount --json | jq '.'
```

Maak het virtuele netwerk. In het volgende voor beeld wordt een virtueel netwerk gemaakt met de naam `myVnet`:

```azurecli
azure network vnet create -g myResourceGroup -l westeurope\
  -n myVnet -a 192.168.0.0/16
```

Maak een subnet. In het volgende voor beeld wordt een subnet met de naam `mySubnet`gemaakt:

```azurecli
azure network vnet subnet create -g myResourceGroup \
  -e myVnet -n mySubnet -a 192.168.1.0/24
```

Controleer het virtuele netwerk en het subnet met behulp van de JSON-parser:

```azurecli
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Maak een openbaar IP-adres. In het volgende voor beeld wordt een openbaar IP-adres met de naam `myPublicIP` met de DNS-naam van `mypublicdns`gemaakt. (De DNS-naam moet uniek zijn, dus geef uw eigen unieke naam op.)

```azurecli
azure network public-ip create -g myResourceGroup -l westeurope \
  -n myPublicIP  -d mypublicdns -a static -i 4
```

Maak de load balancer. In het volgende voor beeld wordt een load balancer met de naam `myLoadBalancer`gemaakt:

```azurecli
azure network lb create -g myResourceGroup -l westeurope -n myLoadBalancer
```

Maak een front-end-IP-adres groep voor de load balancer en koppel het open bare IP-adres. In het volgende voor beeld wordt een front-end-IP-groep met de naam `mySubnetPool`gemaakt:

```azurecli
azure network lb frontend-ip create -g myResourceGroup -l myLoadBalancer \
  -i myPublicIP -n myFrontEndPool
```

Maak de back-end-IP-pool voor de load balancer. In het volgende voor beeld wordt een back-end-IP-groep met de naam `myBackEndPool`gemaakt:

```azurecli
azure network lb address-pool create -g myResourceGroup -l myLoadBalancer \
  -n myBackEndPool
```

Maak regels voor inkomend SSH-Network Address Translation (NAT) voor de load balancer. In het volgende voor beeld worden twee load balancer-regels gemaakt, `myLoadBalancerRuleSSH1` en `myLoadBalancerRuleSSH2`:

```azurecli
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH1 -p tcp -f 4222 -b 22
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH2 -p tcp -f 4223 -b 22
```

Maak de Web-inkomend NAT-regels voor de load balancer. In het volgende voor beeld wordt een load balancer regel gemaakt met de naam `myLoadBalancerRuleWeb`:

```azurecli
azure network lb rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleWeb -p tcp -f 80 -b 80 \
  -t myFrontEndPool -o myBackEndPool
```

Maak de load balancer Health probe. In het volgende voor beeld wordt een TCP-test met de naam `myHealthProbe`:

```azurecli
azure network lb probe create -g myResourceGroup -l myLoadBalancer \
  -n myHealthProbe -p "tcp" -i 15 -c 4
```

Controleer de load balancer, IP-adres groepen en NAT-regels met behulp van de JSON-parser:

```azurecli
azure network lb show -g myResourceGroup -n myLoadBalancer --json | jq '.'
```

Maak de eerste netwerk interface kaart (NIC). Vervang de `#####-###-###` secties door uw eigen Azure-abonnements-ID. Uw abonnements-ID wordt vermeld in de uitvoer van **JQ** wanneer u de resources bekijkt die u maakt. U kunt ook uw abonnement-ID bekijken met `azure account list`.

In het volgende voor beeld wordt een NIC gemaakt met de naam `myNic1`:

```azurecli
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic1 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
```

Maak de tweede NIC. In het volgende voor beeld wordt een NIC gemaakt met de naam `myNic2`:

```azurecli
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic2 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
```

Controleer de twee Nic's met behulp van de JSON-parser:

```azurecli
azure network nic show myResourceGroup myNic1 --json | jq '.'
azure network nic show myResourceGroup myNic2 --json | jq '.'
```

Maak de netwerk beveiligings groep. In het volgende voor beeld wordt een netwerk beveiligings groep gemaakt met de naam `myNetworkSecurityGroup`:

```azurecli
azure network nsg create -g myResourceGroup -l westeurope \
  -n myNetworkSecurityGroup
```

Voeg twee regels voor binnenkomende verbindingen toe voor de netwerk beveiligings groep. In het volgende voor beeld worden twee regels gemaakt `myNetworkSecurityGroupRuleSSH` en `myNetworkSecurityGroupRuleHTTP`:

```azurecli
azure network nsg rule create -p tcp -r inbound -y 1000 -u 22 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleSSH
azure network nsg rule create -p tcp -r inbound -y 1001 -u 80 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleHTTP
```

Controleer de netwerk beveiligings groep en de regels voor binnenkomende verbindingen met behulp van de JSON-parser:

```azurecli
azure network nsg show -g myResourceGroup -n myNetworkSecurityGroup --json | jq '.'
```

De netwerk beveiligings groep koppelen aan de twee Nic's:

```azurecli
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic1
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic2
```

De beschikbaarheidsset maken. In het volgende voor beeld wordt een beschikbaarheidsset gemaakt met de naam `myAvailabilitySet`:

```azurecli
azure availset create -g myResourceGroup -l westeurope -n myAvailabilitySet
```

Maak de eerste virtuele Linux-machine. In het volgende voor beeld wordt een VM gemaakt met de naam `myVM1`:

```azurecli
azure vm create \
    --resource-group myResourceGroup \
    --name myVM1 \
    --location westeurope \
    --os-type linux \
    --availset-name myAvailabilitySet \
    --nic-name myNic1 \
    --vnet-name myVnet \
    --vnet-subnet-name mySubnet \
    --storage-account-name mystorageaccount \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

Maak de tweede virtuele Linux-machine. In het volgende voor beeld wordt een VM gemaakt met de naam `myVM2`:

```azurecli
azure vm create \
    --resource-group myResourceGroup \
    --name myVM2 \
    --location westeurope \
    --os-type linux \
    --availset-name myAvailabilitySet \
    --nic-name myNic2 \
    --vnet-name myVnet \
    --vnet-subnet-name mySubnet \
    --storage-account-name mystorageaccount \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

Gebruik de JSON-parser om te controleren of alles dat is gebouwd:

```azurecli
azure vm show -g myResourceGroup -n myVM1 --json | jq '.'
azure vm show -g myResourceGroup -n myVM2 --json | jq '.'
```

Uw nieuwe omgeving exporteren naar een sjabloon om een nieuwe instantie snel opnieuw te maken:

```azurecli
azure group export myResourceGroup
```

## <a name="detailed-walkthrough"></a>Gedetailleerd overzicht
In de gedetailleerde stappen die volgen, wordt uitgelegd wat elke opdracht doet wanneer u uw omgeving bouwt. Deze concepten zijn handig wanneer u uw eigen aangepaste omgevingen bouwt voor ontwikkeling of productie.

Zorg ervoor dat u [de klassieke Azure-cli](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) hebt aangemeld en gebruikmaakt van de modus Resource Manager:

```azurecli
azure config mode arm
```

Vervang in de volgende voor beelden voorbeeld parameter namen door uw eigen waarden. Voor beelden van parameter namen zijn `myResourceGroup`, `mystorageaccount`en `myVM`.

## <a name="create-resource-groups-and-choose-deployment-locations"></a>Resource groepen maken en implementatie locaties kiezen
Azure-resource groepen zijn logische implementatie-entiteiten die configuratie-informatie en meta gegevens bevatten om het logische beheer van resource-implementaties mogelijk te maken. In het volgende voorbeeld wordt een resourcegroep met de naam `myResourceGroup` gemaakt op de locatie `westeurope`:

```azurecli
azure group create --name myResourceGroup --location westeurope
```

Uitvoer:

```azurecli                        
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
data:    Id:                  /subscriptions/guid/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westeurope
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

## <a name="create-a-storage-account"></a>Een opslagaccount maken
U hebt opslag accounts nodig voor uw VM-schijven en voor alle extra gegevens schijven die u wilt toevoegen. U maakt bijna direct na het maken van resource groepen een opslag account.

Hier gebruiken we de `azure storage account create`-opdracht, waarbij u de locatie van het account, de resource groep waarmee deze bepaalt en het type opslag ondersteuning dat u wilt. In het volgende voor beeld wordt een opslag account gemaakt met de naam `mystorageaccount`:

```azurecli
azure storage account create \  
  --location westeurope \
  --resource-group myResourceGroup \
  --kind Storage --sku-name GRS \
  mystorageaccount
```

Uitvoer:

```azurecli
info:    Executing command storage account create
+ Creating storage account
info:    storage account create command OK
```

Als u de resource groep wilt onderzoeken met behulp van de `azure group show` opdracht, gaan we het [JQ](https://stedolan.github.io/jq/) -hulp programma gebruiken samen met de optie `--json` Azure cli. (U kunt **jsawk** of een wille keurige taal bibliotheek gebruiken om de JSON te parseren.)

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Uitvoer:

```json
{
  "tags": {},
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "name": "myResourceGroup",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
      "name": "mystorageaccount",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

Als u het opslag account wilt onderzoeken met behulp van de CLI, moet u eerst de account namen en-sleutels instellen. Vervang de naam van het opslag account in het volgende voor beeld door een naam die u kiest:

```bash
export AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show mystorageaccount --resource-group myResourceGroup --json | jq -r '.string')"
```

Vervolgens kunt u uw opslag gegevens eenvoudig bekijken:

```azurecli
azure storage container list
```

Uitvoer:

```azurecli
info:    Executing command storage container list
+ Getting storage containers
data:    Name  Public-Access  Last-Modified
data:    ----  -------------  -----------------------------
data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
info:    storage container list command OK
```

## <a name="create-a-virtual-network-and-subnet"></a>Een virtueel netwerk en een subnet maken
Nu gaat u een virtueel netwerk maken dat wordt uitgevoerd in Azure en een subnet waarin u uw virtuele machines kunt maken. In het volgende voor beeld wordt een virtueel netwerk met de naam `myVnet` gemaakt met het adres voorvoegsel `192.168.0.0/16`:

```azurecli
azure network vnet create --resource-group myResourceGroup --location westeurope \
  --name myVnet --address-prefixes 192.168.0.0/16
```

Uitvoer:

```azurecli
info:    Executing command network vnet create
+ Looking up virtual network "myVnet"
+ Creating virtual network "myVnet"
+ Loading virtual network state
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet
data:    Name                            : myVnet
data:    Type                            : Microsoft.Network/virtualNetworks
data:    Location                        : westeurope
data:    ProvisioningState               : Succeeded
data:    Address prefixes:
data:      192.168.0.0/16
info:    network vnet create command OK
```

We gebruiken opnieuw de--JSON-optie van `azure group show` en `jq` om te zien hoe we onze resources bouwen. We hebben nu een `storageAccounts` resource en een `virtualNetworks` resource.  

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Uitvoer:

```json
{
  "tags": {},
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "name": "myResourceGroup",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
      "name": "myVnet",
      "type": "virtualNetworks",
      "location": "westeurope",
      "tags": null
    },
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
      "name": "mystorageaccount",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

We gaan nu een subnet maken in het `myVnet` virtuele netwerk waarin de Vm's zijn geïmplementeerd. We gebruiken de `azure network vnet subnet create` opdracht, samen met de resources die we al hebben gemaakt: de `myResourceGroup` resource groep en het `myVnet` virtuele netwerk. In het volgende voor beeld voegen we het subnet met de naam `mySubnet` toe met het adres voorvoegsel van het subnet van `192.168.1.0/24`:

```azurecli
azure network vnet subnet create --resource-group myResourceGroup \
  --vnet-name myVnet --name mySubnet --address-prefix 192.168.1.0/24
```

Uitvoer:

```azurecli
info:    Executing command network vnet subnet create
+ Looking up the subnet "mySubnet"
+ Creating subnet "mySubnet"
+ Looking up the subnet "mySubnet"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet
data:    Type                            : Microsoft.Network/virtualNetworks/subnets
data:    ProvisioningState               : Succeeded
data:    Name                            : mySubnet
data:    Address prefix                  : 192.168.1.0/24
data:
info:    network vnet subnet create command OK
```

Omdat het subnet zich logisch in het virtuele netwerk bevindt, zoeken we de subnetgegevens met een iets andere opdracht. De gebruikte opdracht is `azure network vnet show`, maar we blijven de JSON-uitvoer bekijken met behulp van `jq`.

```azurecli
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Uitvoer:

```json
{
  "subnets": [
    {
      "ipConfigurations": [],
      "addressPrefix": "192.168.1.0/24",
      "provisioningState": "Succeeded",
      "name": "mySubnet",
      "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
    }
  ],
  "tags": {},
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "provisioningState": "Succeeded",
  "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "name": "myVnet",
  "location": "westeurope"
}
```

## <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken
We gaan nu het open bare IP-adres (PIP) maken dat we aan uw load balancer toewijzen. Hiermee kunt u via Internet verbinding maken met uw virtuele machines met behulp van de `azure network public-ip create` opdracht. Omdat het standaard adres dynamisch is, maken we een DNS-vermelding met een naam in het domein **cloudapp.Azure.com** met behulp van de optie `--domain-name-label`. In het volgende voor beeld wordt een openbaar IP-adres met de naam `myPublicIP` met de DNS-naam van `mypublicdns`gemaakt. Omdat de DNS-naam uniek moet zijn, geeft u uw eigen unieke DNS-naam op:

```azurecli
azure network public-ip create --resource-group myResourceGroup \
  --location westeurope --name myPublicIP --domain-name-label mypublicdns
```

Uitvoer:

```azurecli
info:    Executing command network public-ip create
+ Looking up the public ip "myPublicIP"
+ Creating public ip address "myPublicIP"
+ Looking up the public ip "myPublicIP"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP
data:    Name                            : myPublicIP
data:    Type                            : Microsoft.Network/publicIPAddresses
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Allocation method               : Dynamic
data:    Idle timeout                    : 4
data:    Domain name label               : mypublicdns
data:    FQDN                            : mypublicdns.westeurope.cloudapp.azure.com
info:    network public-ip create command OK
```

Het open bare IP-adres is ook een resource op het hoogste niveau, zodat u het kunt zien met `azure group show`.

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Uitvoer:

```json
{
"tags": {},
"id": "/subscriptions/guid/resourceGroups/myResourceGroup",
"name": "myResourceGroup",
"provisioningState": "Succeeded",
"location": "westeurope",
"properties": {
    "provisioningState": "Succeeded"
},
"resources": [
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
    "name": "myPublicIP",
    "type": "publicIPAddresses",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
    "name": "myVnet",
    "type": "virtualNetworks",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "name": "mystorageaccount",
    "type": "storageAccounts",
    "location": "westeurope",
    "tags": null
    }
],
"permissions": [
    {
    "actions": [
        "*"
    ],
    "notActions": []
    }
]
}
```

U kunt meer Resource Details onderzoeken, met inbegrip van de Fully Qualified Domain Name (FQDN) van het subdomein met behulp van de `azure network public-ip show` opdracht volt ooien. De resource voor het open bare IP-adres is logisch toegewezen, maar een specifiek adres is nog niet toegewezen. Als u een IP-adres wilt verkrijgen, hebt u een load balancer nodig dat we nog niet hebben gemaakt.

```azurecli
azure network public-ip show myResourceGroup myPublicIP --json | jq '.'
```

Uitvoer:

```json
{
"tags": {},
"publicIpAllocationMethod": "Dynamic",
"dnsSettings": {
    "domainNameLabel": "mypublicdns",
    "fqdn": "mypublicdns.westeurope.cloudapp.azure.com"
},
"idleTimeoutInMinutes": 4,
"provisioningState": "Succeeded",
"etag": "W/\"c63154b3-1130-49b9-a887-877d74d5ebc5\"",
"id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
"name": "myPublicIP",
"location": "westeurope"
}
```

## <a name="create-a-load-balancer-and-ip-pools"></a>Een load balancer en IP-adres groepen maken
Wanneer u een load balancer maakt, kunt u verkeer distribueren over meerdere Vm's. Het biedt ook redundantie voor uw toepassing door meerdere Vm's uit te voeren die reageren op gebruikers aanvragen in het geval van onderhoud of zware belasting. In het volgende voor beeld wordt een load balancer met de naam `myLoadBalancer`gemaakt:

```azurecli
azure network lb create --resource-group myResourceGroup --location westeurope \
  --name myLoadBalancer
```

Uitvoer:

```azurecli
info:    Executing command network lb create
+ Looking up the load balancer "myLoadBalancer"
+ Creating load balancer "myLoadBalancer"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer
data:    Name                            : myLoadBalancer
data:    Type                            : Microsoft.Network/loadBalancers
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
info:    network lb create command OK
```

Onze load balancer is tamelijk leeg, dus we maken een aantal IP-adres groepen. We willen twee IP-adres groepen maken voor onze load balancer, één voor de front-end en één voor de back-end. De front-end-IP-adres groep is openbaar zichtbaar. Het is ook de locatie waarnaar we de PIP toewijzen die we eerder hebben gemaakt. Vervolgens gebruiken we de back-end-pool als locatie voor de virtuele machines waarmee ze verbinding maken. Op die manier kan het verkeer door de load balancer naar de virtuele machines stromen.

Eerst gaan we onze front-end-IP-adres groep maken. In het volgende voor beeld wordt een front-end-groep met de naam `myFrontEndPool`gemaakt:

```azurecli
azure network lb frontend-ip create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --public-ip-name myPublicIP \
  --name myFrontEndPool
```

Uitvoer:

```azurecli
info:    Executing command network lb frontend-ip create
+ Looking up the load balancer "myLoadBalancer"
+ Looking up the public ip "myPublicIP"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myFrontEndPool
data:    Provisioning state              : Succeeded
data:    Private IP allocation method    : Dynamic
data:    Public IP address id            : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP
info:    network lb mySubnet-ip create command OK
```

Houd er rekening mee dat we de `--public-ip-name` switch hebben gebruikt om door te geven aan de `myPublicIP` die we eerder hebben gemaakt. Als u het open bare IP-adres toewijst aan de load balancer, kunt u uw Vm's via internet bereiken.

We gaan nu onze tweede IP-adres groep maken, deze tijd voor het back-end-verkeer. In het volgende voor beeld wordt een back-end-pool met de naam `myBackEndPool`gemaakt:

```azurecli
azure network lb address-pool create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myBackEndPool
```

Uitvoer:

```azurecli
info:    Executing command network lb address-pool create
+ Looking up the load balancer "myLoadBalancer"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myBackEndPool
data:    Provisioning state              : Succeeded
info:    network lb address-pool create command OK
```

U kunt zien hoe onze load balancer is door te kijken naar `azure network lb show` en de JSON-uitvoer te controleren:

```azurecli
azure network lb show myResourceGroup myLoadBalancer --json | jq '.'
```

Uitvoer:

```json
{
  "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "name": "myLoadBalancer",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "mySubnetIPConfigurations": [
    {
      "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
      "name": "myFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
      "name": "myBackEndPool",
      "provisioningState": "Succeeded",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
    }
  ],
  "loadBalancingRules": [],
  "probes": []
}
```

## <a name="create-load-balancer-nat-rules"></a>load balancer NAT-regels maken
Als u verkeer wilt ontvangen via onze load balancer, moet u Network Address Translation (NAT) regels maken waarmee u inkomende of uitgaande acties kunt opgeven. U kunt het te gebruiken protocol opgeven en vervolgens externe poorten toewijzen aan interne poorten naar wens. Voor onze omgeving gaan we regels maken die SSH via onze load balancer naar onze Vm's toelaten. We stellen TCP-poorten 4222 en 4223 in om te verwijzen naar TCP-poort 22 op onze Vm's (die later worden gemaakt). In het volgende voor beeld wordt een regel met de naam `myLoadBalancerRuleSSH1` om TCP-poort 4222 toe te wijzen aan poort 22:

```azurecli
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH1 \
  --protocol tcp --frontend-port 4222 --backend-port 22
```

Uitvoer:

```azurecli
info:    Executing command network lb inbound-nat-rule create
+ Looking up the load balancer "myLoadBalancer"
warn:    Using default enable floating ip: false
warn:    Using default idle timeout: 4
warn:    Using default mySubnet IP configuration "myFrontEndPool"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myLoadBalancerRuleSSH1
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    mySubnet port                   : 4222
data:    Backend port                    : 22
data:    Enable floating IP              : false
data:    Idle timeout in minutes         : 4
data:    mySubnet IP configuration id    : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool
info:    network lb inbound-nat-rule create command OK
```

Herhaal de procedure voor uw tweede NAT-regel voor SSH. In het volgende voor beeld wordt een regel met de naam `myLoadBalancerRuleSSH2` om TCP-poort 4223 toe te wijzen aan poort 22:

```azurecli
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22
```

Daarnaast gaat u een NAT-regel voor TCP-poort 80 voor webverkeer maken, waarbij u de regel tot onze IP-adres groepen koppelt. Als we de regel aan een IP-adres groep koppelen, kunnen we Vm's toevoegen aan of verwijderen uit de IP-adres groep, in plaats van de regel aan onze Vm's te koppelen. De load balancer past automatisch de stroom van het verkeer aan. In het volgende voor beeld wordt een regel met de naam `myLoadBalancerRuleWeb` om TCP-poort 80 toe te wijzen aan poort 80:

```azurecli
azure network lb rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleWeb --protocol tcp \
  --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEndPool \
  --backend-address-pool-name myBackEndPool
```

Uitvoer:

```azurecli
info:    Executing command network lb rule create
+ Looking up the load balancer "myLoadBalancer"
warn:    Using default idle timeout: 4
warn:    Using default enable floating ip: false
warn:    Using default load distribution: Default
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myLoadBalancerRuleWeb
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    mySubnet port                   : 80
data:    Backend port                    : 80
data:    Enable floating IP              : false
data:    Load distribution               : Default
data:    Idle timeout in minutes         : 4
data:    mySubnet IP configuration id    : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool
data:    Backend address pool id         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool
info:    network lb rule create command OK
```

## <a name="create-a-load-balancer-health-probe"></a>Een load balancer-statustest maken
Een Health probe controleert periodiek op de virtuele machines die zich achter onze load balancer bevinden om ervoor te zorgen dat ze werken en reageren op aanvragen zoals ze zijn gedefinieerd. Als dat niet het geval is, worden ze uit de bewerking verwijderd om ervoor te zorgen dat gebruikers niet worden omgeleid naar hen. U kunt aangepaste controles definiëren voor de status test, samen met intervallen en time-outwaarden. Zie [Load Balancer probe](../../load-balancer/load-balancer-custom-probe-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)(testen) voor meer informatie over status controles. In het volgende voor beeld wordt een TCP-status met de naam `myHealthProbe`gemaakt:

```azurecli
azure network lb probe create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myHealthProbe --protocol "tcp" \
  --interval 15 --count 4
```

Uitvoer:

```azurecli
info:    Executing command network lb probe create
warn:    Using default probe port: 80
+ Looking up the load balancer "myLoadBalancer"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myHealthProbe
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    Port                            : 80
data:    Interval in seconds             : 15
data:    Number of probes                : 4
info:    network lb probe create command OK
```

Hier hebben we een interval van 15 seconden opgegeven voor de status controles. We kunnen Maxi maal vier tests (één minuut) missen voordat de load balancer van mening is dat de host niet meer werkt.

## <a name="verify-the-load-balancer"></a>Controleer de load balancer
De configuratie van load balancer is nu voltooid. Dit zijn de stappen die u hebt genomen:

1. U hebt een load balancer gemaakt.
2. U hebt een front-end-IP-adres groep gemaakt en hieraan een openbaar IP-adres toegewezen.
3. U hebt een back-end-IP-pool gemaakt waarmee virtuele machines verbinding kunnen maken.
4. U hebt NAT-regels gemaakt waarmee SSH kan worden toegevoegd aan de Vm's voor beheer, samen met een regel waarmee TCP-poort 80 voor de web-app wordt toegestaan.
5. U hebt een status test toegevoegd om regel matig de Vm's te controleren. Deze Health probe zorgt ervoor dat gebruikers geen toegang proberen te krijgen tot een virtuele machine die niet meer werkt of inhoud levert.

Laten we eens kijken wat uw load balancer nu ziet:

```azurecli
azure network lb show --resource-group myResourceGroup \
  --name myLoadBalancer --json | jq '.'
```

Uitvoer:

```json
{
  "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleSSH1",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "protocol": "Tcp",
      "mySubnetPort": 4222,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    },
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleSSH2",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "protocol": "Tcp",
      "mySubnetPort": 4223,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    }
  ],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "name": "myLoadBalancer",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "mySubnetIPConfigurations": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb"
        }
      ],
      "inboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
        },
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myBackEndPool",
      "provisioningState": "Succeeded",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
    }
  ],
  "loadBalancingRules": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleWeb",
      "provisioningState": "Succeeded",
      "enableFloatingIP": false,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "backendAddressPool": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
      },
      "protocol": "Tcp",
      "loadDistribution": "Default",
      "mySubnetPort": 80,
      "backendPort": 80,
      "idleTimeoutInMinutes": 4
    }
  ],
  "probes": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myHealthProbe",
      "provisioningState": "Succeeded",
      "numberOfProbes": 4,
      "intervalInSeconds": 15,
      "port": 80,
      "protocol": "Tcp",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/probes/myHealthProbe"
    }
  ]
}
```

## <a name="create-an-nic-to-use-with-the-linux-vm"></a>Een NIC maken voor gebruik met de virtuele Linux-machine
Nic's zijn programmatisch beschikbaar omdat u regels kunt Toep assen op hun gebruik. U kunt ook meer dan één. In de volgende `azure network nic create` opdracht koppelt u de NIC aan de IP-adres groep voor het laden van de back-end en koppelt u deze aan de NAT-regel om SSH-verkeer toe te staan.

Vervang de `#####-###-###` secties door uw eigen Azure-abonnements-ID. Uw abonnements-ID wordt vermeld in de uitvoer van `jq` wanneer u de resources bekijkt die u maakt. U kunt ook uw abonnement-ID bekijken met `azure account list`.

In het volgende voor beeld wordt een NIC gemaakt met de naam `myNic1`:

```azurecli
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic1 \
  --lb-address-pool-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1
```

Uitvoer:

```azurecli
info:    Executing command network nic create
+ Looking up the subnet "mySubnet"
+ Looking up the network interface "myNic1"
+ Creating network interface "myNic1"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1
data:    Name                            : myNic1
data:    Type                            : Microsoft.Network/networkInterfaces
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Enable IP forwarding            : false
data:    IP configurations:
data:      Name                          : Nic-IP-config
data:      Provisioning state            : Succeeded
data:      Private IP address            : 192.168.1.4
data:      Private IP allocation method  : Dynamic
data:      Subnet                        : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet
data:      Load balancer backend address pools:
data:        Id                          : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool
data:      Load balancer inbound NAT rules:
data:        Id                          : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1
data:
info:    network nic create command OK
```

U kunt de details weer geven door de resource rechtstreeks te controleren. U onderzoekt de resource met behulp van de `azure network nic show` opdracht:

```azurecli
azure network nic show myResourceGroup myNic1 --json | jq '.'
```

Uitvoer:

```json
{
  "etag": "W/\"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a\"",
  "provisioningState": "Succeeded",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1",
  "name": "myNic1",
  "type": "Microsoft.Network/networkInterfaces",
  "location": "westeurope",
  "ipConfigurations": [
    {
      "etag": "W/\"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1/ipConfigurations/Nic-IP-config",
      "loadBalancerBackendAddressPools": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
        }
      ],
      "loadBalancerInboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
        }
      ],
      "privateIPAddress": "192.168.1.4",
      "privateIPAllocationMethod": "Dynamic",
      "subnet": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
      },
      "provisioningState": "Succeeded",
      "name": "Nic-IP-config"
    }
  ],
  "dnsSettings": {
    "appliedDnsServers": [],
    "dnsServers": []
  },
  "enableIPForwarding": false,
  "resourceGuid": "a20258b8-6361-45f6-b1b4-27ffed28798c"
}
```

Nu gaan we de tweede NIC maken, waarbij u weer aan de back-end-IP-adres groep koppelt. Deze keer dat de tweede NAT-regel SSH-verkeer toestaat. In het volgende voor beeld wordt een NIC gemaakt met de naam `myNic2`:

```azurecli
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic2 \
  --lb-address-pool-ids  /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2
```

## <a name="create-a-network-security-group-and-rules"></a>Een netwerk beveiligings groep en-regels maken
Nu maken we een netwerk beveiligings groep en de regels voor binnenkomende verbindingen die de toegang tot de NIC regelen. Een netwerk beveiligings groep kan worden toegepast op een NIC of subnet. U definieert regels voor het beheren van de stroom van verkeer in en uit uw Vm's. In het volgende voor beeld wordt een netwerk beveiligings groep gemaakt met de naam `myNetworkSecurityGroup`:

```azurecli
azure network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

We gaan de binnenkomende regel voor de NSG toevoegen om binnenkomende verbindingen op poort 22 toe te staan (ter ondersteuning van SSH). In het volgende voor beeld wordt een regel gemaakt met de naam `myNetworkSecurityGroupRuleSSH` om TCP toe te staan op poort 22:

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1000 --destination-port-range 22 --access allow \
  --name myNetworkSecurityGroupRuleSSH
```

We gaan nu de binnenkomende regel voor de NSG toevoegen om binnenkomende verbindingen op poort 80 (ter ondersteuning van webverkeer) toe te staan. In het volgende voor beeld wordt een regel gemaakt met de naam `myNetworkSecurityGroupRuleHTTP` om TCP op poort 80 toe te staan:

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1001 --destination-port-range 80 --access allow \
  --name myNetworkSecurityGroupRuleHTTP
```

> [!NOTE]
> De regel voor binnenkomend verkeer is een filter voor inkomende netwerk verbindingen. In dit voor beeld binden we de NSG aan virtuele NIC-Vm's, wat betekent dat elke aanvraag voor poort 22 wordt door gegeven aan de NIC op onze VM. Deze binnenkomende regel is over een netwerk verbinding en niet over een eind punt, wat het in de klassieke implementaties zou zijn. Als u een poort wilt openen, moet u de `--source-port-range` ingesteld op '\*' (de standaard waarde) om inkomende aanvragen van **elke** aanvragende poort te accepteren. Poorten zijn meestal dynamisch.
>
>

## <a name="bind-to-the-nic"></a>Binden aan de NIC
Bind de NSG aan de Nic's. We moeten onze Nic's aansluiten met onze netwerk beveiligings groep. Voer beide opdrachten uit om beide Nic's te koppelen:

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic1 \
  --network-security-group-name myNetworkSecurityGroup
```

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic2 \
  --network-security-group-name myNetworkSecurityGroup
```

## <a name="create-an-availability-set"></a>Een beschikbaarheidsset maken
Met beschikbaarheids sets kunnen uw Vm's worden verdeeld over fout domeinen en upgrade domeinen. Laten we een beschikbaarheidsset maken voor uw virtuele machines. In het volgende voor beeld wordt een beschikbaarheidsset gemaakt met de naam `myAvailabilitySet`:

```azurecli
azure availset create --resource-group myResourceGroup --location westeurope
  --name myAvailabilitySet
```

Fout domeinen definiëren een groepering van virtuele machines die een gemeen schappelijke voedings bron en netwerk switch delen. Standaard worden de virtuele machines die in uw beschikbaarheidsset zijn geconfigureerd, gescheiden in Maxi maal drie fout domeinen. Het idee is dat een hardwareprobleem in een van deze fout domeinen geen invloed heeft op elke virtuele machine waarop uw app wordt uitgevoerd. Azure distribueert automatisch Vm's over de fout domeinen wanneer deze in een beschikbaarheidsset worden geplaatst.

Met upgrade domeinen worden groepen virtuele machines en onderliggende fysieke hardware aangegeven die tegelijk opnieuw kunnen worden opgestart. De volg orde waarin upgrade domeinen opnieuw worden opgestart, is mogelijk niet opeenvolgend tijdens gepland onderhoud, maar er wordt slechts één upgrade tegelijk opnieuw opgestart. Daarnaast distribueert Azure automatisch uw Vm's over upgrade domeinen wanneer deze in een beschikbaarheids site worden geplaatst.

Meer informatie over [het beheren van de beschik baarheid van vm's](manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="create-the-linux-vms"></a>Virtuele Linux-machines maken
U hebt de opslag-en netwerk bronnen gemaakt voor de ondersteuning van virtuele machines die toegankelijk zijn via internet. Nu gaan we deze Vm's maken en deze beveiligen met een SSH-sleutel waarvoor geen wacht woord is opgegeven. In dit geval gaan we een Ubuntu-VM maken op basis van de meest recente LTS. We vinden die afbeeldings informatie met behulp van `azure vm image list`, zoals wordt beschreven in [Azure VM-installatie kopieën zoeken](../windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Er is een installatie kopie geselecteerd met behulp van de opdracht `azure vm image list westeurope canonical | grep LTS`. In dit geval gebruiken we `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`. Voor het laatste veld geven we `latest` door, zodat we in de toekomst altijd de meest recente build krijgen. (De teken reeks die we gebruiken is `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`).

Deze volgende stap is bekend bij iedereen die al een open bare en persoonlijke SSH RSA-sleutel paar in Linux of Mac heeft gemaakt met behulp van **ssh-keygen-t rsa-b 2048**. Als uw `~/.ssh` Directory geen sleutel paren voor certificaten bevat, kunt u deze maken:

* Automatisch, met behulp van de `azure vm create --generate-ssh-keys` optie.
* Hand matig, met behulp van [de instructies om ze zelf te maken](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

U kunt ook de `--admin-password`-methode gebruiken om uw SSH-verbindingen te verifiëren nadat de virtuele machine is gemaakt. Deze methode is doorgaans minder veilig.

We maken de virtuele machine door al onze resources en informatie samen met de `azure vm create`-opdracht te brengen:

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM1 \
  --location westeurope \
  --os-type linux \
  --availset-name myAvailabilitySet \
  --nic-name myNic1 \
  --vnet-name myVnet \
  --vnet-subnet-name mySubnet \
  --storage-account-name mystorageaccount \
  --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username azureuser
```

Uitvoer:

```azurecli
info:    Executing command vm create
+ Looking up the VM "myVM1"
info:    Verifying the public key SSH file: /home/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account mystorageaccount
+ Looking up the availability set "myAvailabilitySet"
info:    Found an Availability set "myAvailabilitySet"
+ Looking up the NIC "myNic1"
info:    Found an existing NIC "myNic1"
info:    Found an IP configuration with virtual network subnet id "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet" in the NIC "myNic1"
info:    This is an NIC without publicIP configured
info:    The storage URI 'https://mystorageaccount.blob.core.windows.net/' will be used for boot diagnostics settings, and it can be overwritten by the parameter input of '--boot-diagnostics-storage-uri'.
info:    vm create command OK
```

U kunt direct verbinding maken met uw virtuele machine met behulp van uw standaard-SSH-sleutels. Zorg ervoor dat u de juiste poort opgeeft, omdat we de load balancer door geven. (Voor onze eerste VM hebben we de NAT-regel ingesteld voor het door sturen van poort 4222 naar onze VM.)

```bash
ssh ops@mypublicdns.westeurope.cloudapp.azure.com -p 4222
```

Uitvoer:

```bash
The authenticity of host '[mypublicdns.westeurope.cloudapp.azure.com]:4222 ([xx.xx.xx.xx]:4222)' can't be established.
ECDSA key fingerprint is 94:2d:d0:ce:6b:fb:7f:ad:5b:3c:78:93:75:82:12:f9.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[mypublicdns.westeurope.cloudapp.azure.com]:4222,[xx.xx.xx.xx]:4222' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.1 LTS (GNU/Linux 4.4.0-34-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    https://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

ops@myVM1:~$
```

Maak uw tweede VM op dezelfde manier.

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM2 \
  --location westeurope \
  --os-type linux \
  --availset-name myAvailabilitySet \
  --nic-name myNic2 \
  --vnet-name myVnet \
  --vnet-subnet-name mySubnet \
  --storage-account-name mystorageaccount \
  --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username azureuser
```

En u kunt nu de `azure vm show myResourceGroup myVM1` opdracht gebruiken om te onderzoeken wat u hebt gemaakt. Op dit moment voert u uw Ubuntu-Vm's uit achter een load balancer in Azure. u kunt zich alleen aanmelden met uw SSH-sleutel paar (omdat wacht woorden zijn uitgeschakeld). U kunt nginx of httpd installeren, een web-app implementeren en de verkeers stroom via de load balancer naar beide Vm's bekijken.

```azurecli
azure vm show --resource-group myResourceGroup --name myVM1
```

Uitvoer:

```azurecli
info:    Executing command vm show
+ Looking up the VM "TestVM1"
+ Looking up the NIC "myNic1"
data:    Id                              :/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM1
data:    ProvisioningState               :Succeeded
data:    Name                            :myVM1
data:    Location                        :westeurope
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :16.04.0-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clib45a8b650f4428a1-os-1471973896525
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://mystorageaccount.blob.core.windows.net/vhds/clib45a8b650f4428a1-os-1471973896525.vhd
data:
data:    OS Profile:
data:      Computer Name                 :myVM1
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-24-D4-AA
data:          Provisioning State        :Succeeded
data:          Name                      :LmyNic1
data:          Location                  :westeurope
data:
data:    AvailabilitySet:
data:      Id                            :/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/availabilitySets/myAvailabilitySet
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://mystorageaccount.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm show command OK

```


## <a name="export-the-environment-as-a-template"></a>De omgeving als sjabloon exporteren
Nu u deze omgeving hebt gemaakt, wat als u een extra ontwikkel omgeving met dezelfde para meters of een productie omgeving wilt maken die overeenkomt met deze. Resource Manager maakt gebruik van JSON-sjablonen waarmee alle para meters voor uw omgeving worden gedefinieerd. U bouwt volledige omgevingen door te verwijzen naar deze JSON-sjabloon. U kunt [JSON-sjablonen hand matig maken](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) of een bestaande omgeving exporteren om de JSON-sjabloon voor u te maken:

```azurecli
azure group export --name myResourceGroup
```

Met deze opdracht maakt u het `myResourceGroup.json`-bestand in de huidige werkmap. Wanneer u een omgeving maakt op basis van deze sjabloon, wordt u gevraagd om alle resource namen, met inbegrip van de namen voor de load balancer, netwerk interfaces of Vm's. U kunt deze namen invullen in het sjabloon bestand door de para meter `-p` of `--includeParameterDefaultValue` toe te voegen aan de `azure group export`-opdracht die eerder is weer gegeven. Bewerk de JSON-sjabloon om de resource namen op te geven of [Maak een JSON-bestand](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) waarin de resource namen worden opgegeven.

Een omgeving maken op basis van uw sjabloon:

```azurecli
azure group deployment create --resource-group myNewResourceGroup \
  --template-file myResourceGroup.json
```

[Meer informatie over hoe u kunt implementeren vanuit sjablonen](../../resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Meer informatie over het stapsgewijs bijwerken van omgevingen, het gebruiken van het parameter bestand en het openen van sjablonen vanaf één opslag locatie.

## <a name="next-steps"></a>Volgende stappen
Nu bent u klaar om te gaan werken met meerdere netwerk onderdelen en Vm's. U kunt deze voorbeeld omgeving gebruiken om uw toepassing samen te stellen met behulp van de kern onderdelen die hier worden geïntroduceerd.
