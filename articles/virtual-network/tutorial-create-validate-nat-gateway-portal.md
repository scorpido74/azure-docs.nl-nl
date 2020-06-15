---
title: 'Zelfstudie: Een NAT-gateway maken en testen - Azure-portal'
titlesuffix: Azure Virtual Network NAT
description: In deze zelfstudie ziet u hoe u met behulp van de Azure Portal een NAT-gateway maakt en de NAT-service test
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumundD
Customer intent: I want to test a NAT Gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2020
ms.author: allensu
ms.openlocfilehash: d798725892a9586c17cd7023863fe5cf7df05cb6
ms.sourcegitcommit: 8e5b4e2207daee21a60e6581528401a96bfd3184
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/04/2020
ms.locfileid: "84417834"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-portal-and-test-the-nat-service"></a>Zelfstudie: Een NAT-gateway maken met behulp van de Azure-portal en de NAT-service testen

In deze zelfstudie maakt u een NAT-gateway om uitgaande connectiviteit te bieden voor virtuele machines in Azure. Als u de NAT-gateway wilt testen, implementeert u een virtuele bron- en doelmachine. U gaat de NAT-gateway testen door uitgaande verbindingen te maken met een openbaar IP-adres van de bron- naar de virtuele doelmachine.  In deze zelfstudie worden de bron en het doel in twee verschillende virtuele netwerken in dezelfde resourcegroep geïmplementeerd om het eenvoudiger te maken.

Indien gewenst, kunt u deze stappen uitvoeren met behulp van de [Azure CLI](tutorial-create-validate-nat-gateway-cli.md) of [Azure PowerShell](tutorial-create-validate-nat-gateway-powershell.md) in plaats van met de portal.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="prepare-the-source-for-outbound-traffic"></a>De bron voorbereiden voor uitgaand verkeer

We begeleiden u bij het configureren van een volledige testomgeving en de uitvoering van de tests zelf in de volgende stappen. We beginnen met de bron, die gebruikmaakt van de NAT-gateway-resource die we in de latere stappen zullen maken.

## <a name="virtual-network-and-parameters"></a>Virtueel netwerk en parameters

Voordat u een virtuele machine implementeert en uw NAT-gateway kunt gebruiken, moet u de resourcegroep en het virtuele netwerk maken.

In deze sectie moet u de volgende parameters in de stappen vervangen door onderstaande informatie:

| Parameter                   | Waarde                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupNAT |
| **\<virtual-network-name>** | myVNetsource          |
| **\<region-name>**          | VS - oost 2      |
| **\<IPv4-address-space>**   | 192.168.0.0/16          |
| **\<subnet-name>**          | mySubnetsource        |
| **\<subnet-address-range>** | 192.168.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-source-virtual-machine"></a>Virtuele bronmachine maken

We gaan nu een VM maken voor het gebruik van de NAT-service. Deze VM heeft een openbaar IP-adres dat kan worden gebruikt als een openbaar IP-adres op exemplaarniveau om toegang te krijgen tot de virtuele machine. De NAT-service neemt stroomrichting waar, en vervangt de standaard Internetdoel in uw subnet. Het openbare IP-adres van de virtuele machine wordt niet gebruikt voor uitgaande verbindingen.

Als u de NAT-gateway wilt testen, wijzen we een openbare IP-adresresource toe als een openbaar IP op exemplaarniveau voor externe toegang tot deze VM. Dit adres wordt alleen gebruikt voor toegang tot de test.  We laten u zien hoe de NAT-service prioriteit krijgt boven andere uitgaande opties.

U kunt deze virtuele machine ook maken zonder een openbaar IP-adres en een andere virtuele machine maken om te gebruiken als jumpbox zonder openbare IP als oefening.

1. Selecteer in de linkerbovenhoek van de portal **Een resource maken** > **Berekening** > **Ubuntu-server 18,04 LTS**, of zoek naar **Ubuntu-server 18,04 LTS** in de zoekactie voor Marketplace.

2. In **Een virtuele machine maken** voert u de volgende waarden in of selecteert u deze op het tabblad **Basisinformatie**:
   - **Abonnement** > **Resourcegroep**: Selecteer **myResourceGroupNAT**.
   - **Instantiedetails** > **Naam van virtuele machine**: voer **MmyVMsource** in.
   - **Instantiedetails** > **Regio** > selecteer **VS - Oost 2**.
   - **Beheerdersaccount** > **Verificatie voer in** : Selecteer **Wachtwoord**.
   - **Beheerdersaccount** > Voer de **Gebruikersnaam**, **Wachtwoord** in en **Bevestig het wachtwoord**.
   - **Regels voor binnenkomende poort** > **Openbare poorten voor inkomend verkeer**: Selecteer **Geselecteerde poorten toestaan**.
   - **Regels voor binnenkomende poort** > **Binnenkomende poorten selecteren**: **SSH (22)** selecteren
   - Selecteer het tabblad **Netwerken** of selecteer **Volgende: Schijven** en vervolgens **Volgende: Netwerken**.

