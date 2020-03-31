---
title: Azure-netwerklatentie voor virtuele machines testen in een virtueel Azure-netwerk | Microsoft Documenten
description: Meer informatie over het testen van netwerklatentie tussen virtuele Azure-machines in een virtueel netwerk
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
ms.openlocfilehash: 00efc2754948d53d4f80a6261dbd4041b358185b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74896363"
---
# <a name="test-vm-network-latency"></a>Netwerklatentie van VM testen

Om de meest nauwkeurige resultaten te bereiken, meet u de netwerklatentie van uw Azure-virtuele machine (VM) met een tool die is ontworpen voor de taak. Openbaar beschikbare tools zoals SockPerf (voor Linux) en latte.exe (voor Windows) kunnen de netwerklatentie isoleren en meten, terwijl andere soorten latentie, zoals de latentie van toepassingen, worden uitgesloten. Deze tools richten zich op het soort netwerkverkeer dat van invloed is op de prestaties van toepassingen (namelijk Transmission Control Protocol [TCP] en User Datagram Protocol [UDP]-verkeer). 

Andere veelvoorkomende connectiviteitstools, zoals Ping, kunnen latentie meten, maar de resultaten ervan vertegenwoordigen mogelijk niet het netwerkverkeer dat wordt gebruikt in echte workloads. Dat komt omdat de meeste van deze hulpprogramma's gebruik maken van het Internet Control Message Protocol (ICMP), dat anders kan worden behandeld dan toepassingsverkeer en waarvan de resultaten mogelijk niet van toepassing zijn op workloads die TCP en UDP gebruiken. 

Voor nauwkeurige netwerklatentietesten van de protocollen die door de meeste toepassingen worden gebruikt, produceren SockPerf (voor Linux) en latte.exe (voor Windows) de meest relevante resultaten. Dit artikel behandelt beide tools.

## <a name="overview"></a>Overzicht

Door twee VM's te gebruiken, één als afzender en één als ontvanger, maakt u een communicatiekanaal in twee richtingen. Met deze aanpak u pakketten in beide richtingen verzenden en ontvangen en de retourtijd (RTT) meten.

U deze benadering gebruiken om de netwerklatentie tussen twee VM's of zelfs tussen twee fysieke computers te meten. Latentiemetingen kunnen nuttig zijn voor de volgende scenario's:

- Stel een benchmark vast voor netwerklatentie tussen de geïmplementeerde VM's.
- Vergelijk de effecten van wijzigingen in netwerklatentie nadat gerelateerde wijzigingen zijn aangebracht in:
  - Besturingssysteem (OS) of netwerkstacksoftware, inclusief configuratiewijzigingen.
  - Een VM-implementatiemethode, zoals het implementeren naar een beschikbaarheidszone of een nabijheidsplaatsingsgroep (PPG).
  - VM-eigenschappen, zoals Versnelde netwerken of groottewijzigingen.
  - Een virtueel netwerk, zoals het routeren of filteren van wijzigingen.

### <a name="tools-for-testing"></a>Hulpmiddelen voor het testen
Als u de latentie wilt meten, hebt u twee verschillende gereedschapsopties:

