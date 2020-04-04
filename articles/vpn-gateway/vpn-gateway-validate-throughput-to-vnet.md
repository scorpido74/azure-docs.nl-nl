---
title: VPN-doorvoer naar een Microsoft Azure Virtual Network valideren
description: Het doel van dit document is om een gebruiker te helpen de netwerkdoorvoer te valideren van hun on-premises resources naar een virtuele Azure-machine.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
manager: dcscontentpm
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 05/29/2019
ms.author: radwiv
ms.reviewer: chadmat;genli
ms.openlocfilehash: dcf86deda32069bf9711dbeb733dc9361e22a771
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631775"
---
# <a name="how-to-validate-vpn-throughput-to-a-virtual-network"></a>VPN-doorvoer naar een virtueel netwerk valideren

Met een VPN-gatewayverbinding u veilige, cross-premises connectiviteit tot stand brengen tussen uw virtuele netwerk binnen Azure en uw on-premises IT-infrastructuur.

In dit artikel ziet u hoe u de netwerkdoorvoer valideren van de on-premises resources naar een virtuele Azure-machine (VM).

> [!NOTE]
> Dit artikel is bedoeld om veelvoorkomende problemen te diagnosticeren en op te lossen. Als u het probleem niet oplossen met behulp van de volgende informatie, neemt [u contact op met de ondersteuning.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)

## <a name="overview"></a>Overzicht

De VPN-gatewayverbinding omvat de volgende componenten:

