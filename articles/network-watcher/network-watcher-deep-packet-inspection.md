---
title: Pakketinspectie met Azure Network Watcher | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u Network Watcher gebruiken om deep packet inspection uit te voeren die zijn verzameld bij een VM
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: 7b907d00-9c35-40f5-a61e-beb7b782276f
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 7d32043ca73e9cf810b3eab5e65cb4b42b599d18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152921"
---
# <a name="packet-inspection-with-azure-network-watcher"></a>Pakketinspectie met Azure Network Watcher

Met behulp van de functie voor het vastleggen van pakketten van Network Watcher u opnamesessies op uw Azure VM's starten en beheren vanuit de portal, PowerShell, CLI en programmatisch via de SDK- en REST-API. Met packet capture u scenario's aanpakken waarvoor gegevens op pakketniveau nodig zijn door de informatie in een gemakkelijk bruikbare indeling te verstrekken. Door gebruik te maken van vrij beschikbare tools om de gegevens te inspecteren, u communicatie van en naar uw VM's onderzoeken en inzicht krijgen in uw netwerkverkeer. Enkele voorbeelden van pakketopnamegegevens zijn: het onderzoeken van netwerk- of toepassingsproblemen, het opsporen van netwerkmisbruik en inbraakpogingen of het handhaven van naleving van de regelgeving. In dit artikel laten we zien hoe je een packet capture-bestand openen dat door Network Watcher wordt geleverd met behulp van een populaire open source-tool. We geven ook voorbeelden die laten zien hoe u een verbindingslatentie berekenen, abnormaal verkeer identificeren en netwerkstatistieken onderzoeken.

## <a name="before-you-begin"></a>Voordat u begint

