---
title: Een Linux-vm maken in Azure met meerdere NIC's
description: Meer informatie over het maken van een Linux-vm met meerdere NIC's die eraan zijn gekoppeld met de Azure CLI- of Resource Manager-sjablonen.
author: cynthn
ms.service: virtual-machines-linux
ms.subservice: networking
ms.topic: article
ms.workload: infrastructure
ms.date: 06/07/2018
ms.author: cynthn
ms.openlocfilehash: ecbff4beadd9d10a8489c89cc322c0bb67ec5f40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267181"
---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>Een virtuele Linux-machine maken in Azure met meerdere netwerkinterfacekaarten


In dit artikel wordt beschreven hoe u een VM maakt met meerdere NIC's met de Azure CLI.

## <a name="create-supporting-resources"></a>Ondersteunende bronnen maken
Installeer de nieuwste [Azure CLI](/cli/azure/install-az-cli2) en meld u aan bij een Azure-account met [az-aanmelding.](/cli/azure/reference-index)

Vervang in de volgende voorbeelden voorbeeldparameternamen door uw eigen waarden. Voorbeelden van parameternamen waren *myResourceGroup,* *mystorageaccount*en *myVM*.

Maak eerst een resourcegroep met [az group create](/cli/azure/group). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* op de *locatie Eastus geaald:*

```azurecli
az group create --name myResourceGroup --location eastus
```

Maak het virtuele netwerk met [az-netwerk vnet maken](/cli/azure/network/vnet). In het volgende voorbeeld wordt een virtueel netwerk met de naam *myVnet* en subnet met de naam *mySubnetFrontEnd:*

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 10.0.1.0/24
```

Maak een subnet voor het back-endverkeer met [het az-netwerk vnet subnet maken](/cli/azure/network/vnet/subnet). In het volgende voorbeeld wordt een subnet met de naam *mySubnetBackEnd gemaakt:*

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 10.0.2.0/24
```

Maak een netwerkbeveiligingsgroep met [het AZ-netwerk nsg maken](/cli/azure/network/nsg). In het volgende voorbeeld wordt een netwerkbeveiligingsgroep met de naam *myNetworkSecurityGroup* gemaakt:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="create-and-configure-multiple-nics"></a>Meerdere NIC's maken en configureren
Maak twee NIC's met [az-netwerk nic create](/cli/azure/network/nic). In het volgende voorbeeld worden twee NIC's gemaakt, *myNic1* en *myNic2,* die de netwerkbeveiligingsgroep hebben verbonden met één NIC die verbinding maakt met elk subnet:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic1 \
    --vnet-name myVnet \
    --subnet mySubnetFrontEnd \
    --network-security-group myNetworkSecurityGroup
az network nic create \
    --resource-group myResourceGroup \
    --name myNic2 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>Een VM maken en de NIC's koppelen
Wanneer u de VM maakt, geeft `--nics`u de NIC's op waarmee u hebt gemaakt . U moet ook voorzichtig zijn wanneer u de VM-grootte selecteert. Er zijn limieten voor het totale aantal NIC's dat u aan een vm toevoegen. Lees meer over [Linux VM maten](sizes.md).

