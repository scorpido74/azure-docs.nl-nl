---
title: 'Snelstartgids: een NAT-gateway maken-Azure CLI'
titlesuffix: Azure Virtual Network NAT
description: Deze Quick Start laat zien hoe u een NAT-gateway maakt met behulp van de Azure CLI
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumundD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: 9402960927f56092e226ab81bd3e6ede0cf6a52d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79202192"
---
# <a name="quickstart-create-a-nat-gateway-using-azure-cli"></a>Snelstartgids: een NAT-gateway maken met behulp van Azure CLI

In deze Quick start ziet u hoe u de NAT-service van Azure Virtual Network gebruikt. U maakt een NAT-gateway om uitgaande connectiviteit te bieden voor een virtuele machine in Azure. 


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

U kunt deze zelf studie volt ooien met behulp van Azure Cloud Shell of de betreffende opdrachten lokaal uitvoeren.  Als u Azure Cloud Shell nog nooit hebt gebruikt, [meldt u zich nu](https://shell.azure.com) aan om door te gaan met de eerste installatie.
Als u ervoor kiest om deze opdrachten lokaal uit te voeren, moet u CLI installeren.  Voor deze zelf studie moet u een versie van de Azure CLI-versie 2.0.71 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren]( /cli/azure/install-azure-cli).


## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep maken met [az group create](https://docs.microsoft.com/cli/azure/group). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

In het volgende voor beeld wordt een resource groep met de naam **myResourceGroupNAT** gemaakt op de locatie **eastus2** :

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>De NAT-gateway maken

### <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken

Voor toegang tot het open bare Internet hebt u een of meer open bare IP-adressen nodig voor de NAT-gateway. Gebruik [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip) om een resource met een openbaar IP-adres te maken met de naam **myPublicIP** in **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIP \
    --sku standard
```

### <a name="create-a-public-ip-prefix"></a>Een openbaar IP-voor voegsel maken

U kunt een of meer open bare IP-adres bronnen, open bare IP-voor voegsels of beide met NAT-gateway gebruiken. We voegen een open bare IP-prefix bron toe aan dit scenario om te demonstreren.   Gebruik [AZ Network Public-IP prefix Create](https://docs.microsoft.com/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create) om een open bare IP-adres voorvoegsel resource te maken met de naam **myPublicIPprefix** in **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPprefix \
    --length 31
```

### <a name="create-a-nat-gateway-resource"></a>Een NAT-gateway resource maken

In deze sectie wordt beschreven hoe u de volgende onderdelen van de NAT-service kunt maken en configureren met behulp van de NAT-gateway resource:
  - Een open bare IP-adres groep en een openbaar IP-voor voegsel dat moet worden gebruikt voor uitgaande stromen die worden vertaald door de NAT-gateway resource.
  - Wijzig de time-out voor inactiviteit van de standaard waarde van 4 minuten in 10 minuten.

