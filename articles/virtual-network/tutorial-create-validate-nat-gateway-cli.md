---
title: 'Zelfstudie: Een NAT-gateway maken en testen - Azure CLI'
titlesuffix: Azure Virtual Network NAT
description: In deze zelfstudie ziet u hoe u een NAT-gateway maakt met de Azure CLI en de NAT-service test
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to test a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: b3e10b3abbe5c9815e51ce67786882dbd294df3f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79202211"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-cli-and-test-the-nat-service"></a>Zelfstudie: Een NAT-gateway maken met Azure CLI en de NAT-service testen

In deze zelfstudie maakt u een NAT-gateway om uitgaande connectiviteit te bieden voor virtuele machines in Azure. Als u de NAT-gateway wilt testen, implementeert u een virtuele bron- en doelmachine. U test de NAT-gateway door uitgaande verbindingen te maken met een openbaar IP-adres. Deze verbindingen komen van de bron naar de virtuele bestemming machine. Deze zelfstudie implementeert bron en bestemming in twee verschillende virtuele netwerken in dezelfde brongroep voor alleen eenvoud.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

U deze zelfstudie voltooien met Azure Cloud Shell of de desbetreffende opdrachten lokaal uitvoeren.  Als u Azure Cloud Shell niet hebt gebruikt, moet u [zich nu aanmelden.](https://shell.azure.com)

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

Om toegang te krijgen tot het openbare internet, hebt u een of meer openbare IP-adressen nodig voor de NAT-gateway. Gebruik [het public-ip-maken van het AZ-netwerk](https://docs.microsoft.com/cli/azure/network/public-ip) om een openbare IP-adresbron met de naam **myPublicIPsource** te maken in **myResourceGroupNAT.**

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPsource \
  --sku standard
```

### <a name="create-a-public-ip-prefix"></a>Een openbaar IP-voorvoegsel maken

U een of meer openbare IP-adresbronnen, openbare IP-voorvoegsels of beide gebruiken met NAT-gateway. We voegen een openbare IP-bron toe aan dit scenario om aan te tonen.   Gebruik [het public-ip-voorvoegsel van het AZ-netwerk](/cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-create) om een openbare IP-voorvoegselbron met de naam **myPublicIPprefixsource** te maken in **myResourceGroupNAT.**

```azurecli-interactive
  az network public-ip prefix create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPprefixsource \
  --length 31
```

### <a name="create-a-nat-gateway-resource"></a>Een NAT-gatewaybron maken

In deze sectie wordt beschreven hoe u de volgende onderdelen van de NAT-service maken en configureren met behulp van de NAT-gatewaybron:
  - Een openbare IP-groep en een openbaar IP-voorvoegsel die moeten worden gebruikt voor uitgaande stromen die zijn vertaald door de NAT-gatewaybron.
  - Wijzig de niet-actieve time-out van de standaardinstelling van 4 minuten naar 10 minuten.

Maak een wereldwijde Azure NAT-gateway met [az-netwerk nat-gateway maak naam](https://docs.microsoft.com/cli/azure/network/nat?view=azure-cli-latest) **myNATgateway**. De opdracht maakt gebruik van zowel het openbare IP-adres **myPublicIP** als het openbare IP-voorvoegsel **myPublicIPprefix**. De opdracht wijzigt ook de niet-actieve time-out in 10 minuten.

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIPsource \
    --public-ip-prefixes myPublicIPprefixsource \
    --idle-timeout 10       
  ```

Op dit punt is de NAT-gateway functioneel en ontbreekt het alleen om te configureren welke subnetten van een virtueel netwerk het moeten gebruiken.

## <a name="prepare-the-source-for-outbound-traffic"></a>De bron voorbereiden op uitgaand verkeer

Wij begeleiden u bij het opzetten van een volledige testomgeving. U stelt een test in met open-sourcetools om de NAT-gateway te verifiëren. We beginnen met de bron, die de NAT-gateway gebruikt die we eerder hebben gemaakt.

### <a name="configure-virtual-network-for-source"></a>Virtueel netwerk configureren voor bron

Voordat u een VM implementeert en uw NAT-gateway testen, moeten we het virtuele netwerk maken.

Maak een virtueel netwerk met de naam **myVnetsource** met een subnet genaamd **mySubnetsource** in de **myResourceGroupNAT** met behulp van [het AZ-netwerk Microsoft Azure Virtual Network maken.](https://docs.microsoft.com/cli/azure/network/vnet)  De IP-adresruimte voor het virtuele netwerk is **192.168.0.0/16**. Het subnet binnen het virtuele netwerk is **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnetsource \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetsource \
    --subnet-prefix 192.168.0.0/24
```

### <a name="configure-nat-service-for-source-subnet"></a>NAT-service configureren voor bronsubnet

Configureer het bronsubnet **mySubnetsource** in het virtuele netwerk **myVnetsource** om een specifieke NAT-gatewaybron **myNATgateway** te gebruiken met [subnetupdate van het AZ-netwerk Microsoft Azure Virtual Network.](https://docs.microsoft.com/cli/azure/network/vnet/subnet) Met deze opdracht wordt de NAT-service geactiveerd op het opgegeven subnet.

```azurecli-interactive
    az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnetsource \
    --name mySubnetsource \
    --nat-gateway myNATgateway
```

Al het uitgaande verkeer naar internetbestemmingen maakt nu gebruik van de NAT-service.  Het is niet nodig om een UDR te configureren.

Voordat we de NAT-gateway kunnen testen, moeten we een bron-VM maken.  We wijzen een openbare IP-adresbron toe als openbaar IP-adres op instantieom toegang te krijgen tot deze vm van buitenaf. Dit adres wordt alleen gebruikt om toegang te krijgen voor de test.  We laten zien hoe de NAT-service voorrang heeft op andere uitgaande opties.

U deze VM ook maken zonder een openbaar IP en een andere VM maken om te gebruiken als een jumpbox zonder een openbaar IP als een oefening.

### <a name="create-public-ip-for-source-vm"></a>Openbaar IP maken voor bron-VM

We maken een openbaar IP dat kan worden gebruikt om toegang te krijgen tot de bron-VM. Gebruik [het public-ip-maken van het AZ-netwerk](https://docs.microsoft.com/cli/azure/network/public-ip) om een openbare IP-adresbron met de naam **myPublicIPsourceVM** in **myResourceGroupNAT**te maken.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM \
    --sku standard
```

### <a name="create-an-nsg-for-source-vm"></a>Een NSG maken voor bron-VM

Omdat standaard openbare IP-adressen standaard 'veilig' zijn, moeten we een NSG maken om inkomende toegang voor ssh-toegang mogelijk te maken.  Azure NAT-service is stroomrichtingsbewust. Deze NSG wordt niet gebruikt voor uitgaande zodra de NAT-gateway is geconfigureerd op hetzelfde subnet. Gebruik [het AZ-netwerk nsg maken](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) om een NSG-bron met de naam **myNSGsource** te maken in **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGsource 
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>SSH-eindpunt blootleggen op bron-VM

We maken een regel in de NSG voor SSH-toegang tot de bronvm. Gebruik [de NSG-regel van het AZ-netwerk om](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) een NSG-regel met de naam **ssh**te maken. Deze regel wordt gemaakt in de NSG met de naam **myNSGsource** in de brongroep **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGsource \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
```

### <a name="create-nic-for-source-vm"></a>NIC voor bron-VM maken

Maak een netwerkinterface met [het az-netwerk en](/cli/azure/network/nic#az-network-nic-create) associeer met het openbare IP-adres en de netwerkbeveiligingsgroep. 

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicsource \
    --vnet-name myVnetsource \
    --subnet mySubnetsource \
    --public-ip-address myPublicIPSourceVM \
    --network-security-group myNSGsource
```

### <a name="create-a-source-vm"></a>Een bron-VM maken

Maak de virtuele machine met [az vm maken](/cli/azure/vm#az-vm-create).  We genereren ssh-sleutels voor deze VM en slaan de privésleutel op om later te gebruiken.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMsource \
    --nics myNicsource \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait
```

Hoewel de opdracht onmiddellijk terugkeert, kan het enkele minuten duren voordat de VM wordt geïmplementeerd.

## <a name="prepare-destination-for-outbound-traffic"></a>Bestemming voorbereiden op uitgaand verkeer

We maken nu een bestemming voor het uitgaande verkeer dat door de NAT-service is vertaald, zodat u het testen.

### <a name="configure-virtual-network-for-destination"></a>Virtueel netwerk configureren voor bestemming

 We moeten een virtueel netwerk creëren waar de virtuele machine van de bestemming zal zijn.  Deze opdrachten zijn dezelfde stappen als voor de bron-VM met kleine wijzigingen om het doeleindpunt bloot te leggen.

Maak een virtueel netwerk met de naam **myVnetdestination** met een subnet genaamd **mySubnetdestination** in de **myResourceGroupNAT** met behulp van [az-netwerk Microsoft Azure Virtual Network maken](https://docs.microsoft.com/cli/azure/network/vnet).  De IP-adresruimte voor het virtuele netwerk is **192.168.0.0/16**. Het subnet binnen het virtuele netwerk is **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location westus \
    --name myVnetdestination \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetdestination \
    --subnet-prefix 192.168.0.0/24
```

### <a name="create-public-ip-for-destination-vm"></a>Openbaar IP maken voor doel-VM

We maken een openbaar IP dat kan worden gebruikt om toegang te krijgen tot de bron-VM. Gebruik [het public-ip-maken van het AZ-netwerk](https://docs.microsoft.com/cli/azure/network/public-ip) om een openbare IP-adresbron met de naam **myPublicIPdestinationVM** in **myResourceGroupNAT**te maken. 

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPdestinationVM \
  --sku standard \
  --location westus
```

### <a name="create-an-nsg-for-destination-vm"></a>Een NSG maken voor doel-VM

Standaard openbare IP-adressen zijn 'standaard veilig', u moet een NSG maken om inkomende toegang voor ssh mogelijk te maken. De Azure NAT-service is stroomrichtingsbewust. Deze NSG wordt niet gebruikt voor uitgaande zodra de NAT-gateway is geconfigureerd op hetzelfde subnet. Gebruik [de NSG-nsg-zender AZ-netwerk](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) om een NSG-bron met de naam **myNSGdestination** te maken in **myResourceGroupNAT.**

```azurecli-interactive
    az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGdestination \
    --location westus
```

### <a name="expose-ssh-endpoint-on-destination-vm"></a>SSH-eindpunt blootleggen op doel-VM

We maken een regel in de NSG voor SSH toegang tot de bestemming vm. Gebruik [de NSG-regel van het AZ-netwerk om](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) een NSG-regel met de naam **ssh**te maken. Deze regel wordt gemaakt in de NSG met de naam **myNSGdestination** in de resourcegroep **myResourceGroupNAT**.

```azurecli-interactive
    az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGdestination \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
```

### <a name="expose-http-endpoint-on-destination-vm"></a>HTTP-eindpunt blootleggen op doel-VM

We maken een regel in de NSG voor HTTP-toegang tot de doelvm. Gebruik [de NSG-regel van het AZ-netwerk](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) om een NSG-regel met de naam **http** te maken in de NSG met de naam **myNSGdestination** in **myResourceGroupNAT.**

```azurecli-interactive
    az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGdestination \
    --priority 101 \
    --name http \
    --description "HTTP access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 80
```

### <a name="create-nic-for-destination-vm"></a>NIC voor doelVM maken

Maak een netwerkinterface met [az-netwerknic create](/cli/azure/network/nic#az-network-nic-create) en associeer met het openbare IP-adres **myPublicIPdestinationVM** en de netwerkbeveiligingsgroep **myNSGdestination.** 

```azurecli-interactive
    az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicdestination \
    --vnet-name myVnetdestination \
    --subnet mySubnetdestination \
    --public-ip-address myPublicIPdestinationVM \
    --network-security-group myNSGdestination \
    --location westus
```

### <a name="create-a-destination-vm"></a>Een doel-VM maken

Maak de virtuele machine met [az vm maken](/cli/azure/vm#az-vm-create).  We genereren ssh-sleutels voor deze VM en slaan de privésleutel op om later te gebruiken.

 ```azurecli-interactive
    az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMdestination \
    --nics myNicdestination \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait \
    --location westus
```
Hoewel de opdracht onmiddellijk terugkeert, kan het enkele minuten duren voordat de VM wordt geïmplementeerd.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Een webserver voorbereiden en payload testen op doel-VM

Eerst moeten we het IP-adres van de doel-VM ontdekken.  Gebruik de [public-ip-show van az-netwerk om](/cli/azure/network/public-ip#az-network-public-ip-show)het openbare IP-adres van de bestemmingvm te krijgen. 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPdestinationVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>Kopieer het openbare IP-adres en plak het in een notitieblok, zodat u het in volgende stappen gebruiken. Geef aan dat dit de virtuele bestemmingsmachine is.

### <a name="sign-in-to-destination-vm"></a>Aanmelden bij doel-VM

De SSH-referenties moeten vanaf de vorige bewerking in uw Cloud Shell worden opgeslagen.  Open een [Azure Cloud Shell](https://shell.azure.com) in uw browser. Gebruik het IP-adres dat in de vorige stap naar SSH is opgehaald naar de virtuele machine. 

```bash
ssh <ip-address-destination>
```

Kopieer en plak de volgende opdrachten zodra u zich hebt aangemeld.  

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100
```

Deze opdrachten werken uw virtuele machine bij, installeren nginx en maken een bestand van 100 KBytes. Dit bestand wordt opgehaald van de bron-VM met behulp van de NAT-service.

Sluit de SSH-sessie af met de doel-VM.

## <a name="prepare-test-on-source-vm"></a>Test voorbereiden op bron-VM

Eerst moeten we het IP-adres van de bron VM te ontdekken.  Gebruik het [AZ-netwerk public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show)om het openbare IP-adres van de bron VM te krijgen. 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>Kopieer het openbare IP-adres en plak het in een notitieblok, zodat u het in volgende stappen gebruiken. Geef aan dat dit de virtuele bronmachine is.

### <a name="sign-in-to-source-vm"></a>Aanmelden bij bron-VM

Nogmaals, de SSH-referenties worden opgeslagen in Cloud Shell. Open een nieuw tabblad voor [Azure Cloud Shell](https://shell.azure.com) in uw browser.  Gebruik het IP-adres dat in de vorige stap naar SSH is opgehaald naar de virtuele machine. 

```bash
ssh <ip-address-source>
```

Kopieer en plak de volgende opdrachten om u voor te bereiden op het testen van de NAT-service.

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

Deze opdracht zal uw virtuele machine bijwerken, go installeren, [hey](https://github.com/rakyll/hey) van GitHub installeren en uw shell-omgeving bijwerken.

U bent nu klaar om de NAT-service te testen.

## <a name="validate-nat-service"></a>NAT-service valideren

Terwijl u bent aangemeld bij de bron-VM, u **curl** en **hey** gebruiken om aanvragen naar het IP-adres van de bestemming te genereren.

Gebruik curl om het bestand 100-KBytes op te halen.  Vervang ** \<ip-adres-bestemming>** in het onderstaande voorbeeld door het doel-IP-adres dat u eerder hebt gekopieerd.  De parameter **--output** geeft aan dat het opgehaalde bestand wordt verwijderd.

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

U ook het genereren van een reeks verzoeken met behulp van **hey**. Vervang nogmaals ** \<ip-adres-bestemming>** door het doel-IP-adres dat u eerder hebt gekopieerd.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

Deze opdracht genereert 100 aanvragen, 10 gelijktijdig, met een time-out van 30 seconden. De TCP-verbinding wordt niet opnieuw gebruikt.  Elke aanvraag haalt 100 Kbytes op.  Aan het einde van de run, **hey** zal een aantal statistieken over hoe goed de NAT service deed verslag.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer dit niet meer nodig is, u de opdracht verwijderen van de [AZ-groep](/cli/azure/group#az-group-delete) gebruiken om de brongroep en alle bronnen binnen te verwijderen.

```azurecli-interactive 
  az group delete --name myResourceGroupNAT
```

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u een NAT-gateway gemaakt, een bron- en doel-VM gemaakt en vervolgens de NAT-gateway getest.

Bekijk de statistieken in Azure Monitor om uw NAT-service te bekijken. Diagnose problemen zoals resource uitputting van de beschikbare SNAT-poorten.  Uitputting van resources van SNAT-poorten is eenvoudig aan te pakken door extra openbare IP-adresbronnen of openbare IP-voorvoegselbronnen of beide toe te voegen.

- Meer informatie over [NAT van virtueel netwerk](./nat-overview.md)
- Meer informatie over [NAT-gatewaybron](./nat-gateway-resource.md).
- Snel starten voor het implementeren van [NAT-gatewaybron met Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Snel starten voor het implementeren van [NAT-gatewaybron met Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Snel starten voor het implementeren van [NAT-gatewaybron met Azure-portal.](./quickstart-create-nat-gateway-portal.md)

> [!div class="nextstepaction"]

