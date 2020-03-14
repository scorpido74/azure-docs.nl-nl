---
title: 'Zelf studie: een NAT-gateway maken en testen-Azure Portal'
titlesuffix: Azure Virtual Network NAT
description: Deze zelf studie laat zien hoe u een NAT-gateway maakt met behulp van de Azure Portal en de NAT-service test
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumundD
Customer intent: I want to test a NAT Gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2020
ms.author: allensu
ms.openlocfilehash: 3468dd72ff47c895ee4df6ed4308aabab92c33f1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79216953"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-portal-and-test-the-nat-service"></a>Zelf studie: een NAT-gateway maken met behulp van de Azure Portal en de NAT-service testen

In deze zelf studie maakt u een NAT-gateway om uitgaande connectiviteit te bieden voor virtuele machines in Azure. Als u de NAT-gateway wilt testen, implementeert u een virtuele bron-en doel machine. U gaat de NAT-gateway testen door uitgaande verbindingen te maken met een openbaar IP-adres van de bron naar de virtuele doel machine.  In deze zelf studie worden de bron en bestemming in twee verschillende virtuele netwerken in dezelfde resource groep geïmplementeerd voor eenvoud.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="prepare-the-source-for-outbound-traffic"></a>De bron voorbereiden voor uitgaand verkeer

We begeleiden u bij het configureren van een volledige test omgeving en de uitvoering van de tests zelf in de volgende stappen. We beginnen met de bron, die gebruikmaakt van de NAT-gateway resource die u in latere stappen hebt gemaakt.

## <a name="virtual-network-and-parameters"></a>Virtueel netwerk en para meters

Voordat u een virtuele machine implementeert en uw NAT-gateway kunt gebruiken, moet u de resource groep en het virtuele netwerk maken.

In deze sectie moet u de volgende para meters in de stappen vervangen door de onderstaande informatie:

| Parameter                   | Waarde                |
|-----------------------------|----------------------|
| **\<resource-group-name >**  | myResourceGroupNAT |
| **\<virtuele-netwerk naam >** | myVNetsource          |
| **\<regio-naam >**          | VS - oost 2      |
| **> \<IPv4-adres ruimte**   | 192.168.0.0 \ 16          |
| **\<subnet naam >**          | mySubnetsource        |
| **\<subnet-adres bereik >** | 192.168.0.0 \ 24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-source-virtual-machine"></a>Virtuele bron machine maken

We gaan nu een VM maken voor het gebruik van de NAT-service. Deze VM heeft een openbaar IP-adres dat kan worden gebruikt als een openbaar IP-adres op exemplaar niveau om toegang te krijgen tot de virtuele machine. De NAT-service is stroom richting, en vervangt de standaard Internet bestemming in uw subnet. Het open bare IP-adres van de virtuele machine wordt niet gebruikt voor uitgaande verbindingen.

Als u de NAT-gateway wilt testen, wijzen we een open bare IP-adres resource toe als een openbaar IP op exemplaar niveau voor toegang tot deze VM vanaf buiten. Dit adres wordt alleen gebruikt voor toegang tot de test.  We laten u zien hoe de NAT-service prioriteit krijgt boven andere uitgaande opties.

U kunt deze virtuele machine ook maken zonder een openbaar IP-adres en een andere virtuele machine maken om te gebruiken als JumpBox zonder open bare IP als oefening.

1. Selecteer in de linkerbovenhoek van de Portal de optie **een resource maken** > **Compute** > **Ubuntu Server 18,04 LTS**, of zoek naar **Ubuntu Server 18,04 LTS** in de zoek functie voor Marketplace.

2. In **een virtuele machine maken**, typt of selecteert u de volgende waarden op het tabblad **basis beginselen** :
   -  > **resource groep** **abonnement** : Selecteer **myResourceGroupNAT**.
   - **Exemplaar Details** > **naam van de virtuele machine**: Voer **myVMsource**in.
   - **Exemplaar Details** > **regio** > Selecteer **VS Oost 2**.
   - **Beheerders account** > **verificatie**: Selecteer **wacht woord**.
   - **Beheerders account** > Geef de **gebruikers naam**, het **wacht woord**en de **wachtwoord** gegevens op.
   - **Binnenkomende poort regels** > **open bare binnenkomende poorten**: Selecteer **geselecteerde poorten toestaan**.
   - **Binnenkomende poort regels** > **binnenkomende poorten selecteren**: Selecteer **SSH (22)**
   - Selecteer het tabblad **Netwerken** of selecteer **Volgende: Schijven** en vervolgens **Volgende: Netwerken**.