* Voor Windows-systemen: [latte.exe (Windows)](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)
* Voor Linux-gebaseerde systemen: [SockPerf (Linux)](https://github.com/mellanox/sockperf)

Door deze hulpprogramma's te gebruiken, u ervoor zorgen dat alleen de levertijden van TCP- of UDP-payloadworden gemeten en niet ICMP (Ping) of andere pakkettypen die niet door toepassingen worden gebruikt en geen invloed hebben op hun prestaties.

### <a name="tips-for-creating-an-optimal-vm-configuration"></a>Tips voor het maken van een optimale VM-configuratie

Houd bij het maken van uw VM-configuratie rekening met de volgende aanbevelingen:
- Gebruik de nieuwste versie van Windows of Linux.
- Schakel Accelerated Networking in voor de beste resultaten.
- Vm's implementeren met een [Azure-plaatsingsgroep voor nabijheid](https://docs.microsoft.com/azure/virtual-machines/linux/co-location).
- Grotere VM's presteren over het algemeen beter dan kleinere VM's.

### <a name="tips-for-analysis"></a>Tips voor analyse

Houd bij het analyseren van testresultaten rekening met de volgende aanbevelingen:

- Stel een basislijn vast in een vroeg stadium, zodra de implementatie, configuratie en optimalisaties zijn voltooid.
- Vergelijk nieuwe resultaten altijd met een basislijn of, anders, van de ene test naar de andere met gecontroleerde wijzigingen.
- Herhaal tests wanneer wijzigingen worden waargenomen of gepland.


## <a name="test-vms-that-are-running-windows"></a>VM's testen waarop Windows wordt uitgevoerd

### <a name="get-latteexe-onto-the-vms"></a>Download latte.exe op de VM's

Download de [nieuwste versie van latte.exe](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b).

Overweeg om latte.exe in een aparte map te plaatsen, zoals *c:\tools*.

### <a name="allow-latteexe-through-windows-defender-firewall"></a>Latte.exe toestaan via Windows Defender Firewall

Maak op de *ontvanger*een regel Toestaan op Windows Defender Firewall zodat het latte.exe-verkeer kan aankomen. Het is het gemakkelijkst om het hele latte.exe-programma op naam toe te staan in plaats van specifieke TCP-poorten toe te staan.

Sta latte.exe toe via Windows Defender Firewall door de volgende opdracht uit te voeren:

```cmd
netsh advfirewall firewall add rule program=<path>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY
```

Als u bijvoorbeeld latte.exe naar de map *c:\tools* hebt gekopieerd, is dit de opdracht:

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>Latentietests uitvoeren

* Op de *ontvanger*, start latte.exe (voer het uit het CMD-venster, niet van PowerShell):

    ```cmd
    latte -a <Receiver IP address>:<port> -i <iterations>
    ```

    Ongeveer 65.000 iteraties is lang genoeg om representatieve resultaten terug te geven.

    Alle beschikbare poortnummer is prima.

    Als de VM een IP-adres van 10.0.0.4 heeft, ziet de opdracht er als volgt uit:

    `latte -a 10.0.0.4:5005 -i 65100`

* Op de *afzender*, start latte.exe (voer het uit het CMD-venster, niet van PowerShell):

    ```cmd
    latte -c -a <Receiver IP address>:<port> -i <iterations>
    ```

    De resulterende opdracht is hetzelfde als op de&nbsp;ontvanger, behalve met de toevoeging van *-c* om aan te geven dat dit de *client*is, of *afzender:*

    `latte -c -a 10.0.0.4:5005 -i 65100`

Wacht op de resultaten. Afhankelijk van hoe ver uit elkaar de VM's zijn, kan de test een paar minuten duren om te voltooien. Overweeg om te beginnen met minder iteraties om te testen op succes voordat u langere tests uitvoert.

## <a name="test-vms-that-are-running-linux"></a>VM's testen waarop Linux wordt uitgevoerd

Als u VM's wilt testen waarop Linux wordt uitgevoerd, gebruikt u [SockPerf](https://github.com/mellanox/sockperf).

### <a name="install-sockperf-on-the-vms"></a>Installeer SockPerf op de VM's

Op de Linux VM's, zowel *afzender* als *ontvanger,* voeren de volgende commando's uit om SockPerf op de VM's voor te bereiden. Commando's zijn voorzien voor de grote distro's.

#### <a name="for-red-hat-enterprise-linux-rhelcentos"></a>Voor Red Hat Enterprise Linux (RHEL)/CentOS

Voer de volgende opdrachten uit:

```bash
#RHEL/CentOS - Install Git and other helpful tools
    sudo yum install gcc -y -q
    sudo yum install git -y -q
    sudo yum install gcc-c++ -y
    sudo yum install ncurses-devel -y
    sudo yum install -y automake
    sudo yum install -y autoconf
```

#### <a name="for-ubuntu"></a>Voor Ubuntu

Voer de volgende opdrachten uit:

```bash
#Ubuntu - Install Git and other helpful tools
    sudo apt-get install build-essential -y
    sudo apt-get install git -y -q
    sudo apt-get install -y autotools-dev
    sudo apt-get install -y automake
    sudo apt-get install -y autoconf
```

#### <a name="for-all-distros"></a>Voor alle distro's

Kopieer, compileer en installeer SockPerf volgens de volgende stappen:

```bash
#Bash - all distros

#From bash command line (assumes Git is installed)
git clone https://github.com/mellanox/sockperf
cd sockperf/
./autogen.sh
./configure --prefix=

#make is slower, may take several minutes
make

#make install is fast
sudo make install
```

### <a name="run-sockperf-on-the-vms"></a>Run SockPerf op de VM's

Nadat de SockPerf installatie is voltooid, zijn de VM's klaar om de latentietests uit te voeren. 

Start eerst SockPerf op de *ontvanger.*

Alle beschikbare poortnummer is prima. In dit voorbeeld gebruiken we poort 12345:

```bash
#Server/Receiver - assumes server's IP is 10.0.0.4:
sudo sockperf sr --tcp -i 10.0.0.4 -p 12345
```

Nu de server luistert, kan de client beginnen met het verzenden van pakketten naar de server op de poort waarop hij luistert (in dit geval 12345).

Ongeveer 100 seconden is lang genoeg om representatieve resultaten terug te geven, zoals in het volgende voorbeeld wordt weergegeven:

```bash
#Client/Sender - assumes server's IP is 10.0.0.4:
sockperf ping-pong -i 10.0.0.4 --tcp -m 350 -t 101 -p 12345  --full-rtt
```

Wacht op de resultaten. Afhankelijk van hoe ver de VM's uit elkaar liggen, zal het aantal iteraties variëren. Om te testen op succes voordat u langere tests uitvoert, u overwegen te beginnen met kortere tests van ongeveer 5 seconden.

In dit voorbeeld SockPerf wordt een berichtgrootte van 350 byte gebruikt, wat typisch is voor een gemiddeld pakket. U de grootte hoger of lager aanpassen om resultaten te bereiken die nauwkeuriger de werkbelasting weergeven die op uw VM's wordt uitgevoerd.


## <a name="next-steps"></a>Volgende stappen
* De latentie verbeteren met een [Azure-plaatsingsgroep](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)voor nabijheid .
* Meer informatie over het [optimaliseren van netwerken voor VM's](../virtual-network/virtual-network-optimize-network-bandwidth.md) voor uw scenario.
* Lees [hoe bandbreedte wordt toegewezen aan virtuele machines.](../virtual-network/virtual-machine-network-throughput.md)
* Zie [Veelgestelde vragen over het Virtuele Azure-netwerk](../virtual-network/virtual-networks-faq.md)van Azure .
