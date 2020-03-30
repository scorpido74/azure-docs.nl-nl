---
title: Prestatierichtlijnen voor Azure SignalR Service
description: Een overzicht van de prestaties en benchmark van Azure SignalR Service. Belangrijke statistieken om rekening mee te houden bij het plannen van de capaciteit.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 68cad32be177fa20794399157fca89e87c2f8f59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74157663"
---
# <a name="performance-guide-for-azure-signalr-service"></a>Prestatierichtlijnen voor Azure SignalR Service

Een van de belangrijkste voordelen van het gebruik van Azure SignalR Service is het gemak van het schalen van SignalR-toepassingen. In een grootschalig scenario zijn prestaties een belangrijke factor. 

In deze handleiding introduceren we de factoren die de prestaties van de SignalR-toepassing beïnvloeden. We beschrijven typische prestaties in verschillende use-case scenario's. Uiteindelijk introduceren we de omgeving en tools die u gebruiken om een prestatierapport te genereren.

## <a name="term-definitions"></a>Term definities

*Binnenkomend:* het binnenkomende bericht naar Azure SignalR-service.

*Uitgaand:* het uitgaande bericht van Azure SignalR Service.

*Bandbreedte*: De totale grootte van alle berichten in 1 seconde.

*Standaardmodus:* de standaardwerkmodus wanneer een Azure SignalR Service-instantie is gemaakt. Azure SignalR Service verwacht dat de app-server een verbinding met deze verbinding tot stand zal brengen voordat deze clientverbindingen accepteert.

*Serverloze modus:* een modus waarin Azure SignalR Service alleen clientverbindingen accepteert. Er is geen serververbinding toegestaan.

## <a name="overview"></a>Overzicht

Azure SignalR Service definieert zeven standaardlagen voor verschillende prestatiecapaciteiten. Deze gids beantwoordt de volgende vragen:

-   Wat zijn de typische Azure SignalR Service-prestaties voor elke laag?

-   Voldoet Azure SignalR Service aan mijn vereisten voor berichtdoorvoer (bijvoorbeeld het verzenden van 100.000 berichten per seconde)?

-   Voor mijn specifieke scenario, welke laag is geschikt voor mij? Of hoe kan ik de juiste laag selecteren?

-   Wat voor soort app server (VM-grootte) is geschikt voor mij? Hoeveel moet ik er inzetten?

Om deze vragen te beantwoorden, geeft deze gids eerst een verklaring op hoog niveau van de factoren die de prestaties beïnvloeden. Het illustreert vervolgens de maximale inkomende en uitgaande berichten voor elke laag voor typische use cases: **echo,** **uitzending,** **verzenden naar groep**en verzenden naar **verbinding** (peer-to-peer chatten).

Deze handleiding kan niet alle scenario's behandelen (en verschillende use cases, berichtformaten, berichtverzendpatronen, enzovoort). Maar het biedt een aantal methoden om u te helpen:

- Evalueer uw geschatte vereiste voor de binnenkomende of uitgaande berichten.
- Zoek de juiste lagen door de prestatietabel te controleren.

## <a name="performance-insight"></a>Inzicht in prestaties

In dit gedeelte worden de prestatie-evaluatiemethoden beschreven en worden alle factoren weergegeven die van invloed zijn op de prestaties. Uiteindelijk biedt het methoden om u te helpen de prestatievereisten te evalueren.

### <a name="methodology"></a>Methodologie

*Doorvoer* en *latentie* zijn twee typische aspecten van prestatiecontrole. Voor Azure SignalR-service heeft elke SKU-laag zijn eigen doorvoerbeperkingsbeleid. Het beleid definieert *de maximaal toegestane doorvoer (inkomende en uitgaande bandbreedte)* als de maximaal bereikte doorvoer wanneer 99 procent van de berichten latentie heeft die minder dan 1 seconde bedraagt.

Latentie is de tijdspanne van de verbinding die het bericht verzendt tot het ontvangen van het antwoordbericht van Azure SignalR Service. Laten we **echo** als voorbeeld nemen. Elke clientverbinding voegt een tijdstempel toe in het bericht. De hub van de app-server stuurt het oorspronkelijke bericht terug naar de client. Dus de propagatie vertraging is gemakkelijk berekend door elke client verbinding. De tijdstempel is gekoppeld voor elk bericht in **uitzending,** **verzenden naar de groep,** en **verzenden naar verbinding**.

Om duizenden gelijktijdige clientverbindingen te simuleren, worden meerdere VM's gemaakt in een virtueel privénetwerk in Azure. Al deze VM's maken verbinding met dezelfde Azure SignalR Service-instantie.