Maak een VM met [az vm create](/cli/azure/vm). In het volgende voorbeeld wordt een VM met de naam *myVM gemaakt:*

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS3_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic1 myNic2
```

Voeg routeringstabellen toe aan het besturingssysteem van de gast door de stappen in [Het gastbesturingssysteem configureren voor meerdere NIC's te](#configure-guest-os-for-multiple-nics)voltooien.

## <a name="add-a-nic-to-a-vm"></a>Een NIC toevoegen aan een virtuele machine
In de vorige stappen is een VM gemaakt met meerdere NIC's. U ook NIC's toevoegen aan een bestaande VM met de Azure CLI. Verschillende [VM-formaten](sizes.md) ondersteunen een wisselend aantal NIC's, zodat de grootte van uw VM dienovereenkomstig. Indien nodig u [het formaat van een vm wijzigen.](change-vm-size.md)

Maak een andere NIC met [az-netwerk nic maken](/cli/azure/network/nic). In het volgende voorbeeld wordt een NIC met de naam *myNic3* gemaakt die is verbonden met de back-endsubnet- en netwerkbeveiligingsgroep die in de vorige stappen is gemaakt:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic3 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

Als u een NIC wilt toevoegen aan een bestaande VM, dekent u eerst de VM af met [de toewijzing van az vm](/cli/azure/vm). In het volgende voorbeeld wordt de VM met de naam *myVM toegewezen:*


```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Voeg de NIC toe met [az vm nic add](/cli/azure/vm/nic). In het volgende voorbeeld wordt *myNic3* toegevoegd aan *myVM:*

```azurecli
az vm nic add \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Start de VM met [az vm start:](/cli/azure/vm)

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

Voeg routeringstabellen toe aan het besturingssysteem van de gast door de stappen in [Het gastbesturingssysteem configureren voor meerdere NIC's te](#configure-guest-os-for-multiple-nics)voltooien.

## <a name="remove-a-nic-from-a-vm"></a>Een NIC uit een virtuele machine verwijderen
Als u een NIC uit een bestaande vm wilt verwijderen, dekent u eerst de VM af met [de toewijzing van az vm](/cli/azure/vm). In het volgende voorbeeld wordt de VM met de naam *myVM toegewezen:*

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Verwijder de NIC met [az vm nic verwijderen](/cli/azure/vm/nic). In het volgende voorbeeld wordt *myNic3* van *myVM verwijderd:*

```azurecli
az vm nic remove \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Start de VM met [az vm start:](/cli/azure/vm)

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```


## <a name="create-multiple-nics-using-resource-manager-templates"></a>Meerdere NIC's maken met behulp van Resource Manager-sjablonen
Azure Resource Manager-sjablonen gebruiken declaratieve JSON-bestanden om uw omgeving te definiëren. U een [overzicht van Azure Resource Manager](../../azure-resource-manager/management/overview.md)lezen. Resourcebeheersjablonen bieden een manier om meerdere exemplaren van een resource te maken tijdens de implementatie, zoals het maken van meerdere NIC's. U gebruikt *kopiëren* om het aantal exemplaren op te geven dat u wilt maken:

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Lees meer over [het maken van meerdere exemplaren met *kopiëren*](../../resource-group-create-multiple.md). 

U ook `copyIndex()` een gebruiken om vervolgens een nummer toe te `myNic1` `myNic2`voegken aan een resourcenaam, waarmee u maken, enz. Het volgende toont een voorbeeld van het toevoegen van de indexwaarde:

```json
"name": "[concat('myNic', copyIndex())]", 
```

U een volledig voorbeeld lezen van het maken van [meerdere NIC's met behulp van Resource Manager-sjablonen.](../../virtual-network/template-samples.md)

Voeg routeringstabellen toe aan het besturingssysteem van de gast door de stappen in [Het gastbesturingssysteem configureren voor meerdere NIC's te](#configure-guest-os-for-multiple-nics)voltooien.

## <a name="configure-guest-os-for-multiple-nics"></a>Gastbesturingssysteem configureren voor meerdere NIC's

In de vorige stappen is een virtueel netwerk en subnet gemaakt, gekoppelde NIC's en vervolgens een VM gemaakt. Er zijn geen regels voor openbare IP-adressen en netwerkbeveiligingsgroepen gemaakt waarmee SSH-verkeer kan worden gemaakt. Als u het gastbesturingssysteem voor meerdere NIC's wilt configureren, moet u externe verbindingen toestaan en opdrachten lokaal uitvoeren op de VM.

Als u SSH-verkeer wilt toestaan, maakt u als volgt een regel voor netwerkbeveiliging met [nsg-regel van het AZ-netwerk:](/cli/azure/network/nsg/rule#az-network-nsg-rule-create)

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name allow_ssh \
    --priority 101 \
    --destination-port-ranges 22
```

