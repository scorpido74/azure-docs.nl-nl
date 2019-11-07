---
title: Azure Virtual Machines-netwerk latentie testen in een Azure Virtual Network | Microsoft Docs
description: Meer informatie over het testen van de netwerk latentie tussen virtuele machines van Azure in een virtueel netwerk
services: virtual-network
documentationcenter: na
author: steveesp
manager: Marina Lipshteyn
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: steveesp
ms.openlocfilehash: 760a181b4459db28d3a6eee5022cc0f984c4bee0
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73588276"
---
# <a name="testing-network-latency"></a>Netwerk latentie testen

Door netwerk latentie te meten met een hulp programma dat is ontworpen voor de taak, krijgt u de meest nauw keurige resultaten. Openbaar beschik bare hulpprogram ma's zoals SockPerf (voor Linux) en Latte (voor Windows) zijn voor beelden van hulpprogram ma's waarmee netwerk latentie kan worden geïsoleerd en gemeten terwijl andere typen latentie, zoals toepassings latentie, worden uitgesloten. Deze hulpprogram ma's zijn gericht op het soort netwerk verkeer dat van invloed is op de prestaties van de toepassing, namelijk TCP en UDP. Andere veelgebruikte connectiviteits hulpprogramma's, zoals ping, kunnen soms worden gebruikt voor meet latentie, maar deze resultaten zijn mogelijk niet representatief voor het netwerk verkeer dat wordt gebruikt in echte workloads. De meeste van deze hulpprogram ma's maken gebruik van het ICMP-protocol, dat anders kan worden behandeld vanuit het verkeer van toepassingen en de resultaten zijn mogelijk niet van toepassing op werk belastingen die gebruikmaken van TCP en UDP. Voor een nauw keurige test van de netwerk latentie van protocollen die worden gebruikt door de meeste toepassingen, produceren SockPerf (voor Linux) en Latte (voor Windows) de meest relevante resultaten. Dit document bevat deze hulpprogram ma's.

## <a name="overview"></a>Overzicht

Met twee Vm's, een als afzender en een als ontvanger, wordt een communicatie kanaal in twee richtingen gemaakt voor het verzenden en ontvangen van pakketten in beide richtingen om de RTT (round trip time) te meten.