3. Controleer op het tabblad **netwerken** of het volgende is geselecteerd:
   - **Virtueel netwerk**: **myVnetsource**
   - **Subnet**: **mySubnetsource**
   - **Open bare IP-** > Selecteer **nieuwe maken**.  Voer in het venster **openbaar IP-adres maken** **myPublicIPsourceVM** in het veld **naam** in. Selecteer **standaard** voor de **SKU**. Laat de rest op de standaard waarden staan en klik op **OK**.
   - **NIC-netwerk beveiligings groep**: Selecteer **basis**.
   - **Open bare binnenkomende poorten**: Selecteer **geselecteerde poorten toestaan**.
   - **Selecteer binnenkomende poorten**: Bevestig **SSH** is geselecteerd.

4. Stel op het tabblad **beheer** , onder **bewaking**, **Diagnostische gegevens over opstarten** in op **uit**.

5. Selecteer **Controleren + maken**.

6. Controleer de instellingen en klik op **maken**.

## <a name="create-the-nat-gateway"></a>De NAT-gateway maken

U kunt een of meer open bare IP-adres bronnen, open bare IP-voor voegsels of beide met NAT-gateway gebruiken. We voegen een open bare IP-resource, een openbaar IP-voor voegsel en een NAT-gateway bron toe.

In deze sectie wordt beschreven hoe u de volgende onderdelen van de NAT-service kunt maken en configureren met behulp van de NAT-gateway resource:
  - Een open bare IP-adres groep en een openbaar IP-voor voegsel dat moet worden gebruikt voor uitgaande stromen die worden vertaald door de NAT-gateway resource.
  - Wijzig de time-out voor inactiviteit van de standaard waarde van 4 minuten in 10 minuten.

### <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken

1. Selecteer in de linkerbovenhoek van de Portal de optie **een resource maken** > **netwerk** > **open bare IP-adres**, of zoek naar een **openbaar IP-adres** in de zoek opdracht voor Marketplace. 

2. Voer in **openbaar IP-adres maken**de volgende gegevens in of Selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | IP-versie | Selecteer **IPv4**.
    | SKU | selecteer **Standaard**.
    | Naam | Voer **myPublicIPsource**in. |
    | Abonnement | Selecteer uw abonnement.|
    | Resourcegroep | Selecteer **myResourceGroupNAT**. |
    | Locatie | Selecteer **VS - oost 2**.|

3. Laat de overige standaardwaarden staan en selecteer **Maken**.

### <a name="create-a-public-ip-prefix"></a>Een openbaar IP-voor voegsel maken

1. Selecteer in de linkerbovenhoek van de Portal de optie **een resource maken** > **netwerk** > **openbaar IP-voor voegsel**of zoek naar **openbaar IP-voor voegsel** in de zoek opdracht voor Marketplace.

2. Typ of Selecteer in het **voor voegsel een openbaar IP-adres maken**de volgende waarden op het tabblad **basis beginselen** :
   -  > **resource groep** **abonnement** : Selecteer **myResourceGroupNAT**>
   - **Exemplaar details** > **naam**: Voer **myPublicIPprefixsource**in.
   - **Exemplaar details** > **regio**: Selecteer **VS Oost 2**.
   - **Exemplaar gegevens** > **grootte van voor voegsel**: Select **/31 (2 adressen)**

3. Laat de overige standaard instellingen ongewijzigd en selecteer **controleren + maken**.

4. Controleer de instellingen en selecteer vervolgens **Maken**.


### <a name="create-a-nat-gateway-resource"></a>Een NAT-gateway resource maken

1. Selecteer in de linkerbovenhoek van de Portal de optie **een resource maken** > **netwerk** > NAT- **Gateway**, of zoek naar de **NAT-gateway** in de zoek functie voor Marketplace.

2. In **Create Network Address Translation (NAT)-gateway**, typt of selecteert u de volgende waarden op het tabblad **basis beginselen** :
   -  > **resource groep** **abonnement** : Selecteer **myResourceGroupNAT**.
   - **Exemplaar details** > **naam**van de NAT-gateway: Voer **myNATgateway**in.
   - **Exemplaar details** > **regio**: Selecteer **VS Oost 2**.
   - **Exemplaar gegevens** > **time-out voor inactiviteit (minuten)** : Voer **10**in.
   - Selecteer het tabblad **openbaar IP-adres** of selecteer **volgende: openbaar IP-adres**.

3. Op het tabblad **openbaar IP-adres** voert of selecteert u de volgende waarden:
   - **Open bare IP-adressen**: Selecteer **myPublicIPsource**.
   - Voor **voegsels van open bare IP-adressen**: Selecteer **myPublicIPprefixsource**.
   - Selecteer het tabblad **subnet** of selecteer **volgende: subnet**.