3. Zorg ervoor dat de volgende opties zijn geselecteerd in het tabblad **Netwerken**:
   - **Virtueel netwerk**: **myVnetsource**
   - **Subnet**: **mySubnetsource**
   - **Openbaar IP-adres** > selecteer **Nieuwe maken**.  Voer in het venster **Openbaar IP-adres maken** **myPublicIPsourceVM** in het veld **Naam** in. Selecteer **Standaard** voor de **SKU**. Laat de overige staan en klik op **OK**.
   - **NIC-netwerkbeveiligingsgroep**: Selecteer **Basic**.
   - **Openbare binnenkomende poorten**: Selecteer **Geselecteerde poorten toestaan**.
   - **Binnenkomende poorten selecteren**: Bevestigen dar **SSH** is geselecteerd.

4. In het tabblad **Beheer**, onder **Bewaking**, stelt u **Diagnostische gegevens over opstarten** in op **Uit**.

5. Selecteer **Controleren + maken**.

6. Controleer de instellingen en klik op **Maken**.

## <a name="create-the-nat-gateway"></a>De NAT-gateway maken

U kunt een of meer openbare IP-adresresources, openbare IP-voorvoegsels of beide met NAT-gateway gebruiken. We voegen een openbare IP-resource, een openbaar IP-voorvoegsel en een NAT-gatewayresource toe.

In deze sectie wordt beschreven hoe u de volgende onderdelen van de NAT-service met de NAT-gatewayresource kunt maken en configureren:
  - Een openbare IP-adresgroep en een openbaar IP-voorvoegsel dat moet worden gebruikt voor uitgaande stromen die worden vertaald door de NAT-gatewayresource.
  - Wijzig de time-out voor inactiviteit van de standaardwaarde van 4 minuten naar 10 minuten.

### <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken

1. Selecteer in de linkerbovenhoek van de portal **Een resource maken** > **Netwerken** > **Openbaar IP-adres**, of zoek naar **Openbaar IP-adres** in de zoekactie voor Marketplace. 

2. Typ of selecteer in **Openbaar IP-adres maken** deze informatie:

    | Instelling | Waarde |
    | ------- | ----- |
    | IP-versie | Selecteer **IPv4**.
    | SKU | selecteer **Standaard**.
    | Naam | Voer **myPublicIPsource** in. |
    | Abonnement | Selecteer uw abonnement.|
    | Resourcegroep | Selecteer **myResourceGroupNAT**. |
    | Locatie | Selecteer **VS - oost 2**.|

3. Laat de overige standaardwaarden staan en selecteer **Maken**.

### <a name="create-a-public-ip-prefix"></a>Een openbaar IP-voorvoegsel maken

1. Selecteer in de linkerbovenhoek van de portal **Een resource maken** > **Netwerken** > **Openbaar IP-voorvoegsel**, of zoek naar **Openbaar IP-voorvoegsel** in de zoekactie voor Marketplace.

2. In **Een openbaar IP-voorvoegsel maken** voert u de volgende waarden in of selecteert u deze op het tabblad **Basisinformatie**:
   - **Abonnement** > **Resourcegroep**: Selecteer **myResourceGroupNAT**>
   - **Instantiedetails** > **Naam**: voer **myPublicIPprefixsource** in.
   - **Instantiegegevens** > **Regio**: Selecteer **VS - oost 2**.
   - **Instantiegegevens** > **Voorvoegselgrootte**: Selecteer **/31 (2 adressen)**

3. Laat de overige standaardwaarden staan en selecteer **Beoordelen en maken**.

4. Controleer de instellingen en selecteer vervolgens **Maken**.


### <a name="create-a-nat-gateway-resource"></a>Een NAT-gatewayresource maken

1. Selecteer in de linkerbovenhoek van de portal **Een resource maken** > **Netwerken** > **NAT-gateway**, of zoek naar **NAT-gateway** in de zoekactie voor Marketplace.

2. In **Network Address Translation (NAT)-gateway maken**, typt of selecteert u de volgende waarden op het tabblad **Basisinstellingen**:
   - **Abonnement** > **Resourcegroep**: Selecteer **myResourceGroupNAT**.
   - **Instantiegegevens** > **Naam van gateway**: Voer **myNATgateway** in.
   - **Instantiegegevens** > **Regio**: Selecteer **VS - oost 2**.
   - **Instantiegegevens** > **Time-out voor inactiviteit (minuten)** : Voer **10** in.
   - Selecteer het tabblad **Openbaar IP** of selecteer **Volgende: Openbaar IP**.