Dit artikel gaat door een aantal vooraf geconfigureerde scenario's op een pakketopname die eerder is uitgevoerd. Deze scenario's illustreren mogelijkheden die toegankelijk zijn door een pakketopname te bekijken. Dit scenario maakt gebruik [van WireShark](https://www.wireshark.org/) om de pakketopname te inspecteren.

In dit scenario wordt ervan uitgegaan dat u al een pakketopname op een virtuele machine hebt uitgevoerd. Voor meer informatie over het maken van een pakketopnamebezoek [Beheer pakketopnames met de portal](network-watcher-packet-capture-manage-portal.md) of met REST door [pakketopnames beheren met REST API](network-watcher-packet-capture-manage-rest.md)te bezoeken.

## <a name="scenario"></a>Scenario

In dit scenario gaat u als volgt te werk:

* Een pakketopname bekijken

## <a name="calculate-network-latency"></a>Netwerklatentie berekenen

In dit scenario laten we zien hoe u de eerste RTT (Round Trip Time) van een TCP-gesprek (Transmission Control Protocol) bekijken dat plaatsvindt tussen twee eindpunten.

Wanneer een TCP-verbinding tot stand wordt gebracht, volgen de eerste drie pakketten die in de verbinding worden verzonden een patroon dat gewoonlijk de handdruk in drie richtingen wordt genoemd. Door de eerste twee pakketten te onderzoeken die in deze handdruk worden verzonden, een eerste verzoek van de client en een antwoord van de server, kunnen we de latentie berekenen wanneer deze verbinding tot stand is gebracht. Deze latentie wordt aangeduid als de Round Trip Time (RTT). Voor meer informatie over het TCP-protocol en de handdruk in drie richtingen verwijzen we naar de volgende bron. [https://support.microsoft.com/en-us/help/172983/explanation-of-the-three-way-handshake-via-tcp-ip](https://support.microsoft.com/en-us/help/172983/explanation-of-the-three-way-handshake-via-tcp-ip)

### <a name="step-1"></a>Stap 1

Lancering WireShark

### <a name="step-2"></a>Stap 2

Laad het **.cap-bestand** vanuit uw pakketopname. Dit bestand is te vinden in de blob die het lokaal op de virtuele machine is opgeslagen, afhankelijk van hoe u het hebt geconfigureerd.

### <a name="step-3"></a>Stap 3

Om de eerste Round Trip Time (RTT) in TCP-gesprekken te bekijken, kijken we alleen naar de eerste twee pakketten die betrokken zijn bij de TCP-handshake. We zullen de eerste twee pakketten gebruiken in de drie-weg handdruk, die de [SYN], [SYN, ACK] pakketten zijn. Ze zijn vernoemd naar vlaggen die zijn ingesteld in de TCP-header. Het laatste pakket in de handdruk, het [ACK]-pakket, wordt in dit scenario niet gebruikt. Het [SYN] pakket wordt verzonden door de client. Zodra deze is ontvangen, stuurt de server het [ACK]-pakket als bevestiging van het ontvangen van de SYN van de client. Door gebruik te maken van het feit dat de reactie van de server zeer weinig overhead vereist, berekenen we de RTT door de tijd af te trekken waarop het [SYN, ACK]-pakket door de client is ontvangen tegen de tijd dat [SYN] pakket door de client werd verzonden.

Met WireShark wordt deze waarde voor ons berekend.

Om de eerste twee pakketten in de TCP-handdruk in drie richtingen gemakkelijker te bekijken, zullen we gebruik maken van de filtermogelijkheden van WireShark.

Als u het filter wilt toepassen in WireShark, vouwt u het segment 'Transmission Control Protocol' van een [SYN]-pakket uit in uw opname en onderzoekt u de vlaggen die zijn ingesteld in de TCP-header.

Aangezien we op zoek zijn om te filteren op alle [SYN] en [SYN, ACK] pakketten, onder vlaggen bevestigen dat de Syn bit is ingesteld op 1, klik dan met de rechtermuisknop op de Syn bit -> Toepassen als filter -> geselecteerd.

![figuur 7][7]

### <a name="step-4"></a>Stap 4

Nu u het venster hebt gefilterd om alleen pakketten te zien met de [SYN] bitset, u eenvoudig gesprekken selecteren waarin u geïnteresseerd bent om de eerste RTT te bekijken. Een eenvoudige manier om de RTT in WireShark te bekijken klikt u gewoon op de vervolgkeuzelijst met de "SEQ/ACK"-analyse. U ziet dan de RTT weergegeven. In dit geval was de RTT 0,0022114 seconden, of 2.211 ms.

![figuur 8][8]

## <a name="unwanted-protocols"></a>Ongewenste protocollen

U veel toepassingen laten uitvoeren op een virtuele machine-instantie die u in Azure hebt geïmplementeerd. Veel van deze toepassingen communiceren via het netwerk, misschien zonder uw uitdrukkelijke toestemming. Met behulp van packet capture om netwerkcommunicatie op te slaan, kunnen we onderzoeken hoe de toepassing op het netwerk praat en zoeken naar eventuele problemen.

In dit voorbeeld bekijken we een eerdere run-packet capture op ongewenste protocollen die kunnen duiden op ongeautoriseerde communicatie van een toepassing die op uw machine wordt uitgevoerd.

### <a name="step-1"></a>Stap 1

Met dezelfde opname in het vorige scenario klikt u op**De Hiërarchie van het protocol** bij **statistieken** > 

![menu protocolhiërarchie][2]

Het venster protocolhiërarchie wordt weergegeven. Deze weergave bevat een lijst van alle protocollen die tijdens de opnamesessie in gebruik waren en het aantal pakketten dat met behulp van de protocollen is verzonden en ontvangen. Deze weergave kan handig zijn voor het vinden van ongewenst netwerkverkeer op uw virtuele machines of netwerk.

![protocolhiërarchie geopend][3]

Zoals u zien in de volgende schermopname, was er verkeer met behulp van de BitTorrent protocol, die wordt gebruikt voor peer-to-peer file sharing. Als beheerder verwacht je niet te zien BitTorrent verkeer op deze bijzondere virtuele machines. Nu u zich bewust bent van dit verkeer, u de peer-to-peer-software verwijderen die op deze virtuele machine is geïnstalleerd, of het verkeer blokkeren met behulp van netwerkbeveiligingsgroepen of een firewall. Bovendien u ervoor kiezen om pakketopnames volgens een schema uit te voeren, zodat u het protocolgebruik op uw virtuele machines regelmatig bekijken. Voor een voorbeeld over het automatiseren van netwerktaken in azure bezoek [Netwerkbronnen monitoren met azure automation](network-watcher-monitor-with-azure-automation.md)

## <a name="finding-top-destinations-and-ports"></a>Topbestemmingen en havens vinden

Inzicht in de typen verkeer, de eindpunten en de poorten die worden gecommuniceerd, is een belangrijk belangrijk bij het bewaken of oplossen van toepassingen en resources in uw netwerk. Met behulp van een packet capture-bestand van bovenaf, kunnen we snel leren met welke topbestemmingen onze VM communiceert en de poorten die worden gebruikt.

### <a name="step-1"></a>Stap 1

Met dezelfde opname in het vorige scenario klikt u op**Bestemmingen en poorten** voor **statistieken** > **IPv4-statistieken** > 

![venster voor het vastleggen van pakketten][4]

### <a name="step-2"></a>Stap 2

Als we kijken door de resultaten een lijn opvalt, waren er meerdere aansluitingen op poort 111. De meest gebruikte poort was 3389, dat is extern bureaublad, en de resterende zijn RPC dynamische poorten.

Hoewel dit verkeer niets kan betekenen, is het een poort die voor veel verbindingen is gebruikt en onbekend is voor de beheerder.

![figuur 5][5]

### <a name="step-3"></a>Stap 3

Nu we een niet-geschikte haven hebben vastgesteld, kunnen we onze vangst filteren op basis van de haven.

Het filter in dit scenario zou zijn:

```
tcp.port == 111
```

We voeren de filtertekst van bovenaf in het filtertekstvak in en drukken op enter.

![figuur 6][6]

Uit de resultaten kunnen we zien dat al het verkeer afkomstig is van een lokale virtuele machine op hetzelfde subnet. Als we nog steeds niet begrijpen waarom dit verkeer plaatsvindt, kunnen we de pakketten verder inspecteren om te bepalen waarom het deze oproepen doet op poort 111. Met deze informatie kunnen we de juiste actie ondernemen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de andere diagnostische functies van Network Watcher door het overzicht van [Azure-netwerkbewaking](network-watcher-monitoring-overview.md) te bezoeken

[1]: ./media/network-watcher-deep-packet-inspection/figure1.png
[2]: ./media/network-watcher-deep-packet-inspection/figure2.png
[3]: ./media/network-watcher-deep-packet-inspection/figure3.png
[4]: ./media/network-watcher-deep-packet-inspection/figure4.png
[5]: ./media/network-watcher-deep-packet-inspection/figure5.png
[6]: ./media/network-watcher-deep-packet-inspection/figure6.png
[7]: ./media/network-watcher-deep-packet-inspection/figure7.png
[8]: ./media/network-watcher-deep-packet-inspection/figure8.png