4. Voer op het tabblad **subnet** de volgende waarden in of Selecteer deze:
   - **Virtual Network**: selecteer **myResourceGroupNAT** > **myVnetsource**.
   - **Subnetnaam**: Schakel het selectie vakje in naast **mySubnetsource**.

5. Selecteer **Controleren + maken**.

6. Controleer de instellingen en selecteer vervolgens **Maken**.

Al het uitgaande verkeer naar Internet bestemmingen maakt nu gebruik van de NAT-service.  Het is niet nodig om een UDR te configureren.


## <a name="prepare-destination-for-outbound-traffic"></a>Bestemming voorbereiden voor uitgaand verkeer

We gaan nu een bestemming maken voor het uitgaande verkeer dat door de NAT-service is vertaald, zodat u het kunt testen.


## <a name="virtual-network-and-parameters-for-destination"></a>Virtueel netwerk en de para meters voor bestemming

Voordat u een virtuele machine voor de bestemming implementeert, moet u een virtueel netwerk maken waarin de virtuele doel machine zich kan bevinden. Hieronder volgen dezelfde stappen als voor de bron-VM met enkele kleine wijzigingen om het bestemmings eindpunt zichtbaar te maken.

In deze sectie moet u de volgende para meters in de stappen vervangen door de onderstaande informatie:

| Parameter                   | Waarde                |
|-----------------------------|----------------------|
| **\<resource-group-name >**  | myResourceGroupNAT |
| **\<virtuele-netwerk naam >** | myVNetdestination          |
| **\<regio-naam >**          | VS - oost 2      |
| **> \<IPv4-adres ruimte**   | 192.168.0.0 \ 16          |
| **\<subnet naam >**          | mySubnetdestination        |
| **\<subnet-adres bereik >** | 192.168.0.0 \ 24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-destination-virtual-machine"></a>Virtuele doel machine maken

1. Selecteer in de linkerbovenhoek van de Portal de optie **een resource maken** > **Compute** > **Ubuntu Server 18,04 LTS**, of zoek naar **Ubuntu Server 18,04 LTS** in de zoek functie voor Marketplace.

2. In **een virtuele machine maken**, typt of selecteert u de volgende waarden op het tabblad **basis beginselen** :
   -  > **resource groep** **abonnement** : Selecteer **myResourceGroupNAT**.
   - **Exemplaar Details** > **naam van de virtuele machine**: Voer **myVMdestination**in.
   - **Exemplaar Details** > **regio** > Selecteer **VS Oost 2**.
   - **Beheerders account** > **verificatie**: Selecteer **wacht woord**.
   - **Beheerders account** > Geef de **gebruikers naam**, het **wacht woord**en de **wachtwoord** gegevens op.
   - **Binnenkomende poort regels** > **open bare binnenkomende poorten**: Selecteer **geselecteerde poorten toestaan**.
   - **Binnenkomende poort regels** > **binnenkomende poorten selecteren**: Selecteer **SSH (22)** en **http (80)** .
   - Selecteer het tabblad **Netwerken** of selecteer **Volgende: Schijven** en vervolgens **Volgende: Netwerken**.

3. Controleer op het tabblad **netwerken** of het volgende is geselecteerd:
   - **Virtueel netwerk**: **myVnetdestination**
   - **Subnet**: **mySubnetdestination**
   - **Open bare IP-** > Selecteer **nieuwe maken**.  Voer in het venster **openbaar IP-adres maken** **myPublicIPdestinationVM** in het veld **naam** in. Selecteer **standaard** voor **SKU**. Laat de rest op de standaard waarden staan en klik op **OK**.
   - **NIC-netwerk beveiligings groep**: Selecteer **basis**.
   - **Open bare binnenkomende poorten**: Selecteer **geselecteerde poorten toestaan**.
   - **Selecteer binnenkomende poorten**: Bevestig **SSH** en **http** is geselecteerd.

4. Stel op het tabblad **beheer** , onder **bewaking**, **Diagnostische gegevens over opstarten** in op **uit**.

5. Selecteer **Controleren + maken**.

6. Controleer de instellingen en selecteer vervolgens **Maken**.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Een webserver voorbereiden en payload testen op de doel-VM

Eerst moet het IP-adres van de doel-VM worden gedetecteerd. 

1. Selecteer op de linkerkant van de portal **resource groepen**.
2. Selecteer **myResourceGroupNAT**.
3. Selecteer **myVMdestination**.
4. Kopieer in het **overzicht**de waarde voor het **open bare IP-adres** en plak deze in Klad blok, zodat u deze kunt gebruiken om toegang te krijgen tot de virtuele machine.

