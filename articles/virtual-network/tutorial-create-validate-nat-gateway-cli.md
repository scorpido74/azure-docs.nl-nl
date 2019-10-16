---
title: 'Zelf studie: een NAT-gateway maken en testen-Azure CLI'
titlesuffix: Azure NAT service
description: Deze zelf studie laat zien hoe u een NAT-gateway maakt met behulp van de Azure CLI en de NAT-service test
services: nat
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to test a NAT Gateway for outbound connectivity for my virtual network.
ms.custom: seodec18
ms.service: nat
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/21/2019
ms.author: allensu
ms.openlocfilehash: f97284b3c629dbaab1b0cbb54b1f114342278da7
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376488"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-cli-and-test-the-nat-service"></a>Zelf studie: een NAT-gateway maken met behulp van Azure CLI en de NAT-service testen

In deze zelf studie wordt uitgelegd hoe u de Azure NAT-service gebruikt en hoe u een NAT-gateway maakt om uitgaande connectiviteit te bieden voor virtuele machines in Azure. Als u de NAT-gateway wilt testen, implementeert u een virtuele bron-en doel machine.  U gaat de NAT-gateway testen door uitgaande verbindingen te maken met een openbaar IP-adres van de bron naar de virtuele doel machine.  In deze zelf studie worden de bron en bestemming in twee verschillende virtuele netwerken in dezelfde resource groep geïmplementeerd voor eenvoud.