* On-premises VPN-apparaat (Bekijk een lijst met [gevalideerde VPN-apparaten](vpn-gateway-about-vpn-devices.md#devicetable).)
* Openbaar internet
* Azure VPN-gateway
* Azure VM

Het volgende diagram toont de logische connectiviteit van een on-premises netwerk met een Virtueel Azure-netwerk via VPN.

![Logische connectiviteit van het klantennetwerk met MSFT Network via VPN](./media/vpn-gateway-validate-throughput-to-vnet/VPNPerf.png)

## <a name="calculate-the-maximum-expected-ingressegress"></a>Bereken de maximaal verwachte binnendringen/uitgang

1. Bepaal de basislijndoorvoervereisten van uw toepassing.
1. Bepaal de doorvoerlimieten van uw Azure VPN-gateway. Zie voor hulp het gedeelte "Gateway SKU's" van [About VPN Gateway.](vpn-gateway-about-vpngateways.md#gwsku)
1. Bepaal de [azure VM-doorvoerrichtlijnen](../virtual-machines/virtual-machines-windows-sizes.md) voor uw VM-grootte.
1. Bepaal de bandbreedte van uw internetserviceprovider (ISP).
1. Bereken uw verwachte doorvoer door de minste bandbreedte van de VM, VPN Gateway of ISP te nemen; die wordt gemeten in Megabits per seconde (/) gedeeld door acht (8).

Als uw berekende doorvoer niet voldoet aan de vereisten voor basislijndoorvoer van uw toepassing, moet u de bandbreedte van de resource die u als knelpunt hebt geïdentificeerd, verhogen. Zie [Een gateway-SKU wijzigen](vpn-gateway-about-vpn-gateway-settings.md#gwsku)als u het formaat van een Azure VPN-gateway wilt wijzigen. Zie Het formaat van een virtuele machine wijzigen als u het formaat van een virtuele machine wilt [wijzigen.](../virtual-machines/virtual-machines-windows-resize-vm.md) Als u de verwachte internetbandbreedte niet ervaart, u ook contact opnemen met uw internetprovider.

> [!NOTE]
> VPN-gatewaydoorvoer is een aggregaat van alle site-to-site\VNET-naar-VNET- of Point-to-Site-verbindingen.

## <a name="validate-network-throughput-by-using-performance-tools"></a>Netwerkdoorvoer valideren met prestatietools

Deze validatie moet worden uitgevoerd tijdens niet-piekuren, omdat de overslagverzadiging van de VPN-tunnel tijdens het testen geen nauwkeurige resultaten oplevert.

De tool die we gebruiken voor deze test is iPerf, die werkt op zowel Windows als Linux en zowel client- als servermodi heeft. Het is beperkt tot 3Gbps voor Windows VM's.

Met deze tool worden geen lees-/schrijfbewerkingen uitgevoerd op de schijf. Het produceert alleen zelf gegenereerd TCP-verkeer van de ene naar de andere kant. Het genereert statistieken op basis van experimenten die de beschikbare bandbreedte tussen client- en serverknooppunten meten. Bij het testen tussen twee knooppunten fungeert één knooppunt als de server en fungeert het andere knooppunt als client. Zodra deze test is voltooid, raden we u aan de rollen van de knooppunten om te keren om zowel de upload- als downloaddoorvoer op beide knooppunten te testen.

### <a name="download-iperf"></a>IPerf downloaden

Download [iPerf](https://iperf.fr/download/iperf_3.1/iperf-3.1.2-win64.zip). Zie [iPerf-documentatie](https://iperf.fr/iperf-doc.php)voor meer informatie.

 > [!NOTE]
 > De producten van derden die in dit artikel worden besproken, worden vervaardigd door bedrijven die onafhankelijk zijn van Microsoft. Microsoft geeft geen garantie, impliciet of anderszins, over de prestaties of betrouwbaarheid van deze producten.

### <a name="run-iperf-iperf3exe"></a>IPerf uitvoeren (iperf3.exe)

1. Schakel een NSG/ACL-regel in die het verkeer toestaat (voor openbare IP-adrestests op Azure VM).

1. Schakel op beide knooppunten een firewalluitzondering in voor poort 5001.

   **Windows:** Voer de volgende opdracht uit als beheerder:

   ```CMD
   netsh advfirewall firewall add rule name="Open Port 5001" dir=in action=allow protocol=TCP localport=5001
   ```

   Voer de regel uit als u de regel wilt verwijderen wanneer het testen is voltooid:

   ```CMD
   netsh advfirewall firewall delete rule name="Open Port 5001" protocol=TCP localport=5001
   ```

   **Azure Linux:** Azure Linux-afbeeldingen hebben tolerante firewalls. Als er een toepassing luistert op een poort, is het verkeer toegestaan door. Aangepaste afbeeldingen die zijn beveiligd, moeten mogelijk poorten expliciet openen. Veelvoorkomende Linux OS-laagfirewalls zijn , `iptables` `ufw`of `firewalld`.

1. Wijzig op het serverknooppunt de map waar iperf3.exe wordt geëxtraheerd. Voer vervolgens iPerf uit in de servermodus en stel deze in om te luisteren op poort 5001 als de volgende opdrachten:

   ```CMD
   cd c:\iperf-3.1.2-win65

   iperf3.exe -s -p 5001
   ```

   > [!Note]
   > Poort 5001 is aanpasbaar om rekening te houden met bepaalde firewallbeperkingen in uw omgeving.

1. Wijzig op het clientknooppunt de map waar het gereedschap iperf wordt geëxtraheerd en voer vervolgens de volgende opdracht uit:

   ```CMD
   iperf3.exe -c <IP of the iperf Server> -t 30 -p 5001 -P 32
   ```

   De client stuurt dertig seconden verkeer op poort 5001 naar de server. De vlag '-P' geeft aan dat we 32 gelijktijdige verbindingen maken met het serverknooppunt.

   In het volgende scherm ziet u de uitvoer uit dit voorbeeld:

   ![Uitvoer](./media/vpn-gateway-validate-throughput-to-vnet/06theoutput.png)

1. (OPTIONEEL) Voer de gewenste opdracht uit om de testresultaten te behouden:

   ```CMD
   iperf3.exe -c IPofTheServerToReach -t 30 -p 5001 -P 32  >> output.txt
   ```

1. Nadat u de vorige stappen hebt voltooid, voert u dezelfde stappen uit waarbij de rollen zijn omgekeerd, zodat het serverknooppunt nu het clientknooppunt is en vice versa.

> [!Note]
> Iperf is niet het enige gereedschap. [NTTTCP is een alternatieve oplossing voor het testen.](https://docs.microsoft.com/azure/virtual-network/virtual-network-bandwidth-testing)

## <a name="test-vms-running-windows"></a>VM's met Windows testen

### <a name="load-latteexe-onto-the-vms"></a>Latte.exe op de VM's laden

Download de nieuwste versie van [Latte.exe](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)

Overweeg om Latte.exe in een aparte map te plaatsen, zoals`c:\tools`

### <a name="allow-latteexe-through-the-windows-firewall"></a>Latte.exe toestaan via de Windows-firewall

Maak op de ontvanger een regel Toestaan op de Windows Firewall zodat het Latte.exe-verkeer kan binnenkomen. Het is het gemakkelijkst om het hele Latte.exe-programma op naam toe te staan in plaats van specifieke TCP-poorten toe te staan.

### <a name="allow-latteexe-through-the-windows-firewall-like-this"></a>Latte.exe toestaan via de Windows Firewall als volgt

`netsh advfirewall firewall add rule program=<PATH>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

Als u bijvoorbeeld latte.exe hebt gekopieerd naar de map 'c:\tools', is dit de opdracht

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>Latentietests uitvoeren

Start latte.exe op de ONTVANGER (uitgevoerd vanaf CMD, niet van PowerShell):

`latte -a <Receiver IP address>:<port> -i <iterations>`

Ongeveer 65k iteraties is lang genoeg om representatieve resultaten terug te keren.

Alle beschikbare poortnummer is prima.

Als de VM een IP-adres van 10.0.0.4 heeft, ziet het er als volgt uit

`latte -c -a 10.0.0.4:5005 -i 65100`

Start latte.exe op de AFZENDER (uitgevoerd vanaf CMD, niet van PowerShell)

`latte -c -a <Receiver IP address>:<port> -i <iterations>`

De resulterende opdracht is hetzelfde als op de ontvanger, behalve met de toevoeging van "-c" om aan te geven dat dit de "client" of afzender

`latte -c -a 10.0.0.4:5005 -i 65100`

Wacht op de resultaten. Afhankelijk van hoe ver uit elkaar de VM's zijn, kan het een paar minuten duren om te voltooien. Overweeg om te beginnen met minder iteraties om te testen op succes voordat u langere tests uitvoert.

## <a name="test-vms-running-linux"></a>VM's testen met Linux

Gebruik [SockPerf](https://github.com/mellanox/sockperf) om VM's te testen.

### <a name="install-sockperf-on-the-vms"></a>Installeer SockPerf op de VM's

Voer op de Linux VM's (zowel AFZENDER als ONTVANGER) deze opdrachten uit om SockPerf voor te bereiden op uw VM's:

#### <a name="centos--rhel---install-git-and-other-helpful-tools"></a>CentOS / RHEL - GIT en andere handige tools installeren

`sudo yum install gcc -y -q`
`sudo yum install git -y -q`
`sudo yum install gcc-c++ -y`
`sudo yum install ncurses-devel -y`
`sudo yum install -y automake`

#### <a name="ubuntu---install-git-and-other-helpful-tools"></a>Ubuntu - GIT en andere handige tools installeren

`sudo apt-get install build-essential -y`
`sudo apt-get install git -y -q`
`sudo apt-get install -y autotools-dev`
`sudo apt-get install -y automake`

#### <a name="bash---all"></a>Bash - alles

Van bash commandolijn (veronderstelt git is geïnstalleerd)

`git clone https://github.com/mellanox/sockperf`
`cd sockperf/`
`./autogen.sh`
`./configure --prefix=`

Maken is langzamer, kan enkele minuten duren

`make`

Installeren is snel

`sudo make install`

### <a name="run-sockperf-on-the-vms"></a>Run SockPerf op de VM's

#### <a name="sample-commands-after-installation-serverreceiver---assumes-servers-ip-is-10004"></a>Voorbeeldopdrachten na installatie. Server/ontvanger - gaat ervan uit dat het IP-adres van de server 10.0.0.4 is

`sudo sockperf sr --tcp -i 10.0.0.4 -p 12345 --full-rtt`

#### <a name="client---assumes-servers-ip-is-10004"></a>Client - gaat ervan uit dat het IP-adres van de server 10.0.0.4 is

`sockperf ping-pong -i 10.0.0.4 --tcp -m 1400 -t 101 -p 12345  --full-rtt`

> [!Note]
> Zorg ervoor dat er geen tussenliggende hop (bijvoorbeeld virtueel toestel) tijdens de doorvoer testen tussen de VM en Gateway.
> Als er slechte resultaten (in termen van totale doorvoer) afkomstig van de iPERF / NTTTCP tests hierboven, verwijzen wij u naar het volgende artikel om de belangrijkste factoren achter de mogelijke onderliggende oorzaken van het probleem te begrijpen:https://docs.microsoft.com/azure/virtual-network/virtual-network-tcpip-performance-tuning

Met name de analyse van packet capture traces (Wireshark/Network Monitor) die tijdens die tests parallel van client en server worden verzameld, zal helpen bij het beoordelingen van slechte prestaties. Deze sporen kunnen pakketverlies, hoge latentie, MTU-grootte omvatten. fragmentatie, TCP 0 Window, Out of Order-fragmenten, enzovoort.

## <a name="address-slow-file-copy-issues"></a>Problemen met langzame bestandskopieën aanpakken

Zelfs als de totale doorvoer beoordeeld met de vorige stappen (iPERF / NTTTCP / etc..) was goed, u ervaren trage file coping bij het gebruik van Windows Explorer, of slepen en laten vallen door middel van een RDP-sessie. Dit probleem is normaal gesproken te wijten aan een of beide van de volgende factoren:

* Bestandskopietoepassingen, zoals Windows Verkenner en RDP, gebruiken geen meerdere threads bij het kopiëren van bestanden. Voor betere prestaties gebruikt u een toepassing voor het kopiëren met meerdere threads, zoals [Richcopy,](https://technet.microsoft.com/magazine/2009.04.utilityspotlight.aspx) om bestanden te kopiëren met behulp van 16 of 32 threads. Als u het draadnummer voor bestandskopie in Richcopy wilt wijzigen, klikt u op > **Actiekopieeropties** > **Bestandskopie**. **Action**

   ![Problemen met het langzaam kopiëren van bestanden](./media/vpn-gateway-validate-throughput-to-vnet/Richcopy.png)<br>

   > [!Note]
   > Niet alle toepassingen werken hetzelfde, en niet alle toepassing / proces maakt gebruik van alle threads. Als u de test uitvoert, u zien dat sommige threads leeg zijn en geen nauwkeurige doorvoerresultaten opleveren.
   > Als u de prestaties van uw toepassingsbestandsoverdracht wilt controleren, gebruikt u multi-thread door het aantal thread achter elkaar te verhogen of te verlagen om de optimale doorvoer van de toepassing of bestandsoverdracht te vinden.

* Onvoldoende VM-schijflees-/schrijfsnelheid. Zie [Azure Storage Troubleshooting](../storage/common/storage-e2e-troubleshooting.md)voor meer informatie.

## <a name="on-premises-device-external-facing-interface"></a>On-premises interface voor externe apparaten

Vermeldde de subnetten van on-premises bereiken die u wilt dat Azure bereikt via VPN op Local Network Gateway. Definieer tegelijkertijd de VNET-adresruimte in Azure op het on-premises apparaat.

* **Routegebaseerde gateway:** de beleids- of verkeerskiezer voor routegebaseerde VPN's zijn geconfigureerd als elke -to-any (of wild cards).

* **Policy Based Gateway**: Op beleid gebaseerde VPN's versleutelen en sturen pakketten door IPsec-tunnels op basis van de combinaties van adresvoorvoegsels tussen uw on-premises netwerk en het Azure VNet. Het beleid (of de verkeersselector) wordt gewoonlijk gedefinieerd als een toegangslijst in de VPN-configuratie.

* **UsePolicyBasedTrafficSelector-verbindingen:** ("UsePolicyBasedTrafficSelectors" om te $True op een verbinding, configureert de Azure VPN-gateway om verbinding te maken met vpn-firewall op basis van beleid op locatie. Als u PolicyBasedTrafficSelectors inschakelt, moet u ervoor zorgen dat uw VPN-apparaat de overeenkomende verkeersselectors heeft gedefinieerd met alle combinaties van uw voorvoegsels voor het lokale netwerk (local network gateway) van en naar de voorvoegsels van het Azure-virtuele netwerk, in plaats van alle-to-any.If you enable policyBasedTrafficSelectselectors, you need to ensure your VPN device has has the matching traffic selectors defined with all combinations of your on-premises network (local network gateway) prefixes to and from the Azure virtual network prefixes, instead of any-to-any.

Ongepaste configuratie kan leiden tot frequente verbreekt binnen de tunnel, packet drops, slechte doorvoer, en latentie.

## <a name="check-latency"></a>Latentie controleren

U de latentie controleren met behulp van de volgende gereedschappen:

* WinMTR (WinMTR)
* Tcptraceroute
* `ping`en `psping` (Deze tools kunnen een goede schatting van RTT geven, maar ze kunnen niet in alle gevallen worden gebruikt.)

![Latentie controleren](./media/vpn-gateway-validate-throughput-to-vnet/08checkinglatency.png)

Als u een hoge latentiepiek merkt bij een van de hop voordat u de MS Network-backbone binnengaat, u verder onderzoek doen met uw internetserviceprovider.

Als een grote, ongebruikelijke latentiepiek wordt opgemerkt door hop binnen "msn.net", neem dan contact op met MS-ondersteuning voor verder onderzoek.

## <a name="next-steps"></a>Volgende stappen

Ga voor meer informatie of hulp naar de volgende link:

* [Microsoft-ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