3. Typ of selecteer de volgende waarden in het tabblad **Openbaar IP**:
   - **Openbare IP-adressen**: Selecteer **myPublicIPsource** in.
   - **Voorvoegsels voor openbare IP**: Selecteer **myPublicIPprefixsource**.
   - Selecteer het tabblad **Subnet** of selecteer **Volgende: Subnet**.

4. Typ of selecteer de volgende waarden in het tabblad **Subnet**:
   - **Virtueel netwerk**: Selecteer **myResourceGroupNAT** > **myVnetsource**.
   - **Subnetnaam**: Selecteert het vakje naast **mySubnetsource**.

5. Selecteer **Controleren + maken**.

6. Controleer de instellingen en selecteer vervolgens **Maken**.

Al het uitgaande verkeer naar Internetdoelen maakt nu gebruik van de NAT-service.  Het is niet nodig om een UDR te configureren.


## <a name="prepare-destination-for-outbound-traffic"></a>Doel voorbereiden voor uitgaand verkeer

We gaan nu een doel maken voor het uitgaande verkeer dat door de NAT-service is vertaald, zodat u het kunt testen.


## <a name="virtual-network-and-parameters-for-destination"></a>Virtueel netwerk en parameters voor doel

Voordat u een virtuele machine voor het doel implementeert, moet u een virtueel netwerk maken waarin de virtuele doelmachine zich kan bevinden. Hieronder volgen dezelfde stappen als voor de bron-VM met enkele kleine wijzigingen om het doeleindpunt zichtbaar te maken.

In deze sectie moet u de volgende parameters in de stappen vervangen door onderstaande informatie:

| Parameter                   | Waarde                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupNAT |
| **\<virtual-network-name>** | myVNetdestination          |
| **\<region-name>**          | VS - oost 2      |
| **\<IPv4-address-space>**   | 10.1.0.0/16          |
| **\<subnet-name>**          | mySubnetdestination        |
| **\<subnet-address-range>** | 10.1.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-destination-virtual-machine"></a>Doel-VM maken

1. Selecteer in de linkerbovenhoek van de portal **Een resource maken** > **Berekening** > **Ubuntu-server 18,04 LTS**, of zoek naar **Ubuntu-server 18,04 LTS** in de zoekactie voor Marketplace.

2. In **Een virtuele machine maken** voert u de volgende waarden in of selecteert u deze op het tabblad **Basisinformatie**:
   - **Abonnement** > **Resourcegroep**: Selecteer **myResourceGroupNAT**.
   - **Instantiedetails** > **Naam van virtuele machine**: voer **myVMdestination** in.
   - **Instantiedetails** > **Regio** > selecteer **VS - Oost 2**.
   - **Beheerdersaccount** > **Verificatie voer in** : Selecteer **Wachtwoord**.
   - **Beheerdersaccount** > Voer de **Gebruikersnaam**, **Wachtwoord** in en **Bevestig het wachtwoord**.
   - **Regels voor binnenkomende poort** > **Openbare poorten voor inkomend verkeer**: Selecteer **Geselecteerde poorten toestaan**.
   - **Regels voor binnenkomende poort** > **Binnenkomende poorten selecteren**: Selecteer **SSH (22)** en **HTTP (80)** .
   - Selecteer het tabblad **Netwerken** of selecteer **Volgende: Schijven** en vervolgens **Volgende: Netwerken**.

3. Zorg ervoor dat de volgende opties zijn geselecteerd in het tabblad **Netwerken**:
   - **Virtueel netwerk**: **myVnetdestination**
   - **Subnet**: **mySubnetdestination**
   - **Openbaar IP-adres** > selecteer **Nieuwe maken**.  Voer in het venster **Openbaar IP-adres maken** **myPublicIPdestinationVM** in het veld **Naam** in. Selecteer **Standaard** voor **SKU**. Laat de overige staan en klik op **OK**.
   - **NIC-netwerkbeveiligingsgroep**: Selecteer **Basic**.
   - **Openbare binnenkomende poorten**: Selecteer **Geselecteerde poorten toestaan**.
   - **Binnenkomende poorten selecteren**: Bevestig dat **SSH** en **HTTP** is geselecteerd.

4. In het tabblad **Beheer**, onder **Bewaking**, stelt u **Diagnostische gegevens over opstarten** in op **Uit**.

5. Selecteer **Controleren + maken**.

6. Controleer de instellingen en selecteer vervolgens **Maken**.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Een webserver voorbereiden en payload testen op de doel-VM

Eerst moet het IP-adres van de doel-VM worden gedetecteerd. 