>[!NOTE] 
>De Azure NAT-service is op dit moment beschikbaar als open bare preview en beschikbaar in een beperkt aantal [regio's](https://azure.microsoft.com/global-infrastructure/regions/). Deze preview is beschikbaar zonder service level agreement en wordt niet aanbevolen voor productie werkbelastingen. De reden hiervoor is dat bepaalde functies mogelijk niet worden ondersteund of beperkte mogelijkheden hebben. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.comsupport/legal/preview-supplemental-terms).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

U kunt deze zelf studie volt ooien met behulp van Azure Cloud shell of de respectievelijke opdrachten lokaal uitvoeren.  Als u Azure Cloud shell nog nooit hebt gebruikt, moet u zich [nu aanmelden](https://shell.azure.com) om door te gaan met de eerste installatie.

Als u ervoor kiest om deze opdrachten lokaal uit te voeren, moet u CLI installeren.  Voor deze zelf studie moet u een versie van de Azure CLI-versie 2.0.71 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

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

Voor toegang tot het open bare Internet hebt u een of meer open bare IP-adressen nodig voor de NAT-gateway. Gebruik [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip) om een resource met een openbaar IP-adres te maken met de naam **myPublicIPsource** in **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPsource \
  --sku standard
```

### <a name="create-a-public-ip-prefix"></a>Een openbaar IP-voor voegsel maken

U kunt een of meer open bare IP-adres bronnen of een of meer open bare IP-voor voegsels of beide met NAT-gateway gebruiken. We gaan een open bare IP-prefix bron toevoegen aan dit scenario om te demonstreren.   Gebruik [AZ Network Public-IP prefix Create](https://docs.microsoft.com/cli/azure/network/public-ip-prefix) om een open bare IP-adres voorvoegsel resource te maken met de naam **myPublicIPprefixsource** in **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip prefix create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPprefixsource \
  --length 31
```

### <a name="create-a-nat-gateway-resource"></a>Een NAT-gateway resource maken

In deze sectie wordt beschreven hoe u de volgende onderdelen van de NAT-service kunt maken en configureren met behulp van de NAT-gateway resource:
  - Een open bare IP-adres groep en een openbaar IP-voor voegsel dat moet worden gebruikt voor uitgaande stromen die worden vertaald door de NAT-gateway resource.
  - Wijzig de time-out voor inactiviteit van de standaard waarde van 4 minuten in 10 minuten.

Een globale Azure NAT-gateway maken met [AZ Network NAT gateway Create](https://docs.microsoft.com/cli/azure/network/nat?view=azure-cli-latest) named **myNATgateway** die zowel het open bare IP-adres **myPublicIPsource** als het open bare IP-voor voegsel **myPublicIPprefixsource** gebruikt en de time-out voor inactiviteit wijzigt in **10 minuten**.

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIPsource \
    --public-ip-prefixes myPublicIPprefixsource \
    --idle-timeout 10       
  ```

Op dit punt is de NAT-gateway functioneel en ontbreekt er een configuratie van de subnetten van een virtueel netwerk.

## <a name="prepare-the-source-for-outbound-traffic"></a>De bron voorbereiden voor uitgaand verkeer

U wordt begeleid bij het configureren van een volledige test omgeving en de uitvoering van de tests zelf in de volgende stappen. We beginnen met de bron, die gebruikmaakt van de NAT-gateway resource die we eerder hebben gemaakt.

### <a name="configure-virtual-network-for-source"></a>Het virtuele netwerk configureren voor de bron

Voordat u een virtuele machine implementeert en uw NAT-gateway kunt testen, moet u het virtuele netwerk maken.

Maak een virtueel netwerk met de naam **myVnetsource** met een subnet met de naam **MySubnetsource** in de **myResourceGroupNAT** met [AZ Network vnet Create](https://docs.microsoft.com/cli/azure/network/vnet).  De IP-adres ruimte voor het virtuele netwerk is **192.168.0.0/16** en het subnet in het virtuele netwerk is **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnetsource \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetsource \
    --subnet-prefix 192.168.0.0/24
```

### <a name="configure-nat-service-for-source-subnet"></a>NAT-service voor bron subnet configureren

De NAT-gateway is al gemaakt en de **mySubnetsource** in **myVnetsource** van het bron subnet wordt nu geconfigureerd voor gebruik van een specifieke NAT gateway resource **myNATsource** met [AZ Network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet).  Met deze opdracht wordt de NAT-service op het opgegeven subnet geactiveerd.

```azurecli-interactive
    az network vnet subnet update \
    --resource-group myResourceGroupNAT
    --vnet-name myVnetsource \
    --name mySubnetsource \
    --nat-gateway myNATgateway
```

Al het uitgaande verkeer naar Internet bestemmingen maakt nu gebruik van de NAT-service.  Het is niet nodig om een UDR te configureren.

Voordat we de NAT-gateway kunnen testen, moeten we een bron-VM maken.  Er wordt een open bare IP-adres resource toegewezen als een openbaar IP op exemplaar niveau voor toegang tot deze VM vanaf buiten. Dit adres wordt alleen gebruikt voor toegang tot de test.  We laten zien hoe de NAT-service voor rang heeft op andere uitgaande opties.

U kunt deze virtuele machine ook maken zonder een openbaar IP-adres en een andere virtuele machine maken om te gebruiken als JumpBox zonder open bare IP als oefening.

### <a name="create-public-ip-for-source-vm"></a>Een openbaar IP-adres voor de bron-VM maken

We maken een openbaar IP-adres om te gebruiken voor toegang tot de bron-VM. Gebruik [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip) om een resource met een openbaar IP-adres te maken met de naam **myPublicIPsourceVM** in **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM
    --sku standard
```

### <a name="create-an-nsg-for-source-vm"></a>Een NSG maken voor de bron-VM

Omdat standaard open bare IP-adressen standaard ' veilig ' zijn, moet er een NSG worden gemaakt om de toegang tot de SSH mogelijk te maken.  Omdat de NAT-service stroom richting is, wordt deze NSG niet gebruikt voor uitgaand verkeer wanneer de NAT-gateway is geconfigureerd op hetzelfde subnet. Gebruik [AZ Network NSG Create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) om een NSG-resource met de naam **myNSGsource** in **myResourceGroupNAT**te maken.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGsource 
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>SSH-eind punt beschikbaar maken op de bron-VM

We maken een regel in het NSG voor SSH-toegang tot de bron-VM. Gebruik [AZ Network NSG Rule Create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) om een NSG-regel met de naam **SSH** te maken in de NSG met de naam **myNSGsource** in **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGsource \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow 
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
```

### <a name="create-nic-for-source-vm"></a>NIC maken voor de bron-VM

Maak een netwerk interface met [AZ Network NIC Create](/cli/azure/network/nic#az-network-nic-create) en koppel deze aan het open bare IP-adres en de netwerk beveiligings groep. 

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicsource \
    --vnet-name myVnetsource \
    --subnet mySubnetsource \
    --public-ip-address myPublicIPSourceVM
    --network-security-group myNSGsource
```

### <a name="create-a-source-vm"></a>Een bron-VM maken

Maak de virtuele machine met [AZ VM Create](/cli/azure/vm#az-vm-create).  We genereren SSH-sleutels voor deze VM en slaan de persoonlijke sleutel op voor later gebruik.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMsource \
    --nics myNicsource \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait
```

Terwijl de opdracht onmiddellijk wordt geretourneerd, kan het enkele minuten duren voordat de virtuele machine wordt geïmplementeerd.

## <a name="prepare-destination-for-outbound-traffic"></a>Bestemming voorbereiden voor uitgaand verkeer

We gaan nu een bestemming maken voor het uitgaande verkeer dat door de NAT-service is vertaald, zodat u het kunt testen.

### <a name="configure-virtual-network-for-destination"></a>Virtueel netwerk voor bestemming configureren

Voordat u een virtuele machine en voor het doel implementeert, moet u ook een virtueel netwerk maken waarop de virtuele doel machine kan worden geplaatst.  Dit zijn dezelfde stappen als voor de bron-VM met enkele kleine wijzigingen om het bestemmings eindpunt zichtbaar te maken.

Maak een virtueel netwerk met de naam **myVnetdestination** met een subnet met de naam **MySubnetdestination** in de **myResourceGroupNAT** met [AZ Network vnet Create](https://docs.microsoft.com/cli/azure/network/vnet).  De IP-adres ruimte voor het virtuele netwerk is **192.168.0.0/16** en het subnet in het virtuele netwerk is **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location westus \
    --name myVnetdestination \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetdestination \
    --subnet-prefix 192.168.0.0/24
```

### <a name="create-public-ip-for-destination-vm"></a>Een openbaar IP-adres voor de doel-VM maken

We maken een openbaar IP-adres om te gebruiken voor toegang tot de bron-VM. Gebruik [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip) om een resource met een openbaar IP-adres te maken met de naam **myPublicIPdestinationVM** in **myResourceGroupNAT**. 

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPdestinationVM
  --sku standard
  --location westus
```

### <a name="create-an-nsg-for-destination-vm"></a>Een NSG maken voor de doel-VM

Omdat standaard open bare IP-adressen standaard ' veilig ' zijn, moet er een NSG worden gemaakt om de toegang tot de SSH mogelijk te maken.  Omdat de NAT-service stroom richting is, wordt deze NSG niet gebruikt voor uitgaand verkeer wanneer de NAT-gateway is geconfigureerd op hetzelfde subnet. Gebruik [AZ Network NSG Create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) om een NSG-resource met de naam **myNSGdestination** in **myResourceGroupNAT**te maken.

```azurecli-interactive
    az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGdestination
    --location westus
```

### <a name="expose-ssh-endpoint-on-destination-vm"></a>SSH-eind punt beschikbaar maken op de doel-VM

We maken een regel in het NSG voor SSH-toegang tot de doel-VM. Gebruik [AZ Network NSG Rule Create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) om een NSG-regel met de naam **SSH** te maken in de NSG met de naam **myNSGdestination** in **myResourceGroupNAT**.

```azurecli-interactive
    az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGdestination \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow 
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
```

### <a name="expose-http-endpoint-on-destination-vm"></a>HTTP-eind punt beschikbaar maken op de doel-VM

We maken een regel in de NSG voor HTTP-toegang tot de doel-VM. Gebruik [AZ Network NSG Rule Create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) om een NSG **-** regel met de naam http te maken in de NSG met de naam **myNSGdestination** in **myResourceGroupNAT**.

```azurecli-interactive
    az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGdestination \
    --priority 101 \
    --name http \
    --description "HTTP access" \
    --access allow 
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 80
```

### <a name="create-nic-for-destination-vm"></a>NIC maken voor bestemmings-VM

Maak een netwerk interface met [AZ Network NIC Create](/cli/azure/network/nic#az-network-nic-create) en koppel deze aan het open bare IP-adres **myPublicIPdestinationVM** en de netwerk beveiligings groep **myNSGdestination**. 

```azurecli-interactive
    az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicdestination \
    --vnet-name myVnetdestination \
    --subnet mySubnetdestination \
    --public-ip-address myPublicIPdestinationVM
    --network-security-group myNSGdestination
    --location westus
```

### <a name="create-a-destination-vm"></a>Een doel-VM maken

Maak de virtuele machine met [AZ VM Create](/cli/azure/vm#az-vm-create).  We genereren SSH-sleutels voor deze VM en slaan de persoonlijke sleutel op voor later gebruik.

 ```azurecli-interactive
    az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMdestination \
    --nics myNicdestination \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait
    --location westus
```
Terwijl de opdracht onmiddellijk wordt geretourneerd, kan het enkele minuten duren voordat de virtuele machine wordt geïmplementeerd.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Een webserver voorbereiden en payload testen op de doel-VM

Eerst moet het IP-adres van de doel-VM worden gedetecteerd.  Gebruik [AZ Network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show)om het open bare IP-adres van de doel-VM op te halen. 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPdestinationVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>Kopieer het open bare IP-adres en plak het in een Klad blok, zodat u het kunt gebruiken in de volgende stappen. Geef aan dat dit de virtuele doel machine is.

### <a name="sign-in-to-destination-vm"></a>Aanmelden bij de doel-VM

De SSH-referenties moeten worden opgeslagen in de Cloud shell vanuit de vorige bewerking.  Open een [Azure Cloud shell](https://shell.azure.com) in uw browser. Gebruik het IP-adres dat in de vorige stap is opgehaald om SSH naar de virtuele machine te gaan. 

```bash
ssh <ip-address-destination>
```

Kopieer en plak de volgende opdrachten zodra u bent aangemeld.  

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

Met deze opdrachten wordt uw virtuele machine bijgewerkt, wordt nginx geïnstalleerd en wordt een bestand van 100 KB gemaakt dat u kunt gebruiken om op te halen van de bron-VM met behulp van de NAT-service.

Sluit de SSH-sessie met de doel-VM.

## <a name="prepare-test-on-source-vm"></a>Test voorbereiden op de bron-VM

Eerst moet het IP-adres van de bron-VM worden gedetecteerd.  Gebruik [AZ Network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show)om het open bare IP-adres van de bron-VM op te halen. 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>Kopieer het open bare IP-adres en plak het in een Klad blok, zodat u het kunt gebruiken in de volgende stappen. Geef aan dat dit de virtuele bron machine is.

### <a name="log-into-source-vm"></a>Aanmelden bij de bron-VM

De SSH-referenties worden opgeslagen in Cloud shell. Open een nieuw tabblad voor [Azure Cloud shell](https://shell.azure.com) in uw browser.  Gebruik het IP-adres dat in de vorige stap is opgehaald om SSH naar de virtuele machine te gaan. 

```bash
ssh <ip-address-source>
```

Kopieer en plak de volgende opdrachten om het testen van de NAT-service voor te bereiden.

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

Hiermee wordt uw virtuele machine bijgewerkt, installeert u go, installeert u [een van github](https://github.com/rakyll/hey) en werkt u uw shell-omgeving bij.

U bent nu klaar om de NAT-service te testen.

## <a name="validate-nat-service"></a>NAT-service valideren

Terwijl u bent aangemeld bij de bron-VM, kunt u **krul** **gebruiken en zo aanvragen genereren voor het** doel-IP-adres.

Gebruik krul om het bestand van 100 KB op te halen.  Vervang **\<ip-Address-destination >** in het onderstaande voor beeld met het doel-IP-adres dat u eerder hebt gekopieerd.  De para meter **--output** geeft aan dat het opgehaalde bestand wordt verwijderd.

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

U kunt ook een reeks aanvragen genereren met behulp van **Hey**. Vervang opnieuw **\<ip-Address-destination >** door het doel-IP-adres dat u eerder hebt gekopieerd.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

Hiermee worden 100-aanvragen gegenereerd, 10 gelijktijdig, met een time-out van 30 seconden en zonder de TCP-verbinding opnieuw te gebruiken.  Elke aanvraag zal 100 KB ophalen.  Aan het einde van de uitvoering worden enkele statistieken gerapporteerd over hoe goed de NAT- **service is uitgevoerd** .

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [AZ Group delete](/cli/azure/group#az-group-delete) gebruiken om de resource groep en alle resources in te verwijderen wanneer u deze niet meer nodig hebt.

```azurecli-interactive 
  az group delete --name myResourceGroupNAT
```

## <a name="next-steps"></a>Volgende stappen
In deze zelf studie hebt u een NAT-gateway gemaakt, een bron-en doel-VM gemaakt en vervolgens de NAT-gateway getest. Ga verder met andere zelf studies voor de Azure NAT-service voor meer informatie over de Azure NAT-service.

U kunt ook de metrische gegevens in Azure Monitor bekijken om de werking van uw NAT-service te bekijken en problemen op te lossen, zoals de bron uitputting van de beschik bare SNAT-poorten.  Bron uitputting van de SNAT-poorten kan gemakkelijk worden verholpen door extra open bare IP-adres bronnen of open bare IP-prefix bronnen of beide toe te voegen.

> [!div class="nextstepaction"]

