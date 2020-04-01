---
title: 'Zelfstudie: Een NAT-gateway maken en testen - Azure-portal'
titlesuffix: Azure Virtual Network NAT
description: In deze zelfstudie ziet u hoe u een NAT-gateway maakt met de Azure-portal en de NAT-service test
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
ms.openlocfilehash: 7c26487018b9632b5780dcd7c56ab36f286f8cac
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80059998"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-portal-and-test-the-nat-service"></a>Zelfstudie: Een NAT-gateway maken met de Azure-portal en de NAT-service testen

In deze zelfstudie maakt u een NAT-gateway om uitgaande connectiviteit te bieden voor virtuele machines in Azure. Als u de NAT-gateway wilt testen, implementeert u een virtuele bron- en doelmachine. U test de NAT-gateway door uitgaande verbindingen te maken met een openbaar IP-adres van de bron naar de virtuele bestemmingsmachine.  Deze zelfstudie implementeert bron en bestemming in twee verschillende virtuele netwerken in dezelfde brongroep voor alleen eenvoud.

Indien gewenst, kunt u deze stappen uitvoeren met behulp van de [Azure CLI](tutorial-create-validate-nat-gateway-cli.md) of [Azure PowerShell](tutorial-create-validate-nat-gateway-powershell.md) in plaats van met de portal.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij [Azure Portal](https://portal.azure.com).

## <a name="prepare-the-source-for-outbound-traffic"></a>De bron voorbereiden op uitgaand verkeer

Wij begeleiden u bij de configuratie van een volledige testomgeving en de uitvoering van de tests zelf in de volgende stappen. We beginnen met de bron, die de NAT-gatewaybron gebruikt die we in latere stappen maken.

## <a name="virtual-network-and-parameters"></a>Virtueel netwerk en parameters

Voordat u een VM implementeert en uw NAT-gateway gebruiken, moeten we de brongroep en het virtuele netwerk maken.

In deze sectie moet u de volgende parameters in de stappen vervangen door de onderstaande informatie:

| Parameter                   | Waarde                |
|-----------------------------|----------------------|
| **\<resourcegroepnaam>**  | myResourceGroupNAT |
| **\<>van de naam van het virtuele netwerk** | myVNetsource          |
| **\<regionaam>**          | VS - oost 2      |
| **\<IPv4-adresruimte>**   | 192.168.0.0\16          |
| **\<subnetnaam>**          | mySubnetsource        |
| **\<subnet-adresbereik>** | 192.168.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-source-virtual-machine"></a>Bronvirtuele machine maken

We maken nu een VM om de NAT-service te gebruiken. Deze VM heeft een openbaar IP-adres om te gebruiken als openbaar IP op instantie, zodat u toegang hebt tot de VM. NAT-service is de stroomrichting bewust en vervangt de standaardinternetbestemming in uw subnet. Het openbare IP-adres van de VM wordt niet gebruikt voor uitgaande verbindingen.

Als u de NAT-gateway wilt testen, wijzen we een openbare IP-adresbron toe als openbaar IP-adres op instantieniveau om toegang te krijgen tot deze VM van buitenaf. Dit adres wordt alleen gebruikt om toegang te krijgen voor de test.  We laten zien hoe de NAT-service voorrang heeft op andere uitgaande opties.

U deze VM ook maken zonder een openbaar IP en een andere VM maken om te gebruiken als een jumpbox zonder een openbaar IP als een oefening.

1. Selecteer linksboven in de portal **de** > optie Een bron**Compute** > **Ubuntu Server 18.04 LTS**maken of zoek naar **Ubuntu Server 18.04 LTS** in de Marketplace-zoekopdracht.

2. Voer **in Een virtuele machine maken**de volgende waarden in op het tabblad **Basisbeginselen:**
   - **Abonnementsbrongroep** > **Resource Group**: Selecteer **myResourceGroupNAT**.
   - **Instantie details** > **Virtuele machine naam:** voer **myVMsource**.
   - **Instantiedetails** > **regio** > selecteer Oost **US 2**.
   - **Verificatie van beheerdersaccount** > **voer uit**: **Wachtwoord**selecteren .
   - **Beheerdersaccount** > Voer de **gebruikersnaam,** **wachtwoord**en **wachtwoordgegevens bevestigen in.**
   - **Inkomende poortregels** > **Openbare binnenkomende poorten:** Selecteer **Geselecteerde poorten toestaan**.
   - **Binnenkomende poortregels** > **Selecteer binnenkomende poorten:** **Selecteer SSH (22)**
   - Selecteer het tabblad **Netwerken** of selecteer **Volgende: Schijven** en vervolgens **Volgende: Netwerken**.

3. Controleer op het tabblad **Netwerken** of de volgende opties zijn geselecteerd:
   - **Virtueel netwerk**: **myVnetsource**
   - **Subnet**: **mySubnetsource**
   - **Openbaar IP->** **Nieuw maken selecteren**.  Voer in het venster **Openbaar IP-adres maken** **mijnPublicIPsourceVM** in het veld **Naam** in. Selecteer **Standaard** voor de **SKU**. Laat de rest bij de standaardinstellingen staan en klik op **OK**.
   - **NIC-netwerkbeveiligingsgroep**: Selecteer **Basic**.
   - **Openbare binnenkomende poorten:** selecteer **Geselecteerde poorten toestaan**.
   - **Selecteer binnenkomende poorten:** bevestig dat **SSH** is geselecteerd.

4. Stel op het tabblad **Beheer** onder **Controleren** **de diagnose Opstarten** in op **Uit**.

5. Selecteer **Controleren + maken**.

6. Controleer de instellingen en klik op **Maken**.

## <a name="create-the-nat-gateway"></a>De NAT-gateway maken

U een of meer openbare IP-adresbronnen, openbare IP-voorvoegsels of beide met NAT-gateway gebruiken. We voegen een openbare IP-bron, openbaar IP-voorvoegsel en een NAT-gatewaybron toe.

In deze sectie wordt beschreven hoe u de volgende onderdelen van de NAT-service maken en configureren met behulp van de NAT-gatewaybron:
  - Een openbare IP-groep en een openbaar IP-voorvoegsel die moeten worden gebruikt voor uitgaande stromen die zijn vertaald door de NAT-gatewaybron.
  - Wijzig de niet-actieve time-out van de standaardinstelling van 4 minuten naar 10 minuten.

### <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken

1. Selecteer linksboven in de portal **de** > optie Een**openbaar IP-adres**voor**resourcenetwerken** > maken of zoek naar **openbaar IP-adres** in de Marketplace-zoekopdracht. 

2. Voer in **Openbaar IP-adres maken**deze gegevens in of selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | IP-versie | Selecteer **IPv4**.
    | SKU | Selecteer **Standaard**.
    | Name | Voer **myPublicIPsource in**. |
    | Abonnement | Selecteer uw abonnement.|
    | Resourcegroep | Selecteer **myResourceGroupNAT**. |
    | Locatie | Selecteer **VS - oost 2**.|

3. Laat de overige standaardwaarden staan en selecteer **Maken**.

### <a name="create-a-public-ip-prefix"></a>Een openbaar IP-voorvoegsel maken

1. Selecteer linksboven in de portal **het** > **openbare IP-voorvoegsel**van**resourcenetwerken** > maken of zoek naar **openbaar IP-voorvoegsel** in de Marketplace-zoekopdracht.

2. Voer in **Een openbaar IP-voorvoegsel**maken de volgende waarden in op het tabblad **Basisbeginselen** in of selecteer u deze:
   - **Subscription** > **Abonnementsbrongroep**: **MyResourceGroupNAT selecteren**>
   - **Naam van instantiedetails** > **Name**: voer **mijnPublicIPprefixsource in**.
   - **Instantiedetails** > **Regio**: Selecteer **Oost-VS 2**.
   - **Instantiedetails** > **Voorvoegselgrootte**: **Selecteer /31 (2 adressen)**

3. Laat de rest de standaardwaarden en selecteer **Controleren + maken**.

4. Controleer de instellingen en selecteer vervolgens **Maken**.


### <a name="create-a-nat-gateway-resource"></a>Een NAT-gatewaybron maken

1. Selecteer linksboven in de portal de optie Een**NAT-gateway**voor > **bronnetwerken** > maken of zoek naar **NAT-gateway** in de Marketplace-zoekopdracht. **Create a resource**

2. Voer in **NAT-gateway (Network Address translation) (NAT)** de volgende waarden in of selecteer op het tabblad **Basisbeginselen:**
   - **Abonnementsbrongroep** > **Resource Group**: Selecteer **myResourceGroupNAT**.
   - **Instantiedetails** > **NAT-gatewaynaam**: voer **myNATgateway in**.
   - **Instantiedetails** > **Regio**: Selecteer **Oost-VS 2**.
   - **Instantiedetails** > **Idle time-out (minuten)**: voer **10**in .
   - Selecteer het tabblad **Openbaar IP** of selecteer **Volgende: Openbaar IP**.

3. Voer op het tabblad **Openbaar IP** de volgende waarden in of selecteer deze:
   - **Openbare IP-adressen**: Selecteer **myPublicIPsource**.
   - **Openbare IP-voorvoegsels**: Selecteer **mijnPublicIPprefixsource**.
   - Selecteer het tabblad **Subnet** of selecteer **Volgende: Subnet**.

4. Voer op het tabblad **Subnet** de volgende waarden in of selecteer deze:
   - **Virtueel netwerk**: Selecteer **myResourceGroupNAT** > **myVnetsource**.
   - **Subnetnaam**: Schakel het vak naast **mySubnetsource**in.

5. Selecteer **Controleren + maken**.

6. Controleer de instellingen en selecteer vervolgens **Maken**.

Al het uitgaande verkeer naar internetbestemmingen maakt nu gebruik van de NAT-service.  Het is niet nodig om een UDR te configureren.


## <a name="prepare-destination-for-outbound-traffic"></a>Bestemming voorbereiden op uitgaand verkeer

We maken nu een bestemming voor het uitgaande verkeer dat door de NAT-service is vertaald, zodat u het testen.


## <a name="virtual-network-and-parameters-for-destination"></a>Virtueel netwerk en parameters voor bestemming

Voordat u een VM voor de bestemming implementeert, moeten we een virtueel netwerk maken waar de virtuele bestemming kan verblijven. De volgende zijn dezelfde stappen als voor de bron-VM met enkele kleine wijzigingen om het doeleindpunt bloot te leggen.

In deze sectie moet u de volgende parameters in de stappen vervangen door de onderstaande informatie:

| Parameter                   | Waarde                |
|-----------------------------|----------------------|
| **\<resourcegroepnaam>**  | myResourceGroupNAT |
| **\<>van de naam van het virtuele netwerk** | myVNetdestination          |
| **\<regionaam>**          | VS - oost 2      |
| **\<IPv4-adresruimte>**   | 192.168.0.0\16          |
| **\<subnetnaam>**          | mySubnetdestination        |
| **\<subnet-adresbereik>** | 192.168.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-destination-virtual-machine"></a>Doelvirtuele machine maken

1. Selecteer linksboven in de portal **de** > optie Een bron**Compute** > **Ubuntu Server 18.04 LTS**maken of zoek naar **Ubuntu Server 18.04 LTS** in de Marketplace-zoekopdracht.

2. Voer **in Een virtuele machine maken**de volgende waarden in op het tabblad **Basisbeginselen:**
   - **Abonnementsbrongroep** > **Resource Group**: Selecteer **myResourceGroupNAT**.
   - **Instantie details** > **Virtuele machine naam:** voer **myVMdestination**.
   - **Instantiedetails** > **regio** > selecteer Oost **US 2**.
   - **Verificatie van beheerdersaccount** > **voer uit**: **Wachtwoord**selecteren .
   - **Beheerdersaccount** > Voer de **gebruikersnaam,** **wachtwoord**en **wachtwoordgegevens bevestigen in.**
   - **Inkomende poortregels** > **Openbare binnenkomende poorten:** Selecteer **Geselecteerde poorten toestaan**.
   - **Binnenkomende poortregels** > **Selecteer binnenkomende poorten**: Selecteer **SSH (22)** en HTTP **(80)**.
   - Selecteer het tabblad **Netwerken** of selecteer **Volgende: Schijven** en vervolgens **Volgende: Netwerken**.

3. Controleer op het tabblad **Netwerken** of de volgende opties zijn geselecteerd:
   - **Virtueel netwerk**: **myVnetdestination**
   - **Subnet**: **mySubnetdestination**
   - **Openbaar IP->** **Nieuw maken selecteren**.  Voer in het venster **Openbaar IP-adres maken** **mijnPublicIPdestinationVM** in het veld **Naam** in. Selecteer **Standaard** voor **SKU**. Laat de rest bij de standaardinstellingen staan en klik op **OK**.
   - **NIC-netwerkbeveiligingsgroep**: Selecteer **Basic**.
   - **Openbare binnenkomende poorten:** selecteer **Geselecteerde poorten toestaan**.
   - **Selecteer binnenkomende poorten:** Bevestig **dat SSH** en **HTTP** is geselecteerd.

4. Stel op het tabblad **Beheer** onder **Controleren** **de diagnose Opstarten** in op **Uit**.

5. Selecteer **Controleren + maken**.

6. Controleer de instellingen en selecteer vervolgens **Maken**.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Een webserver voorbereiden en payload testen op doel-VM

Eerst moeten we het IP-adres van de doel-VM ontdekken. 

1. Selecteer **Resourcegroepen**aan de linkerkant van de portal .
2. Selecteer **myResourceGroupNAT**.
3. Selecteer **myVM-bestemming**.
4. Kopieer in **Overzicht**de waarde **van het IP-adres van het openbaar** en plak deze in notitieblokken, zodat u deze gebruiken om toegang te krijgen tot de vm.

>[!IMPORTANT]
>Kopieer het openbare IP-adres en plak het in een notitieblok, zodat u het in volgende stappen gebruiken. Geef aan dat dit de virtuele bestemmingsmachine is.

### <a name="sign-in-to-destination-vm"></a>Aanmelden bij doel-VM

Open een [Azure Cloud Shell](https://shell.azure.com) in uw browser. Gebruik het IP-adres dat in de vorige stap naar SSH is opgehaald naar de virtuele machine.

```azurecli-interactive
ssh <username>@<ip-address-destination>
```

Kopieer en plak de volgende opdrachten zodra u bent ingelogd.  

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

Eerst moeten we het IP-adres van de bron VM te ontdekken.

1. Selecteer **Resourcegroepen**aan de linkerkant van de portal .
2. Selecteer **myResourceGroupNAT**.
3. Selecteer **myVMsource**.
4. Kopieer in **Overzicht**de waarde **van het IP-adres van het openbaar** en plak deze in notitieblokken, zodat u deze gebruiken om toegang te krijgen tot de vm.

>[!IMPORTANT]
>Kopieer het openbare IP-adres en plak het in een notitieblok, zodat u het in volgende stappen gebruiken. Geef aan dat dit de virtuele bronmachine is.

### <a name="log-into-source-vm"></a>Inloggen bij bron-VM

Open een nieuw tabblad voor [Azure Cloud Shell](https://shell.azure.com) in uw browser.  Gebruik het IP-adres dat in de vorige stap naar SSH is opgehaald naar de virtuele machine. 

```azurecli-interactive
ssh <username>@<ip-address-source>
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

Deze opdracht genereert 100 aanvragen, 10 gelijktijdig, met een time-out van 30 seconden en zonder de TCP-verbinding opnieuw te gebruiken.  Elke aanvraag haalt 100 Kbytes op.  Aan het einde van de run, **hey** zal een aantal statistieken over hoe goed de NAT service deed verslag.

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de brongroep, NAT-gateway en alle gerelateerde bronnen wanneer dit niet meer nodig is. Selecteer de brongroep **myResourceGroupNAT** die de NAT-gateway bevat en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u een NAT-gateway gemaakt, een bron- en doel-VM gemaakt en vervolgens de NAT-gateway getest.

Bekijk de statistieken in Azure Monitor om uw NAT-service te bekijken. Diagnose problemen zoals resource uitputting van de beschikbare SNAT-poorten.  Uitputting van resources van SNAT-poorten is eenvoudig aan te pakken door extra openbare IP-adresbronnen of openbare IP-voorvoegselbronnen of beide toe te voegen.

- Meer informatie over [NAT van virtueel netwerk](./nat-overview.md)
- Meer informatie over [NAT-gatewaybron](./nat-gateway-resource.md).
- Snel starten voor het implementeren van [NAT-gatewaybron met Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Snel starten voor het implementeren van [NAT-gatewaybron met Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Snel starten voor het implementeren van [NAT-gatewaybron met Azure-portal.](./quickstart-create-nat-gateway-portal.md)

> [!div class="nextstepaction"]