In de standaardmodus van Azure SignalR Service worden vm's van app-server geïmplementeerd in hetzelfde virtuele privénetwerk als client-VM's. Alle clientVM's en vm's van de app-server worden geïmplementeerd in hetzelfde netwerk van dezelfde regio om latentie tussen regio's te voorkomen.

### <a name="performance-factors"></a>Prestatiefactoren

In theorie wordt de capaciteit van azure SignalR-service beperkt door berekeningsbronnen: CPU, geheugen en netwerk. Meer verbindingen met Azure SignalR Service zorgen er bijvoorbeeld voor dat de service meer geheugen gebruikt. Voor groter berichtverkeer (elk bericht is bijvoorbeeld groter dan 2.048 bytes) moet Azure SignalR Service meer CPU-cycli besteden om verkeer te verwerken. Ondertussen legt Azure-netwerkbandbreedte ook een limiet op voor maximaal verkeer.

Het transporttype is een andere factor die de prestaties beïnvloedt. De drie typen zijn [WebSocket,](https://en.wikipedia.org/wiki/WebSocket) [Server-Sent-Event](https://en.wikipedia.org/wiki/Server-sent_events)en [Long-Polling](https://en.wikipedia.org/wiki/Push_technology). 

WebSocket is een bidirectioneel en full-duplex communicatieprotocol via één TCP-verbinding. Server-Sent-Event is een unidirectioneel protocol om berichten van server naar client te pushen. Long-Polling vereist dat de clients periodiek informatie van de server peilen via een HTTP-aanvraag. Voor dezelfde API onder dezelfde voorwaarden heeft WebSocket de beste prestaties, is Server-Sent-Event trager en is Long-Polling de langzaamste. Azure SignalR Service raadt WebSocket standaard aan.

De kosten voor het routeren van berichten beperken ook de prestaties. Azure SignalR Service speelt een rol als een berichtrouter, die het bericht van een set clients of servers naar andere clients of servers leidt. Een ander scenario of API vereist een ander routeringsbeleid. 

Voor **echo**stuurt de client een bericht naar zichzelf en de routeringsbestemming is ook zichzelf. Dit patroon heeft de laagste routeringskosten. Maar voor **uitzending,** **verzenden naar groep**en verzenden naar **verbinding**, Azure SignalR Service moet opzoeken van de doelverbindingen via de interne gedistribueerde gegevensstructuur. Deze extra verwerking maakt gebruik van meer CPU, geheugen en netwerkbandbreedte. Als gevolg hiervan zijn de prestaties trager.

In de standaardmodus kan de app-server ook een knelpunt worden voor bepaalde scenario's. De Azure SignalR SDK moet de hub aanroepen, terwijl deze een live verbinding met elke client behoudt via hartslagsignalen.

In de serverloze modus stuurt de client een bericht per HTTP-bericht, dat niet zo efficiënt is als WebSocket.

Een andere factor is protocol: JSON en [MessagePack](https://msgpack.org/index.html). MessagePack is kleiner in omvang en sneller geleverd dan JSON. MessagePack verbetert echter mogelijk de prestaties niet. De prestaties van Azure SignalR Service zijn niet gevoelig voor protocollen omdat het de payload van het bericht niet wordt gedecodeerd tijdens het doorsturen van berichten van clients naar servers of vice versa.

Samengevat zijn de volgende factoren van invloed op de inkomende en uitgaande capaciteit:

-   SKU-laag (CPU/geheugen)

-   Aantal verbindingen

-   Berichtgrootte

-   Berichtverzendsnelheid

-   Transporttype (WebSocket, Server-Sent-Event of Long-Polling)

-   Use-case scenario (routeringskosten)

-   App-server- en serviceverbindingen (in de servermodus)


### <a name="finding-a-proper-sku"></a>Het vinden van een goede SKU

Hoe u de inkomende/uitgaande capaciteit evalueren of vinden welke laag geschikt is voor een specifieke use case?

Stel dat de app-server krachtig genoeg is en niet het knelpunt in de prestaties is. Controleer vervolgens de maximale inkomende en uitgaande bandbreedte voor elke laag.

#### <a name="quick-evaluation"></a>Snelle evaluatie

Laten we eerst de evaluatie vereenvoudigen door een aantal standaardinstellingen aan te nemen: 

- Het transporttype is WebSocket.
- De grootte van het bericht is 2.048 bytes.
- Elke 1 seconde wordt er een bericht verzonden.
- Azure SignalR-service bevindt zich in de standaardmodus.

Elke laag heeft zijn eigen maximale inkomende bandbreedte en uitgaande bandbreedte. Een soepele gebruikerservaring is niet gegarandeerd nadat de binnenkomende of uitgaande verbinding de limiet overschrijdt.

**Echo** geeft de maximale inkomende bandbreedte omdat deze de laagste routekosten heeft. **Broadcast** definieert de maximale uitgaande berichtbandbreedte.

De gemarkeerde waarden in de volgende twee tabellen *niet* overschrijden.

|       Echo                        | Eenheid1 | Eenheid2 | Eenheid5 | Eenheid10 | Eenheid20 | Eenheid50 | Eenheid100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Verbindingen                       | 1000 | 2.000 | 5.000 | 10.000 | 20.000 | 50,000 | 100.000 |
| **Binnenkomende bandbreedte** | **2 Mbps**    | **4 Mbps**    | **10 Mbps**   | **20 MBps**    | **40 MBps**    | **100 MBps**   | **200 MBps**    |
| Uitgaande bandbreedte | 2 Mbps   | 4 Mbps   | 10 Mbps  | 20 MBps   | 40 MBps   | 100 MBps  | 200 MBps   |


|     Uitzenden             | Eenheid1 | Eenheid2 | Eenheid5  | Eenheid10 | Eenheid20 | Eenheid50  | Eenheid100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Verbindingen               | 1000 | 2.000 | 5.000  | 10.000 | 20.000 | 50,000  | 100.000 |
| Binnenkomende bandbreedte  | 4 KBps   | 4 KBps   | 4 KBps    | 4 KBps    | 4 KBps    | 4 KBps     | 4 KBps    |
| **Uitgaande bandbreedte** | **4 Mbps**    | **8 MBps**    | **20 MBps**    | **40 MBps**    | **80 MBps**    | **200 MBps**    | **400 MBps**   |

*Binnenkomende bandbreedte* en *uitgaande bandbreedte* zijn de totale berichtgrootte per seconde.  Hier zijn de formules voor hen:
```
  inboundBandwidth = inboundConnections * messageSize / sendInterval
  outboundBandwidth = outboundConnections * messageSize / sendInterval
```

- *inkomende verbindingen:* het aantal verbindingen dat het bericht verzendt.

- *uitgaande verbindingen:* het aantal verbindingen dat het bericht ontvangt.

- *messageSize:* de grootte van één bericht (gemiddelde waarde). Een klein bericht met minder dan 1024 bytes heeft een prestatie-impact die vergelijkbaar is met een bericht van 1.024 bytes.

- *sendInterval:* het tijdstip van het verzenden van één bericht. Meestal is het 1 seconde per bericht, wat betekent dat het verzenden van een bericht per seconde. Een kleiner interval betekent het verzenden van meer berichten in een periode. Bijvoorbeeld, 0,5 seconden per bericht betekent het verzenden van twee berichten per seconde.

- *Verbindingen:* de vastgelegde maximale drempel voor Azure SignalR Service voor elke laag. Als het verbindingsnummer verder wordt verhoogd, zal het last hebben van verbindingsbeperking.

#### <a name="evaluation-for-complex-use-cases"></a>Evaluatie voor complexe use cases

##### <a name="bigger-message-size-or-different-sending-rate"></a>Grotere berichtgrootte of andere verzendsnelheid

De echte use case is ingewikkelder. Het kan een bericht verzenden dat groter is dan 2.048 bytes, of de verzendsnelheid is niet één bericht per seconde. Laten we unit100's uitzending als voorbeeld nemen om te zien hoe de prestaties ervan te evalueren.

De volgende tabel toont een echte use case van **uitzending**. Maar de grootte van het bericht, het aantal verbindingen en de verzendsnelheid van berichten zijn anders dan in de vorige sectie. De vraag is hoe we een van deze items kunnen afleiden (berichtgrootte, aantal verbindingen of het verzendpercentage van berichten) als we er slechts twee kennen.

| Uitzenden  | Berichtgrootte | Binnenkomende berichten per seconde | Verbindingen | Intervallen verzenden |
|---|---------------------|--------------------------|-------------|-------------------------|
| 1 | 20 KB                | 1                        | 100.000     | 5 sec                      |
| 2 | 256 kB               | 1                        | 8,000       | 5 sec                      |

De volgende formule is eenvoudig af te leiden op basis van de vorige formule:

```
outboundConnections = outboundBandwidth * sendInterval / messageSize
```

Voor Unit100 is de maximale uitgaande bandbreedte 400 MB uit de vorige tabel. Voor een berichtgrootte van 20 KB moeten de maximale \* uitgaande verbindingen 400 MB 5 / 20 KB = 100.000 zijn, wat overeenkomt met de werkelijke waarde.

##### <a name="mixed-use-cases"></a>Gemengde use cases

De echte use case mengt meestal de vier basisuse cases samen: **echo,** **uitzending,** **verzenden naar groep**en verzenden naar **verbinding**. De methode die u gebruikt om de capaciteit te evalueren is:

1. Verdeel de gemengde use cases in vier basisuse cases.
1. Bereken de maximale inkomende en uitgaande berichtbandbreedte met behulp van de voorgaande formules afzonderlijk.
1. Som de bandbreedteberekeningen op om de totale maximale inkomende/uitgaande bandbreedte te krijgen. 

Pak vervolgens de juiste laag op uit de maximale inkomende/uitgaande bandbreedtetabellen.

> [!NOTE]
> Voor het verzenden van een bericht naar honderden of duizenden kleine groepen, of voor duizenden klanten die een bericht naar elkaar sturen, worden de routeringskosten dominant. Houd rekening met deze impact.

Controleer ervoor dat de app-server *niet* het knelpunt vormt voor het verzenden van een bericht naar clients. In de volgende sectie 'Casestudy' vindt u richtlijnen over het aantal app-servers dat u nodig hebt en hoeveel serververbindingen u moet configureren.

## <a name="case-study"></a>Casestudy

De volgende secties gaan door vier typische use cases voor WebSocket transport: **echo,** **uitzending,** **verzenden naar groep**, en verzenden naar **verbinding**. Voor elk scenario wordt in de sectie de huidige inkomende en uitgaande capaciteit voor Azure SignalR Service weergegeven. Het verklaart ook de belangrijkste factoren die de prestaties beïnvloeden.

In de standaardmodus maakt de app-server vijf serververbindingen met Azure SignalR Service. De app-server maakt standaard gebruik van de Azure SignalR Service SDK. In de volgende prestatietestresultaten worden serververbindingen verhoogd tot 15 (of meer voor het uitzenden en verzenden van een bericht naar een grote groep).

Verschillende use cases hebben verschillende vereisten voor app-servers. **Broadcast** heeft een klein aantal app-servers nodig. **Echo** of **verzenden naar verbinding** heeft veel app-servers nodig.

In alle gebruiksgevallen is de standaardberichtgrootte 2.048 bytes en het interval voor het verzenden van berichten 1 seconde.

### <a name="default-mode"></a>Standaardmodus

Clients, web-app-servers en Azure SignalR-service zijn betrokken bij de standaardmodus. Elke klant staat voor één verbinding.

#### <a name="echo"></a>Echo

Eerst maakt een web-app verbinding met Azure SignalR Service. Ten tweede maken veel clients verbinding met de web-app, die de clients doorverwijst naar Azure SignalR Service met het access token en eindpunt. Vervolgens maken de clients WebSocket-verbindingen met Azure SignalR Service.

Nadat alle clients verbindingen hebben gelegd, beginnen ze elke seconde een bericht te verzenden met een tijdstempel naar de specifieke hub. De hub echoot het bericht terug naar de oorspronkelijke client. Elke client berekent de latentie wanneer het echobericht wordt teruggegeven.

In het volgende diagram bevinden 5 tot en met 8 (rood gemarkeerd verkeer) zich in een lus. De lus wordt uitgevoerd voor een standaardduur (5 minuten) en krijgt de statistiek van alle berichtlatentie.

![Verkeer voor de echo use case](./media/signalr-concept-performance/echo.png)

Het gedrag van **echo** bepaalt dat de maximale inkomende bandbreedte gelijk is aan de maximale uitgaande bandbreedte. Zie de volgende tabel voor details.

|       Echo                        | Eenheid1 | Eenheid2 | Eenheid5 | Eenheid10 | Eenheid20 | Eenheid50 | Eenheid100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Verbindingen                       | 1000 | 2.000 | 5.000 | 10.000 | 20.000 | 50,000 | 100.000 |
| Binnenkomende/uitgaande berichten per seconde | 1000 | 2.000 | 5.000 | 10.000 | 20.000 | 50,000 | 100.000 |
| Binnenkomende/uitgaande bandbreedte | 2 Mbps   | 4 Mbps   | 10 Mbps  | 20 MBps   | 40 MBps   | 100 MBps  | 200 MBps   |

In dit gebruiksgeval roept elke client de hub aan die is gedefinieerd in de app-server. De hub noemt gewoon de methode gedefinieerd in de oorspronkelijke client kant. Deze hub is de meest lichtgewicht hub voor **echo.**

```
        public void Echo(IDictionary<string, object> data)
        {
            Clients.Client(Context.ConnectionId).SendAsync("RecordLatency", data);
        }
```

Zelfs voor deze eenvoudige hub is de verkeersdruk op de app-server prominent naarmate de **inkomende** berichtbelasting van echo's toeneemt. Deze verkeersdruk vereist veel app-servers voor grote SKU-lagen. In de volgende tabel ziet u het aantal app-servers voor elke laag.


|    Echo          | Eenheid1 | Eenheid2 | Eenheid5 | Eenheid10 | Eenheid20 | Eenheid50 | Eenheid100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Verbindingen      | 1000 | 2.000 | 5.000 | 10.000 | 20.000 | 50,000 | 100.000 |
| Aantal app-servers | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Het verbindingsnummer van de clientverbinding, de grootte van het bericht, de verzendsnelheid van berichten, de SKU-laag en het CPU/geheugen van de app-server beïnvloeden de algehele prestaties van **echo.**

#### <a name="broadcast"></a>Uitzenden

Voor **uitzending**, wanneer de web-app ontvangt het bericht, het wordt uitgezonden naar alle clients. Hoe meer klanten er zijn om uit te zenden, hoe meer berichtverkeer er is voor alle klanten. Zie het volgende diagram.

![Verkeer voor de gebruikscase voor uitzending](./media/signalr-concept-performance/broadcast.png)

Een klein aantal klanten zendt uit. De inkomende berichtbandbreedte is klein, maar de uitgaande bandbreedte is enorm. De bandbreedte van uitgaande berichten neemt toe naarmate de clientverbinding of uitzendsnelheid toeneemt.

In de volgende tabel worden de maximale clientverbindingen, het aantal binnenkomende/uitgaande berichten en de bandbreedte samengevat.

|     Uitzenden             | Eenheid1 | Eenheid2 | Eenheid5  | Eenheid10 | Eenheid20 | Eenheid50  | Eenheid100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Verbindingen               | 1000 | 2.000 | 5.000  | 10.000 | 20.000 | 50,000  | 100.000 |
| Binnenkomende berichten per seconde  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Uitgaande berichten per seconde | 2.000 | 4000 | 10.000 | 20.000 | 40,000 | 100.000 | 200.000 |
| Binnenkomende bandbreedte  | 4 KBps   | 4 KBps   | 4 KBps    | 4 KBps    | 4 KBps    | 4 KBps     | 4 KBps     |
| Uitgaande bandbreedte | 4 Mbps   | 8 MBps   | 20 MBps   | 40 MBps   | 80 MBps   | 200 MBps   | 400 MBps   |

De omroepclients die berichten plaatsen zijn niet meer dan vier. Ze hebben minder app-servers nodig in vergelijking met **echo** omdat het aantal binnenkomende berichten klein is. Twee app-servers zijn voldoende voor zowel SLA- als prestatieoverwegingen. Maar u moet de standaardserververbindingen verhogen om onbalans te voorkomen, vooral voor Unit50 en Unit100.

|   Uitzenden      | Eenheid1 | Eenheid2 | Eenheid5 | Eenheid10 | Eenheid20 | Eenheid50 | Eenheid100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Verbindingen      | 1000 | 2.000 | 5.000 | 10.000 | 20.000 | 50,000 | 100.000 |
| Aantal app-servers | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Verhoog de standaardserververbindingen van 5 naar 40 op elke app-server om mogelijke ongebalanceerde serververbindingen met Azure SignalR Service te voorkomen.
>
> Het verbindingsnummer van de clientverbinding, de grootte van het bericht, de verzendsnelheid van berichten en de SKU-laag zijn van invloed op de algehele prestaties voor **uitzending**.

#### <a name="send-to-group"></a>Verzenden naar groep

De **use case send to group** heeft een vergelijkbaar verkeerspatroon om uit te **zenden.** Het verschil is dat nadat clients WebSocket-verbindingen met Azure SignalR Service hebben gemaakt, ze lid moeten worden van groepen voordat ze een bericht naar een specifieke groep kunnen verzenden. Het volgende diagram illustreert de verkeersstroom.

![Verkeer voor de usecase send-to-group](./media/signalr-concept-performance/sendtogroup.png)

Groepsleden en groepstelling zijn twee factoren die van invloed zijn op de prestaties. Om de analyse te vereenvoudigen, definiëren we twee soorten groepen:

- **Kleine groep**: Elke groep heeft 10 verbindingen. Het groepsnummer is gelijk aan (max. aantal verbindingen) / 10. Bijvoorbeeld, voor Unit1, als er 1.000 verbindingstellingen zijn, dan hebben we 1000 / 10 = 100 groepen.

- **Grote groep**: Het groepsnummer is altijd 10. Het aantal groepsleden is gelijk aan (maximaal aantal verbindingen) / 10. Bijvoorbeeld, voor Unit1, als er 1.000 verbindingstellingen zijn, dan heeft elke groep 1000 / 10 = 100 leden.

**Verzenden naar groep** brengt routeringskosten met zich mee naar Azure SignalR Service omdat deze de doelverbindingen moet vinden via een gedistribueerde gegevensstructuur. Naarmate de verzendverbindingen toenemen, stijgen de kosten.

##### <a name="small-group"></a>Kleine groep

De routeringskosten zijn aanzienlijk voor het verzenden van berichten naar veel kleine groepen. Momenteel bereikt de Azure SignalR Service-implementatie de limiet voor routeringskosten bij Unit50. Het toevoegen van meer CPU en geheugen helpt niet, dus Unit100 kan niet verder verbeteren door het ontwerp. Als u meer binnenkomende bandbreedte nodig hebt, neemt u contact op met de klantenservice.

|   Verzenden naar kleine groep     | Eenheid1 | Eenheid2 | Eenheid5  | Eenheid10 | Eenheid20 | Eenheid50 | Eenheid100 |
|---------------------------|-------|-------|--------|--------|--------|--------|---------|
| Verbindingen               | 1000 | 2.000 | 5.000  | 10.000 | 20.000 | 50,000 | 100.000
| Aantal groepsleden        | 10    | 10    | 10     | 10     | 10     | 10     | 10 
| Groepsaantal               | 100   | 200   | 500    | 1000  | 2.000  | 5.000  | 10.000 
| Binnenkomende berichten per seconde  | 200   | 400   | 1000  | 2500  | 4000  | 7,000  | 7,000   |
| Binnenkomende bandbreedte  | 400 KBps  | 800 KBps  | 2 Mbps     | 5 Mbps     | 8 MBps     | 14 MBps    | 14 MBps     |
| Uitgaande berichten per seconde | 2.000 | 4000 | 10.000 | 25,000 | 40,000 | 70,000 | 70,000  |
| Uitgaande bandbreedte | 4 Mbps    | 8 MBps    | 20 MBps    | 50 MBps     | 80 MBps    | 140 MBps   | 140 MBps    |

Veel clientverbindingen bellen de hub, dus het app-servernummer is ook essentieel voor de prestaties. In de volgende tabel worden de voorgestelde app-serveraantallen weergegeven.

|  Verzenden naar kleine groep   | Eenheid1 | Eenheid2 | Eenheid5 | Eenheid10 | Eenheid20 | Eenheid50 | Eenheid100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Verbindingen      | 1000 | 2.000 | 5.000 | 10.000 | 20.000 | 50,000 | 100.000 |
| Aantal app-servers | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Het verbindingsnummer van de clientverbinding, berichtgrootte, verzendsnelheid van berichten, routeringskosten, SKU-laag en CPU/geheugen van de app-server beïnvloeden de algehele prestaties van **verzenden naar kleine groepen**.

##### <a name="big-group"></a>Grote groep

Voor **verzenden naar grote groep**wordt de uitgaande bandbreedte het knelpunt voordat u de routeringskostenlimiet bereikt. In de volgende tabel vindt u de maximale uitgaande bandbreedte, die bijna hetzelfde is als die voor **uitzending.**

|    Verzenden naar grote groep      | Eenheid1 | Eenheid2 | Eenheid5  | Eenheid10 | Eenheid20 | Eenheid50  | Eenheid100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Verbindingen               | 1000 | 2.000 | 5.000  | 10.000 | 20.000 | 50,000  | 100.000
| Aantal groepsleden        | 100   | 200   | 500    | 1000  | 2.000  | 5.000   | 10.000 
| Groepsaantal               | 10    | 10    | 10     | 10     | 10     | 10      | 10
| Binnenkomende berichten per seconde  | 20    | 20    | 20     | 20     | 20     | 20      | 20      |
| Binnenkomende bandbreedte  | 80 KBps   | 40 KBps   | 40 KBps    | 20 KBps    | 40 KBps    | 40 KBps     | 40 KBps     |
| Uitgaande berichten per seconde | 2.000 | 4000 | 10.000 | 20.000 | 40,000 | 100.000 | 200.000 |
| Uitgaande bandbreedte | 8 MBps    | 8 MBps    | 20 MBps    | 40 MBps    | 80 MBps    | 200 MBps    | 400 MBps    |

Het aantal verzendende verbindingen is niet meer dan 40. De belasting op de app-server is klein, dus het voorgestelde aantal web-apps is klein.

|  Verzenden naar grote groep  | Eenheid1 | Eenheid2 | Eenheid5 | Eenheid10 | Eenheid20 | Eenheid50 | Eenheid100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Verbindingen      | 1000 | 2.000 | 5.000 | 10.000 | 20.000 | 50,000 | 100.000 |
| Aantal app-servers | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Verhoog de standaardserververbindingen van 5 naar 40 op elke app-server om mogelijke ongebalanceerde serververbindingen met Azure SignalR Service te voorkomen.
> 
> Het klantverbindingsnummer, de grootte van het bericht, de verzendsnelheid van berichten, de routeringskosten en de SKU-laag zijn van invloed op de algehele prestaties van **verzenden naar grote groepen**.

#### <a name="send-to-connection"></a>Verzenden naar verbinding

In de **usecase verzenden naar verbinding** belt elke client een speciale hub om een eigen verbindings-id te krijgen wanneer clients de verbindingen met Azure SignalR Service tot stand brengen. De prestatiebenchmark verzamelt alle verbindings-id's, schudt ze door en wijst ze opnieuw toe aan alle clients als verzenddoel. De clients blijven het bericht naar de doelverbinding verzenden totdat de prestatietest is voltooid.

![Verkeer voor de send-to-client use case](./media/signalr-concept-performance/sendtoclient.png)

De routeringskosten voor **verzenden naar verbinding** zijn vergelijkbaar met de kosten voor het verzenden naar kleine **groepen**.

Naarmate het aantal verbindingen toeneemt, worden de routekosten beperkt de algehele prestaties. Unit50 heeft de limiet bereikt. Als gevolg hiervan kan Unit100 niet verder verbeteren.

De volgende tabel is een statistisch overzicht na vele rondes van het uitvoeren van de **send to connection** benchmark.

|   Verzenden naar verbinding   | Eenheid1 | Eenheid2 | Eenheid5 | Eenheid10 | Eenheid20 | Eenheid50          | Eenheid100         |
|------------------------------------|-------|-------|-------|--------|--------|-----------------|-----------------|
| Verbindingen                        | 1000 | 2.000 | 5.000 | 10.000 | 20.000 | 50,000          | 100.000         |
| Binnenkomende/uitgaande berichten per seconde | 1000 | 2.000 | 5.000 | 8,000  | 9000  | 20.000 | 20.000 |
| Binnenkomende/uitgaande bandbreedte | 2 Mbps    | 4 Mbps    | 10 Mbps   | 16 MBps    | 18 MBps    | 40 MBps       | 40 MBps       |

Deze use case vereist een hoge belasting aan de kant van de app-server. Zie het aantal voorgestelde app-serverinde volgende tabel.

|  Verzenden naar verbinding  | Eenheid1 | Eenheid2 | Eenheid5 | Eenheid10 | Eenheid20 | Eenheid50 | Eenheid100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Verbindingen      | 1000 | 2.000 | 5.000 | 10.000 | 20.000 | 50,000 | 100.000 |
| Aantal app-servers | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Het verbindingsnummer van de clientverbinding, berichtgrootte, verzendsnelheid van berichten, routeringskosten, SKU-laag en CPU/geheugen voor de app-server beïnvloeden de algehele prestaties van **verzenden naar verbinding.**

#### <a name="aspnet-signalr-echo-broadcast-and-send-to-small-group"></a>ASP.NET SignalR echo, uitzending en verzenden naar kleine groep

Azure SignalR Service biedt dezelfde prestatiecapaciteit voor ASP.NET SignalR. 

De prestatietest maakt gebruik van Azure Web Apps van [Standard Service Plan S3](https://azure.microsoft.com/pricing/details/app-service/windows/) voor ASP.NET SignalR.

In de volgende tabel wordt het aantal voorgestelde web-apps voor ASP.NET **Signaalterugecho gegeven.**

|   Echo           | Eenheid1 | Eenheid2 | Eenheid5 | Eenheid10 | Eenheid20 | Eenheid50 | Eenheid100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Verbindingen      | 1000 | 2.000 | 5.000 | 10.000 | 20.000 | 50,000 | 100.000 |
| Aantal app-servers | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

In de volgende tabel wordt het aantal voorgestelde web-apps voor ASP.NET **SignalR-uitzending gegeven.**

|  Uitzenden       | Eenheid1 | Eenheid2 | Eenheid5 | Eenheid10 | Eenheid20 | Eenheid50 | Eenheid100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Verbindingen      | 1000 | 2.000 | 5.000 | 10.000 | 20.000 | 50,000 | 100.000 |
| Aantal app-servers | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

In de volgende tabel wordt het aantal voorgestelde web-apps vermeld voor ASP.NET SignalR **naar kleine groep wordt verzonden.**

|  Verzenden naar kleine groep     | Eenheid1 | Eenheid2 | Eenheid5 | Eenheid10 | Eenheid20 | Eenheid50 | Eenheid100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Verbindingen      | 1000 | 2.000 | 5.000 | 10.000 | 20.000 | 50,000 | 100.000 |
| Aantal app-servers | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

### <a name="serverless-mode"></a>Serverloze modus

Clients en Azure SignalR Service zijn betrokken bij de serverloze modus. Elke klant staat voor één verbinding. De client stuurt berichten via de REST API naar een andere client of zendt berichten uit naar iedereen.

Het verzenden van berichten met een hoge dichtheid via de REST API is niet zo efficiënt als het gebruik van WebSocket. Het vereist dat u elke keer een nieuwe HTTP-verbinding bouwt, en dat is een extra kostenpost in de serverloze modus.

#### <a name="broadcast-through-rest-api"></a>Uitzenden via REST API
Alle clients maken WebSocket-verbindingen met Azure SignalR Service. Vervolgens beginnen sommige clients te uitzenden via de REST API. Het verzenden van berichten (binnenkomend) verloopt allemaal via HTTP Post, wat niet efficiënt is in vergelijking met WebSocket.

|   Uitzenden via REST API     | Eenheid1 | Eenheid2 | Eenheid5  | Eenheid10 | Eenheid20 | Eenheid50  | Eenheid100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Verbindingen               | 1000 | 2.000 | 5.000  | 10.000 | 20.000 | 50,000  | 100.000 |
| Binnenkomende berichten per seconde  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Uitgaande berichten per seconde | 2.000 | 4000 | 10.000 | 20.000 | 40,000 | 100.000 | 200.000 |
| Binnenkomende bandbreedte  | 4 KBps    | 4 KBps    | 4 KBps     | 4 KBps     | 4 KBps     | 4 KBps      | 4 KBps      |
| Uitgaande bandbreedte | 4 Mbps    | 8 MBps    | 20 MBps    | 40 MBps    | 80 MBps    | 200 MBps    | 400 MBps    |

#### <a name="send-to-user-through-rest-api"></a>Verzenden naar gebruiker via REST API
De benchmark kent gebruikersnamen toe aan alle clients voordat ze verbinding maken met Azure SignalR Service. Nadat de clients WebSocket-verbindingen hebben gemaakt met Azure SignalR Service, beginnen ze berichten naar anderen te verzenden via HTTP Post.

|   Verzenden naar gebruiker via REST API | Eenheid1 | Eenheid2 | Eenheid5  | Eenheid10 | Eenheid20 | Eenheid50  | Eenheid100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Verbindingen               | 1000 | 2.000 | 5.000  | 10.000 | 20.000 | 50,000  | 100.000 |
| Binnenkomende berichten per seconde  | 300   | 600   | 900    | 1,300  | 2.000  | 10.000  | 18.000  |
| Uitgaande berichten per seconde | 300   | 600   | 900    | 1,300  | 2.000  | 10.000  | 18.000 |
| Binnenkomende bandbreedte  | 600 KBps  | 1,2 MBps  | 1,8 MBps   | 2,6 MBps   | 4 Mbps     | 10 Mbps     | 36 MBps    |
| Uitgaande bandbreedte | 600 KBps  | 1,2 MBps  | 1,8 MBps   | 2,6 MBps   | 4 Mbps     | 10 Mbps     | 36 MBps    |

## <a name="performance-test-environments"></a>Prestatietestomgevingen

Voor alle eerder genoemde use cases hebben we de prestatietests uitgevoerd in een Azure-omgeving. Hooguit gebruikten we 50 client VM's en 20 app server VM's. Hier volgt enige informatie:

- Client VM-formaat: StandardDS2V2 (2 vCPU, 7G-geheugen)

- VM-formaat app-server: StandardF4sV2 (4 vCPU, 8G-geheugen)

- Azure SignalR SDK-serververbindingen: 15

## <a name="performance-tools"></a>Prestatiehulpmiddelen

U prestatiehulpprogramma's voor Azure SignalR-service vinden op [GitHub.](https://github.com/Azure/azure-signalr-bench/)

## <a name="next-steps"></a>Volgende stappen

In dit artikel krijgt u een overzicht van de prestaties van Azure SignalR Service in typische use-case scenario's.

Lees de volgende hulplijnen om meer informatie te krijgen over de interne van de service en deze te schalen:

* [Interne werking van Azure SignalR Service](signalr-concept-internals.md)
* [Azure SignalR Service schalen](signalr-howto-scale-multi-instances.md)
