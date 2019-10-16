---
title: 'Snelstartgids: een NAT-gateway maken-Azure CLI'
titlesuffix: Azure NAT service
description: Deze Quick Start laat zien hoe u een NAT-gateway maakt met behulp van de Azure CLI
services: nat
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a NAT Gateway for outbound connectivity for my virtual network.
ms.service: nat
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/21/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: e7b78451ea6bf0a19eb358c8777ce59c07e32fd8
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376397"
---
# <a name="quickstart-create-a-nat-gateway-using-azure-cli"></a>Snelstartgids: een NAT-gateway maken met behulp van Azure CLI

In deze Quick start ziet u hoe u de Azure NAT-service gebruikt en hoe u een NAT-gateway maakt om uitgaande connectiviteit te bieden voor een virtuele machine in Azure. 

>[!NOTE] 
>De Azure NAT-service is op dit moment beschikbaar als open bare preview en beschikbaar in een beperkt aantal [regio's](https://azure.microsoft.com/global-infrastructure/regions/). Deze preview is beschikbaar zonder service level agreement en wordt niet aanbevolen voor productie werkbelastingen. De reden hiervoor is dat bepaalde functies mogelijk niet worden ondersteund of beperkte mogelijkheden hebben. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.comsupport/legal/preview-supplemental-terms).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

U kunt deze zelf studie volt ooien met behulp van Azure Cloud shell of de respectievelijke opdrachten lokaal uitvoeren.  Als u Azure Cloud shell nog nooit hebt gebruikt, [meldt](https://shell.azure.com) u zich nu aan om de eerste installatie te door lopen.

Als u ervoor kiest om deze opdrachten lokaal uit te voeren, moet u CLI installeren.  Voor deze zelf studie moet u een versie van de Azure CLI-versie 2.0.71 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep maken met [az group create](https://docs.microsoft.com/cli/azure/group). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

In het volgende voor beeld wordt een resource groep met de naam *myResourceGroupNAT* gemaakt op de locatie *eastus2* :

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

U kunt een of meer open bare IP-adres bronnen of een of meer open bare IP-voor voegsels of beide met NAT-gateway gebruiken. We gaan een open bare IP-prefix bron toevoegen aan dit scenario om te demonstreren.   Gebruik [AZ Network Public-IP prefix Create](https://docs.microsoft.com/cli/azure/network/public-ip-prefix) om een open bare IP-adres voorvoegsel resource te maken met de naam **myPublicIPprefix** in **myResourceGroupNAT**.

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

Maak een globale Azure NAT-gateway met [AZ Network NAT gateway Create](https://docs.microsoft.com/cli/azure/network/nat?view=azure-cli-latest) named **myNATgateway** die zowel het open bare IP-adres **myPublicIP** als het open bare IP-voor voegsel **myPublicIPprefix** gebruikt en de time-out voor inactiviteit verandert in 10 minuten.

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

Maak met [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) in **myResourceGroup** een virtueel netwerk met de naam **myVnet** met een subnet met de naam **mySubnet**.  De IP-adres ruimte voor het virtuele netwerk is **192.168.0.0/16** en het subnet in het virtuele netwerk is **192.168.0.0/24**.

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

We configureren het bron- **mySubnet** in **myVnet** van het virtuele netwerk voor het gebruik van een specifieke NAT gateway resource **myNAT** met [AZ Network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet).  Met deze opdracht wordt de NAT-service op het opgegeven subnet geactiveerd.

```azurecli-interactive
  az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnet \
    --name mySubnet \
    --nat-gateway myNATgateway
```

Al het uitgaande verkeer naar Internet bestemmingen maakt nu gebruik van de NAT-service.  Het is niet nodig om een UDR te configureren.

## <a name="create-a-vm-to-use-the-nat-service"></a>Een virtuele machine maken voor het gebruik van de NAT-service

We gaan nu een VM maken voor het gebruik van de NAT-service.  Deze VM heeft een openbaar IP-adres dat kan worden gebruikt als een openbaar IP-adres op exemplaar niveau om toegang te krijgen tot de virtuele machine.  De NAT-service is stroom richting, en vervangt de standaard Internet bestemming in uw subnet. Het open bare IP-adres van de virtuele machine wordt niet gebruikt voor uitgaande verbindingen.

### <a name="create-public-ip-for-source-vm"></a>Een openbaar IP-adres voor de bron-VM maken

We maken een openbaar IP-adres dat wordt gebruikt voor toegang tot de virtuele machine.  Gebruik [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip) om een resource met een openbaar IP-adres te maken met de naam **myPublicIPVM** in **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM
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
    --access allow 
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
    --generate-ssh-keys \
```

Wacht totdat de implementatie van de virtuele machine is voltooid en ga daarna verder met de rest van de stappen.

## <a name="discover-the-ip-address-of-the-vm"></a>Het IP-adres van de virtuele machine detecteren

Eerst moet het IP-adres worden gedetecteerd van de virtuele machine die u hebt gemaakt. Gebruik [AZ Network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show)om het open bare IP-adres van de virtuele machine op te halen. 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>Kopieer het open bare IP-adres en plak het in een Klad blok, zodat u het kunt gebruiken om toegang te krijgen tot de virtuele machine.

### <a name="sign-in-to-vm"></a>Aanmelden bij de VM

De SSH-referenties moeten worden opgeslagen in de Cloud shell vanuit de vorige bewerking.  Open een [Azure Cloud shell](https://shell.azure.com) in uw browser. Gebruik het IP-adres dat in de vorige stap is opgehaald om SSH naar de virtuele machine te gaan.

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

In deze zelf studie hebt u een NAT-gateway en een virtuele machine gemaakt voor het gebruik van de NAT-service. Ga verder met andere zelf studies voor de Azure NAT-service voor meer informatie over de Azure NAT-service.

U kunt ook de metrische gegevens in Azure Monitor bekijken om de werking van uw NAT-service te bekijken en problemen op te lossen, zoals de bron uitputting van de beschik bare SNAT-poorten.  Bron uitputting van de SNAT-poorten kan gemakkelijk worden verholpen door extra open bare IP-adres bronnen of open bare IP-prefix bronnen of beide toe te voegen.

> [!div class="nextstepaction"]