>[!IMPORTANT]
>Kopieer het open bare IP-adres en plak het in een Klad blok, zodat u het kunt gebruiken in de volgende stappen. Geef aan dat dit de virtuele doel machine is.

### <a name="sign-in-to-destination-vm"></a>Aanmelden bij de doel-VM

Open een [Azure Cloud shell](https://shell.azure.com) in uw browser. Gebruik het IP-adres dat in de vorige stap is opgehaald om SSH naar de virtuele machine te gaan.

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

Met deze opdrachten wordt uw virtuele machine bijgewerkt, wordt nginx geïnstalleerd en wordt een bestand van 100 KB gemaakt. Dit bestand wordt opgehaald van de bron-VM met behulp van de NAT-service.

Sluit de SSH-sessie met de doel-VM.

## <a name="prepare-test-on-source-vm"></a>Test voorbereiden op de bron-VM

Eerst moet het IP-adres van de bron-VM worden gedetecteerd.

1. Selecteer op de linkerkant van de portal **resource groepen**.
2. Selecteer **myResourceGroupNAT**.
3. Selecteer **myVMsource**.
4. Kopieer in het **overzicht**de waarde voor het **open bare IP-adres** en plak deze in Klad blok, zodat u deze kunt gebruiken om toegang te krijgen tot de virtuele machine.

>[!IMPORTANT]
>Kopieer het open bare IP-adres en plak het in een Klad blok, zodat u het kunt gebruiken in de volgende stappen. Geef aan dat dit de virtuele bron machine is.

### <a name="log-into-source-vm"></a>Aanmelden bij de bron-VM

Open een nieuw tabblad voor [Azure Cloud shell](https://shell.azure.com) in uw browser.  Gebruik het IP-adres dat in de vorige stap is opgehaald om SSH naar de virtuele machine te gaan. 

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

Met deze opdracht werkt u uw virtuele machine bij, installeert u go, installeert u [een van github](https://github.com/rakyll/hey) en werkt u uw shell-omgeving bij.

U bent nu klaar om de NAT-service te testen.

## <a name="validate-nat-service"></a>NAT-service valideren

Terwijl u bent aangemeld bij de bron-VM, kunt u **krul** **gebruiken en zo aanvragen genereren voor het** doel-IP-adres.

Gebruik krul om het bestand 100-KBytes op te halen.  Vervang **\<IP-adres-doel >** in het onderstaande voor beeld met het doel-IP-adres dat u eerder hebt gekopieerd.  De para meter **--output** geeft aan dat het opgehaalde bestand wordt verwijderd.

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

U kunt ook een reeks aanvragen genereren met behulp van **Hey**. Vervang opnieuw **\<IP-adres-doel >** door het doel-IP-adres dat u eerder hebt gekopieerd.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

Met deze opdracht worden 100-aanvragen gegenereerd, 10 gelijktijdig, met een time-out van 30 seconden en zonder de TCP-verbinding opnieuw te gebruiken.  Elke aanvraag zal 100 KB ophalen.  Aan het einde van de uitvoering worden enkele statistieken gerapporteerd over hoe goed de **NAT-service** is.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze niet meer nodig hebt, verwijdert u de resource groep, de NAT-gateway en alle gerelateerde resources. Selecteer de **myResourceGroupNAT** van de resource groep die de NAT-gateway bevat en selecteer vervolgens **verwijderen**.

## <a name="next-steps"></a>Volgende stappen
In deze zelf studie hebt u een NAT-gateway gemaakt, een bron-en doel-VM gemaakt en vervolgens de NAT-gateway getest.

Controleer de metrische gegevens in Azure Monitor om uw NAT-service weer te geven. Problemen vaststellen, zoals de bron uitputting van de beschik bare SNAT-poorten.  Bron uitputting van de SNAT-poorten kan gemakkelijk worden verholpen door extra open bare IP-adres bronnen of open bare IP-prefix bronnen of beide toe te voegen.

- Meer informatie over [Virtual Network NAT](./nat-overview.md)
- Meer informatie over de [NAT gateway-resource](./nat-gateway-resource.md).
- Quick start voor het implementeren van [NAT-gateway resource met behulp van Azure cli](./quickstart-create-nat-gateway-cli.md).
- Quick start voor het implementeren van [NAT-gateway resource met behulp van Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Quick start voor het implementeren van [NAT-gateway resource met behulp van Azure Portal](./quickstart-create-nat-gateway-portal.md).

> [!div class="nextstepaction"]

