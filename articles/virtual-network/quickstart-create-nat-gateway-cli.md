---
title: 'Snelstart: een NAT-gateway maken - Azure CLI'
titlesuffix: Azure Virtual Network NAT
description: In deze quickstart ziet u hoe u een NAT-gateway maakt met de Azure CLI
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79202192"
---
# <a name="quickstart-create-a-nat-gateway-using-azure-cli"></a>Snelstart: een NAT-gateway maken met Azure CLI

In deze snelstart ziet u hoe u de NAT-service van Azure Virtual Network gebruiken. U maakt een NAT-gateway om uitgaande connectiviteit te bieden voor een virtuele machine in Azure. 


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

U deze zelfstudie voltooien met Azure Cloud Shell of de desbetreffende opdrachten lokaal uitvoeren.  Als u Azure Cloud Shell nog nooit hebt gebruikt, [meldt u zich nu aan](https://shell.azure.com) om de eerste installatie te doorlopen.
Als u ervoor kiest deze opdrachten lokaal uit te voeren, moet u CLI installeren.  Deze zelfstudie vereist dat u een versie van de Azure CLI-versie 2.0.71 of hoger uitvoert. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren]( /cli/azure/install-azure-cli).


## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep maken met [az group create](https://docs.microsoft.com/cli/azure/group). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

In het volgende voorbeeld wordt een resourcegroep met de naam **myResourceGroupNAT** op de locatie **Eastus2** ge:

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>De NAT-gateway maken

### <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken

Om toegang te krijgen tot het openbare internet, hebt u een of meer openbare IP-adressen nodig voor de NAT-gateway. Gebruik [het public-ip-maken van het AZ-netwerk](https://docs.microsoft.com/cli/azure/network/public-ip) om een openbare IP-adresbron met de naam **myPublicIP** te maken in **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIP \
    --sku standard
```

### <a name="create-a-public-ip-prefix"></a>Een openbaar IP-voorvoegsel maken

U een of meer openbare IP-adresbronnen, openbare IP-voorvoegsels of beide met NAT-gateway gebruiken. We voegen een openbare IP-bron toe aan dit scenario om aan te tonen.   Gebruik [het public-ip-voorvoegsel van het AZ-netwerk](https://docs.microsoft.com/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create) om een openbare IP-voorvoegselbron met de naam **myPublicIPprefix** in **myResourceGroupNAT**te maken.

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPprefix \
    --length 31
```

### <a name="create-a-nat-gateway-resource"></a>Een NAT-gatewaybron maken

In deze sectie wordt beschreven hoe u de volgende onderdelen van de NAT-service maken en configureren met behulp van de NAT-gatewaybron:
  - Een openbare IP-groep en een openbaar IP-voorvoegsel die moeten worden gebruikt voor uitgaande stromen die zijn vertaald door de NAT-gatewaybron.
  - Wijzig de niet-actieve time-out van de standaardinstelling van 4 minuten naar 10 minuten.

Maak een wereldwijde Azure NAT-gateway met [az-netwerk nat-gateway maak naam](https://docs.microsoft.com/cli/azure/network/nat?view=azure-cli-latest) **myNATgateway**. De opdracht maakt gebruik van zowel het openbare IP-adres **myPublicIP** als het openbare IP-voorvoegsel **myPublicIPprefix**. De opdracht wijzigt de inactieve time-out in **10** minuten.

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIP \
    --public-ip-prefixes myPublicIPprefix \
    --idle-timeout 10       
  ```

Op dit punt is de NAT-gateway functioneel en ontbreekt het alleen om te configureren welke subnetten van een virtueel netwerk het moeten gebruiken.

## <a name="configure-virtual-network"></a>Virtueel netwerk configureren

Voordat u een VM implementeert en uw NAT-gateway gebruiken, moeten we het virtuele netwerk maken.

Maak een virtueel netwerk met de naam **myVnet** met een subnet genaamd **mySubnet** in de **myResourceGroupNAT** met behulp van [az-netwerk vnet maken](https://docs.microsoft.com/cli/azure/network/vnet).  De IP-adresruimte voor het virtuele netwerk is **192.168.0.0/16**. Het subnet binnen het virtuele netwerk is **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.0.0/24
```

### <a name="configure-nat-service-for-source-subnet"></a>NAT-service configureren voor bronsubnet

We configureren het bronsubnet **mySubnet** in het virtuele netwerk **myVnet** om een specifieke NAT gateway resource **myNATgateway** te gebruiken met [az-netwerk vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet).  Met deze opdracht wordt de NAT-service geactiveerd op het opgegeven subnet.

```azurecli-interactive
  az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnet \
    --name mySubnet \
    --nat-gateway myNATgateway
```

Al het uitgaande verkeer naar internetbestemmingen maakt nu gebruik van de NAT-gateway.  Het is niet nodig om een UDR te configureren.

## <a name="create-a-vm-to-use-the-nat-service"></a>Een VM maken om de NAT-service te gebruiken

We maken nu een VM om de NAT-service te gebruiken.  Deze VM heeft een openbaar IP-adres om te gebruiken als openbaar IP op instantie, zodat u toegang hebt tot de VM.  NAT-service is de stroomrichting bewust en vervangt de standaardinternetbestemming in uw subnet. Het openbare IP-adres van de VM wordt niet gebruikt voor uitgaande verbindingen.

### <a name="create-public-ip-for-source-vm"></a>Openbaar IP maken voor bron-VM

We maken een openbaar IP om toegang te krijgen tot de VM.  Gebruik [het public-ip-maken van het AZ-netwerk](https://docs.microsoft.com/cli/azure/network/public-ip) om een openbare IP-adresbron met de naam **myPublicIPVM** te maken in **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM \
    --sku standard
```

### <a name="create-an-nsg-for-vm"></a>Een NSG voor VM maken

Omdat standaard ip-adressen 'standaard veilig' zijn, moeten we een NSG maken om inkomende toegang voor ssh-toegang mogelijk te maken. Gebruik [het AZ-netwerk nsg maken](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) om een NSG-bron met de naam **myNSG** te maken in **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSG 
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>SSH-eindpunt blootleggen op bron-VM

We maken een regel in de NSG voor SSH-toegang tot de bronvm. Gebruik [de NSG-regel van het AZ-netwerk](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) om een NSG-regel met de naam **ssh** te maken in de NSG met de naam **myNSG** in **myResourceGroupNAT.**

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

Maak een netwerkinterface met [het AZ-netwerk en](/cli/azure/network/nic#az-network-nic-create) koppel het aan het openbare IP-adres en de netwerkbeveiligingsgroep. 

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

Maak de virtuele machine met [az vm maken](/cli/azure/vm#az-vm-create).  We genereren ssh-sleutels voor deze VM en slaan de privésleutel op om later te gebruiken.

 ```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --generate-ssh-keys
```

Wacht tot de VM is geïmplementeerd en ga verder met de rest van de stappen.

## <a name="discover-the-ip-address-of-the-vm"></a>Ontdek het IP-adres van de VM

Eerst moeten we het IP-adres van de VM die u hebt gemaakt ontdekken. Gebruik de [public-ip-show van az-netwerk](/cli/azure/network/public-ip#az-network-public-ip-show)om het openbare IP-adres van de VM op te halen. 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>Kopieer het openbare IP-adres en plak het in een notitieblok, zodat u het gebruiken om toegang te krijgen tot de VM.

### <a name="sign-in-to-vm"></a>Aanmelden bij VM

De SSH-referenties moeten vanaf de vorige bewerking in uw Cloud Shell worden opgeslagen.  Open een [Azure Cloud Shell](https://shell.azure.com) in uw browser. Gebruik het IP-adres dat in de vorige stap naar SSH is opgehaald naar de virtuele machine.

```bash
ssh <ip-address-destination>
```

U bent nu klaar om de NAT-service te gebruiken.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer dit niet meer nodig is, u de opdracht verwijderen van de [AZ-groep](/cli/azure/group#az-group-delete) gebruiken om de brongroep en alle bronnen binnen te verwijderen.

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een NAT-gateway en een VM gemaakt om deze te gebruiken. 

Bekijk de statistieken in Azure Monitor om uw NAT-service te bekijken. Diagnose problemen zoals resource uitputting van de beschikbare SNAT-poorten.  Uitputting van resources van SNAT-poorten wordt aangepakt door extra openbare IP-adresbronnen of openbare IP-voorvoegselbronnen of beide toe te voegen.


- Meer informatie over [Azure Virtual Network NAT](./nat-overview.md)
- Meer informatie over [NAT-gatewaybron](./nat-gateway-resource.md).
- Snel starten voor het implementeren van [NAT-gatewaybron met Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Snel starten voor het implementeren van [NAT-gatewaybron met Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Snel starten voor het implementeren van [NAT-gatewaybron met Azure-portal.](./quickstart-create-nat-gateway-portal.md)
> [!div class="nextstepaction"]