Maak een openbaar [IP-adres met az-netwerk public-ip maken](/cli/azure/network/public-ip#az-network-public-ip-create) en toewijzen aan de eerste NIC met [az-netwerk nic ip-config update:](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-update)

```azurecli
az network public-ip create --resource-group myResourceGroup --name myPublicIP

az network nic ip-config update \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --name ipconfig1 \
    --public-ip myPublicIP
```

Als u het openbare IP-adres van de VM wilt bekijken, gebruikt u [de AZ VM-weergave](/cli/azure/vm#az-vm-show) als volgt::

```azurecli
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Nu SSH naar het openbare IP-adres van uw VM. De standaardgebruikersnaam in een vorige stap was *azureuser*. Geef uw eigen gebruikersnaam en openbaar IP-adres op:

```bash
ssh azureuser@137.117.58.232
```

Als u naar of vanuit een secundaire netwerkinterface wilt verzenden, moet u handmatig permanente routes toevoegen aan het besturingssysteem voor elke secundaire netwerkinterface. In dit artikel is *eth1* de secundaire interface. Instructies voor het toevoegen van permanente routes aan het besturingssysteem variëren per distro. Zie documentatie voor uw distro voor instructies.

Bij het toevoegen van de route aan het besturingssysteem is het gatewayadres *.1* voor welk subnet de netwerkinterface zich bevindt. Als de netwerkinterface bijvoorbeeld het adres *10.0.2.4*wordt toegewezen, is de gateway die u opgeeft voor de route *10.0.2.1*. U een specifiek netwerk definiëren voor de bestemming van de route of een bestemming van *0.0.0.0*opgeven als u wilt dat al het verkeer voor de interface door de opgegeven gateway gaat. De gateway voor elk subnet wordt beheerd door het virtuele netwerk.

Zodra u de route voor een secundaire interface hebt toegevoegd, `route -n`controleert u of de route zich in de routetabel bevindt met . Het volgende voorbeeld uitvoer is voor de routetabel waarin de twee netwerkinterfaces in dit artikel aan de VM zijn toegevoegd:

```bash
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         10.0.1.1        0.0.0.0         UG    0      0        0 eth0
0.0.0.0         10.0.2.1        0.0.0.0         UG    0      0        0 eth1
10.0.1.0        0.0.0.0         255.255.255.0   U     0      0        0 eth0
10.0.2.0        0.0.0.0         255.255.255.0   U     0      0        0 eth1
168.63.129.16   10.0.1.1        255.255.255.255 UGH   0      0        0 eth0
169.254.169.254 10.0.1.1        255.255.255.255 UGH   0      0        0 eth0
```

Controleer of de route die u hebt toegevoegd, blijft bestaan bij opnieuw opstarten door de routetabel na een herstart opnieuw te controleren. Als u de connectiviteit wilt testen, u bijvoorbeeld de volgende opdracht invoeren, waarbij *eth1* de naam is van een secundaire netwerkinterface:

```bash
ping bing.com -c 4 -I eth1
```

## <a name="next-steps"></a>Volgende stappen
Bekijk [de Linux VM-formaten](sizes.md) wanneer u een VM met meerdere NIC's probeert te maken. Besteed aandacht aan het maximum aantal NIC's dat elke VM-grootte ondersteunt.

Gebruik net op tijd VM-toegang om uw VM's verder te beveiligen. Deze functie opent netwerkbeveiligingsgroepregels voor SSH-verkeer wanneer dat nodig is en voor een bepaalde periode. Zie [Manage virtual machine access using just in time](../../security-center/security-center-just-in-time.md) (VM-toegang beheren met behulp van JIT) voor meer informatie.