Maak een globale Azure NAT-gateway met [AZ Network NAT gateway Create](https://docs.microsoft.com/cli/azure/network/nat?view=azure-cli-latest) named **myNATgateway**. De opdracht gebruikt zowel het open bare IP-adres **myPublicIP** als het open bare IP-voor voegsel **myPublicIPprefix**. Met de opdracht wordt de time-out voor inactiviteit gewijzigd in **10** minuten.

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIP \
    --public-ip-prefixes myPublicIPprefix \
    --idle-timeout 10       
  ```

Op dit punt is de NAT-gateway functioneel en ontbreekt er een configuratie van de subnetten van een virtueel netwerk.

## <a name="configure-virtual-network"></a>Virtueel netwerk configureren

Voordat u een virtuele machine implementeert en uw NAT-gateway kunt gebruiken, moet u het virtuele netwerk maken.

Maak een virtueel netwerk met de naam **myVnet** met een subnet met de naam **MySubnet** in de **myResourceGroupNAT** met [AZ Network vnet Create](https://docs.microsoft.com/cli/azure/network/vnet).  De IP-adres ruimte voor het virtuele netwerk is **192.168.0.0/16**. Het subnet in het virtuele netwerk is **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.0.0/24
```

### <a name="configure-nat-service-for-source-subnet"></a>NAT-service voor bron subnet configureren

We configureren het bron- **mySubnet** in **myVnet** van het virtuele netwerk voor het gebruik van een specifieke NAT gateway resource **myNATgateway** met [AZ Network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet).  Met deze opdracht wordt de NAT-service op het opgegeven subnet geactiveerd.

```azurecli-interactive
  az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnet \
    --name mySubnet \
    --nat-gateway myNATgateway
```

Al het uitgaande verkeer naar Internet bestemmingen maakt nu gebruik van de NAT-gateway.  Het is niet nodig om een UDR te configureren.

## <a name="create-a-vm-to-use-the-nat-service"></a>Een virtuele machine maken voor het gebruik van de NAT-service

We gaan nu een VM maken voor het gebruik van de NAT-service.  Deze VM heeft een openbaar IP-adres dat kan worden gebruikt als een openbaar IP-adres op exemplaar niveau om toegang te krijgen tot de virtuele machine.  De NAT-service is stroom richting, en vervangt de standaard Internet bestemming in uw subnet. Het open bare IP-adres van de virtuele machine wordt niet gebruikt voor uitgaande verbindingen.

### <a name="create-public-ip-for-source-vm"></a>Een openbaar IP-adres voor de bron-VM maken

We maken een openbaar IP-adres dat wordt gebruikt voor toegang tot de virtuele machine.  Gebruik [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip) om een resource met een openbaar IP-adres te maken met de naam **myPublicIPVM** in **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM \
    --sku standard
```

### <a name="create-an-nsg-for-vm"></a>Een NSG voor de virtuele machine maken

Omdat standaard open bare IP-adressen standaard ' veilig ' zijn, moet er een NSG worden gemaakt om de toegang tot de SSH mogelijk te maken. Gebruik [AZ Network NSG Create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) om een NSG-resource met de naam **mijnnbg** in **myResourceGroupNAT**te maken.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSG 
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>SSH-eind punt beschikbaar maken op de bron-VM

We maken een regel in het NSG voor SSH-toegang tot de bron-VM. Gebruik [AZ Network NSG Rule Create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) om een NSG-regel met de naam **SSH** te maken in de NSG met de naam **mijnnbg** in **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSG \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
```

### <a name="create-nic-for-vm"></a>NIC voor VM maken

Maak een netwerk interface met [AZ Network NIC Create](/cli/azure/network/nic#az-network-nic-create) en koppel deze aan het open bare IP-adres en de netwerk beveiligings groep. 

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --public-ip-address myPublicIPVM \
    --network-security-group myNSG
```

### <a name="create-vm"></a>VM maken

Maak de virtuele machine met [AZ VM Create](/cli/azure/vm#az-vm-create).  We genereren SSH-sleutels voor deze VM en slaan de persoonlijke sleutel op voor later gebruik.

 ```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --generate-ssh-keys
```

Wacht tot de virtuele machine is geïmplementeerd en ga vervolgens verder met de overige stappen.

## <a name="discover-the-ip-address-of-the-vm"></a>Het IP-adres van de virtuele machine detecteren

Eerst moet het IP-adres worden gedetecteerd van de virtuele machine die u hebt gemaakt. Gebruik [AZ Network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show)om het open bare IP-adres van de virtuele machine op te halen. 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>Kopieer het open bare IP-adres en plak het in een Klad blok, zodat u het kunt gebruiken om toegang te krijgen tot de virtuele machine.

### <a name="sign-in-to-vm"></a>Aanmelden bij de VM

De SSH-referenties moeten worden opgeslagen in uw Cloud Shell van de vorige bewerking.  Open een [Azure Cloud shell](https://shell.azure.com) in uw browser. Gebruik het IP-adres dat in de vorige stap is opgehaald om SSH naar de virtuele machine te gaan.

```bash
ssh <ip-address-destination>
```

U bent nu klaar om de NAT-service te gebruiken.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [AZ Group delete](/cli/azure/group#az-group-delete) gebruiken om de resource groep en alle resources in te verwijderen wanneer u deze niet meer nodig hebt.

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een NAT-gateway en een virtuele machine gemaakt om deze te gebruiken. 

Controleer de metrische gegevens in Azure Monitor om uw NAT-service weer te geven. Problemen vaststellen, zoals de bron uitputting van de beschik bare SNAT-poorten.  Bron uitputting van de SNAT-poorten wordt opgelost door extra open bare IP-adres bronnen of open bare IP-prefix bronnen of beide toe te voegen.


- Meer informatie over [Azure Virtual Network NAT](./nat-overview.md)
- Meer informatie over de [NAT gateway-resource](./nat-gateway-resource.md).
- Quick start voor het implementeren van [NAT-gateway resource met behulp van Azure cli](./quickstart-create-nat-gateway-cli.md).
- Quick start voor het implementeren van [NAT-gateway resource met behulp van Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Quick start voor het implementeren van [NAT-gateway resource met behulp van Azure Portal](./quickstart-create-nat-gateway-portal.md).
> [!div class="nextstepaction"]