1. Selecteer aan de linkerkant van de portal de optie **Resourcegroepen**.
2. Selecteer **myResourceGroupNAT**.
3. Selecteer **myVMdestination**.
4. Kopieer in **Overzicht** de **Openbare IP-adres**-waarde en plak deze in Kladblok, zodat u deze kunt gebruiken om toegang te krijgen tot de virtuele machine.

>[!IMPORTANT]
>Kopieer het openbare IP-adres en plak het in een Kladblok, zodat u het kunt gebruiken in de volgende stappen. Geef aan dat dit de virtuele doelmachine is.

### <a name="sign-in-to-destination-vm"></a>Aanmelden bij de doel-VM

Open een [Azure Cloud Shell](https://shell.azure.com) in uw browser. Gebruik het IP-adres dat in de vorige stap is opgehaald om SSH naar de virtuele machine uit te voeren.

```azurecli-interactive
ssh <username>@<ip-address-destination>
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

Met deze opdrachten wordt uw virtuele machine bijgewerkt, nginx geïnstalleerd en een bestand van 100 KB gemaakt. Dit bestand wordt opgehaald van de bron-VM met behulp van de NAT-service.

Sluit de SSH-sessie met de doel-VM.

## <a name="prepare-test-on-source-vm"></a>Test voorbereiden op de bron-VM

Eerst moet het IP-adres van de bron-VM worden gedetecteerd.

1. Selecteer aan de linkerkant van de portal de optie **Resourcegroepen**.
2. Selecteer **myResourceGroupNAT**.
3. Selecteer **myVMsource**.
4. Kopieer in **Overzicht** de **Openbare IP-adres**-waarde en plak deze in Kladblok, zodat u deze kunt gebruiken om toegang te krijgen tot de virtuele machine.

>[!IMPORTANT]
>Kopieer het openbare IP-adres en plak het in een Kladblok, zodat u het kunt gebruiken in de volgende stappen. Geef aan dat dit de virtuele bronmachine is.

### <a name="log-into-source-vm"></a>Aanmelden bij de bron-VM

Open een nieuw tabblad voor [Azure Cloud Shell](https://shell.azure.com) in uw browser.  Gebruik het IP-adres dat in de vorige stap is opgehaald om SSH naar de virtuele machine uit te voeren. 

```azurecli-interactive
ssh <username>@<ip-address-source>
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

Met deze opdracht wordt uw virtuele machine bijgewerkt, installeert u go, installeert u [hey](https://github.com/rakyll/hey) van GitHub en werkt u uw shell-omgeving bij.

U bent nu klaar om de NAT-service te testen.

## <a name="validate-nat-service"></a>NAT-service valideren

Terwijl u bent aangemeld bij de bron-VM, kunt u **curl** en **hey** om aanvragen te genereren voor het doel-IP-adres.

Gebruik curl om het bestand van 100 KB op te halen.  Vervang **\<ip-address-destination>** in het onderstaande voorbeeld met het doel-IP-adres dat u eerder hebt gekopieerd.  De parameter **--output** geeft aan dat het opgehaalde bestand wordt verwijderd.

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

U kunt ook een reeks aanvragen genereren met **hey**. Vervang nogmaals **\<ip-address-destination>** met het doel-IP-adres dat u eerder hebt gekopieerd.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

Met deze opdracht worden 100 aanvragen gegenereerd, 10 gelijktijdig, met een time-out van 30 seconden en zonder de TCP-verbinding opnieuw te gebruiken.  Elke aanvraag zal 100 KB ophalen.  Aan het einde van de uitvoering zal **hey** een aantal statistieken rapporteren over de juiste werking van de NAT-service.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de resourcegroep, de NAT-gateway en alle gerelateerde resources verwijderen als u deze niet meer nodig hebt. Selecteer de resourcegroep **myResourceGroupNAT** met de NAT-gateway en selecteer vervolgens **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u een NAT-gateway gemaakt, een bron- en doel-VM gemaakt en vervolgens de NAT-gateway getest.

Controleer de metrische gegevens in Azure Monitor om uw NAT-service weer te geven. Problemen vaststellen, zoals de resource-uitputting van de beschikbare SNAT-poorten.  Resource-uitputting van de SNAT-poorten kan gemakkelijk worden verholpen door extra openbare IP-adresresources of openbare IP-voorvoegselresources of beide toe te voegen.

- Meer informatie over [Virtual Network NAT](./nat-overview.md)
- Meer informatie over [NAT-gatewayresource](./nat-gateway-resource.md).
- Quickstart voor het implementeren van [NAT-gatewayresource met Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Quickstart voor het implementeren van [NAT-gatewayresource met behulp van Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Quickstart voor het implementeren van [NAT-gatewayresource met Azure Portal](./quickstart-create-nat-gateway-portal.md).

> [!div class="nextstepaction"]