Deze stappen kunnen worden gebruikt om de netwerk latentie tussen twee Virtual Machines (Vm's) of zelfs tussen twee fysieke computers te meten. Latentie metingen kunnen nuttig zijn voor de volgende scenario's:

- Een bench Mark-netwerk latentie instellen tussen de geïmplementeerde Vm's
- Vergelijk de effecten van wijzigingen in de netwerk latentie nadat de gerelateerde wijzigingen zijn aangebracht in:
  - OS-of netwerk stack software, inclusief configuratie wijzigingen
  - VM-implementatie methode, zoals implementeren naar een zone of PPG
  - VM-eigenschappen, zoals versneld netwerken of grootte wijzigingen
  - Virtueel netwerk, zoals wijzigingen in route ring of filters

### <a name="tools-for-testing"></a>Hulpprogram ma's voor testen
Latentie meten er zijn twee verschillende opties: één voor Windows-systemen en één voor Linux-systemen

Voor Windows: latte. exe (Windows) [https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)

Voor Linux: SockPerf (Linux) [https://github.com/mellanox/sockperf](https://github.com/mellanox/sockperf)

Met deze hulpprogram ma's wordt gegarandeerd dat alleen TCP-of UDP-Payload-leverings tijden worden gemeten en niet ICMP (ping) of andere pakket typen die niet door toepassingen worden gebruikt en niet van invloed zijn op de prestaties.

### <a name="tips-for-an-optimal-vm-configuration"></a>Tips voor een optimale VM-configuratie:

- De nieuwste versie van Windows of Linux gebruiken
- Versneld netwerken inschakelen voor de beste resultaten
- Vm's implementeren met de [plaatsings groep Azure proximity](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)
- Grotere Vm's doen over het algemeen beter dan kleinere Vm's

### <a name="tips-for-analysis"></a>Tips voor analyse

- Een vroege basis lijn maken zodra de implementatie, configuratie en optimalisaties zijn voltooid
- Nieuwe resultaten altijd vergelijken met een basis lijn of anderszins van de ene test naar de andere met beheerde wijzigingen
- Herhaal tests wanneer wijzigingen worden waargenomen of gepland


## <a name="testing-vms-running-windows"></a>Vm's met Windows testen

## <a name="get-latteexe-onto-the-vms"></a>Latte. exe op de Vm's ophalen

De nieuwste versie downloaden: [https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)

Overweeg om latte. exe in een afzonderlijke map te plaatsen, zoals c:\Tools

## <a name="allow-latteexe-through-the-windows-firewall"></a>Latte. exe toestaan via de Windows Firewall

Maak op de ontvanger een regel voor toestaan op de Windows Firewall zodat het latte. exe-verkeer kan binnenkomen. Het is eenvoudig om het hele latte. exe-programma op naam toe te staan in plaats van specifieke TCP-poorten toestaan.

Latte. exe toestaan via de Windows Firewall als volgt:

Netsh advfirewall firewall add rule Program =\<pad\>\\latte. exe name =&quot;Latte&quot; protocol = any dir = in Action = allow Enable = Yes profile = ANY


Als u bijvoorbeeld latte. exe hebt gekopieerd naar de map &quot;c:\\&quot;, is dit de opdracht:

```cmd
netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY
```

## <a name="running-latency-tests"></a>Latentie tests uitvoeren

Start latte. exe op de ontvanger (uitvoeren vanuit CMD, niet vanuit Power shell):

Latte-a &lt;IP-adres van ontvanger&gt;:&lt;poort&gt;-i &lt;herhalingen&gt;

Rond 65k-iteraties is lang genoeg om representatieve resultaten te retour neren.

Elk beschikbaar poort nummer is prima.

Als de virtuele machine een IP-adres heeft van 10.0.0.4, ziet deze er als volgt uit:

```cmd
latte -a 10.0.0.4:5005 -i 65100
```

Start latte. exe op de afzender (uitvoeren vanuit CMD, niet vanuit Power shell):

Latte-c-a \<IP-adres van ontvanger\>:\<poort\>-i \<herhalingen\>


De resulterende opdracht is hetzelfde als op de ontvanger, behalve met de toevoeging van &quot;-c&quot; om aan te geven dat dit de &quot;client&quot; of de afzender is:

```cmd
latte -c -a 10.0.0.4:5005 -i 65100
```

Wacht op de resultaten. Afhankelijk van hoe ver de Vm's bestaan, kan het enkele minuten duren voordat het proces is voltooid. Overweeg te beginnen met minder herhalingen om te testen op geslaagde pogingen voordat u langere tests uitvoert.



## <a name="testing-vms-running-linux"></a>Vm's met Linux testen

Gebruik SockPerf. Deze is beschikbaar via [https://github.com/mellanox/sockperf](https://github.com/mellanox/sockperf)

### <a name="install-sockperf-on-the-vms"></a>SockPerf installeren op de virtuele machines

Voer op de virtuele Linux-machines (zowel de afzender als de ontvanger) deze opdrachten uit om SockPerf op uw virtuele machines voor te bereiden. Er zijn opdrachten voor de primaire distributies.

#### <a name="for-rhel--centos-follow-these-steps"></a>Voer de volgende stappen uit voor RHEL/CentOS:
```bash
#CentOS / RHEL - Install GIT and other helpful tools
    sudo yum install gcc -y -q
    sudo yum install git -y -q
    sudo yum install gcc-c++ -y
    sudo yum install ncurses-devel -y
    sudo yum install -y automake
    sudo yum install -y autoconf
```

#### <a name="for-ubuntu-follow-these-steps"></a>Voer voor Ubuntu de volgende stappen uit:
```bash
#Ubuntu - Install GIT and other helpful tools
    sudo apt-get install build-essential -y
    sudo apt-get install git -y -q
    sudo apt-get install -y autotools-dev
    sudo apt-get install -y automake
    sudo apt-get install -y autoconf
```

#### <a name="for-all-distros-copy-compile-and-install-sockperf-according-to-the-following-steps"></a>Voor alle distributies kopieert, compileert en installeert u de SockPerf op basis van de volgende stappen:
```bash
#Bash - all distros

#From bash command line (assumes git is installed)
git clone https://github.com/mellanox/sockperf
cd sockperf/
./autogen.sh
./configure --prefix=

#make is slower, may take several minutes
make

#make install is fast
sudo make install
```

### <a name="run-sockperf-on-the-vms"></a>SockPerf uitvoeren op de Vm's

Wanneer de SockPerf-installatie is voltooid, zijn de Vm's klaar om de latentie tests uit te voeren. 

Start eerst SockPerf op de ontvanger.

Elk beschikbaar poort nummer is prima. In dit voor beeld gebruiken we poort 12345:
```bash
#Server/Receiver - assumes server's IP is 10.0.0.4:
sudo sockperf sr --tcp -i 10.0.0.4 -p 12345
```
Nu de server luistert, kan de client beginnen met het verzenden van pakketten naar de server op de poort waarop deze luistert, 12345 in dit geval.

Ongeveer 100 seconden is lang genoeg om representatieve resultaten te retour neren, zoals wordt weer gegeven in het volgende voor beeld:
```bash
#Client/Sender - assumes server's IP is 10.0.0.4:
sockperf ping-pong -i 10.0.0.4 --tcp -m 350 -t 101 -p 12345  --full-rtt
```

Wacht op de resultaten. Afhankelijk van hoe ver de Vm's bestaan, is het aantal iteraties afhankelijk. U kunt met een kortere tests van ongeveer 5 seconden beginnen om te testen op succes voordat u langere tests uitvoert.

In dit SockPerf-voor beeld wordt een bericht grootte van 350 byte gebruikt omdat het een typisch pakket voor gemiddelde grootte is. De grootte van het bericht kan hoger of lager worden aangepast om de resultaten nauw keuriger weer te geven die op de virtuele machines worden uitgevoerd.


## <a name="next-steps"></a>Volgende stappen
* De latentie verbeteren met de [plaatsings groep voor Azure proximity](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)
* Meer informatie over het [optimaliseren van netwerken voor vm's](../virtual-network/virtual-network-optimize-network-bandwidth.md) voor uw scenario.
* Meer informatie over hoe [band breedte wordt toegewezen aan virtuele machines](../virtual-network/virtual-machine-network-throughput.md)
* Meer informatie met behulp van veelgestelde [vragen over Azure Virtual Network](../virtual-network/virtual-networks-faq.md)