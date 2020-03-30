---
title: AMQP 1.0 in protocolgids azure servicebus en gebeurtenishubs | Microsoft Documenten
description: Protocolhandleiding voor expressies en beschrijving van AMQP 1.0 in Azure Service Bus en Event Hubs
services: service-bus-messaging,event-hubs
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: d2d3d540-8760-426a-ad10-d5128ce0ae24
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: d706e9b3351b0693a1f352e15b6b9b0cc5c7a65d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086139"
---
# <a name="amqp-10-in-azure-service-bus-and-event-hubs-protocol-guide"></a>AMQP 1.0 in protocolhandleiding Azure Service Bus en Event Hubs

Het Advanced Message Queueing Protocol 1.0 is een gestandaardiseerd framing- en transferprotocol voor asynchroon, veilig en betrouwbaar overbrengen van berichten tussen twee partijen. Dit is het primaire protocol van Azure Service Bus Messaging en Azure Event Hubs. Beide services ondersteunen ook HTTPS. Het eigen SBMP-protocol dat ook wordt ondersteund, wordt uitgefaseerd ten gunste van AMQP.

AMQP 1.0 is het resultaat van brede samenwerking in de industrie die middleware-leveranciers, zoals Microsoft en Red Hat, samenbracht met veel messaging middleware-gebruikers zoals JP Morgan Chase die de financiële dienstensector vertegenwoordigen. Het technische standaardisatieforum voor het AMQP-protocol en de uitbreidingsspecificaties is OASIS en heeft als ISO/IEC 19494 een formele goedkeuring gekregen als ISO/IEC 19494.

## <a name="goals"></a>Doelstellingen

Dit artikel vat kort de kernconcepten van de AMQP 1.0-berichtenspecificatie samen met een kleine set conceptuitbreidingsspecificaties samen die momenteel worden afgerond in de technische commissie van OASIS AMQP en legt uit hoe Azure Service Bus implementeert en bouwt voort op deze specificaties.

Het doel is dat elke ontwikkelaar elke bestaande AMQP 1.0-clientstack op elk platform gebruikt om via AMQP 1.0 te kunnen communiceren met Azure Service Bus.

Gemeenschappelijke AMQP 1.0-stacks voor algemene doeleinden, zoals Apache Proton of AMQP.NET Lite, implementeren al alle kern-AMQP 1.0-protocollen. Die fundamentele gebaren zijn soms verpakt met een api op een hoger niveau; Apache Proton biedt zelfs twee, de verplichte Messenger API en de reactieve Reactor API.

In de volgende discussie gaan we ervan uit dat het beheer van AMQP-verbindingen, sessies en koppelingen en de afhandeling van frameoverdrachten en stroombesturing worden afgehandeld door de desbetreffende stack (zoals Apache Proton-C) en niet veel of geen specifieke aandacht vereisen van applicatie-ontwikkelaars. We nemen abstract het bestaan van een paar API primitieven, zoals de mogelijkheid om verbinding te maken, `send()` `receive()` en om een vorm van *afzender* en *ontvanger* abstractie objecten, die vervolgens een of andere vorm van en operaties te creëren, respectievelijk.

Bij het bespreken van geavanceerde mogelijkheden van Azure Service Bus, zoals het browsen door berichten of het beheer van sessies, worden deze functies uitgelegd in AMQP-termen, maar ook als een gelaagde pseudo-implementatie bovenop deze veronderstelde API-abstractie.

## <a name="what-is-amqp"></a>Wat is AMQP?

AMQP is een framing en transfer protocol. Framing betekent dat het structuur biedt voor binaire gegevensstromen die in beide richtingen van een netwerkverbinding stromen. De structuur biedt afbakening voor verschillende blokken van gegevens, *frames*genaamd, worden uitgewisseld tussen de aangesloten partijen. De overdrachtsmogelijkheden zorgen ervoor dat beide communicerende partijen een gedeeld inzicht kunnen krijgen over wanneer frames moeten worden overgedragen en wanneer overdrachten als voltooid worden beschouwd.

In tegenstelling tot eerdere verlopen conceptversies die door de AMQP-werkgroep worden geproduceerd en die nog steeds worden gebruikt door een paar berichtmakelaars, schrijft het definitieve en gestandaardiseerde AMQP 1.0-protocol de aanwezigheid van een berichtenmakelaar of een bepaalde topologie voor entiteiten binnen een berichtenmakelaar.

Het protocol kan worden gebruikt voor symmetrische peer-to-peer-communicatie, voor interactie met berichtenmakelaars die wachtrijen ondersteunen en entiteiten publiceren/abonneren, zoals Azure Service Bus doet. Het kan ook worden gebruikt voor interactie met messaging-infrastructuur waar de interactiepatronen verschillen van reguliere wachtrijen, zoals het geval is met Azure Event Hubs. Een gebeurtenishub fungeert als een wachtrij wanneer gebeurtenissen naar de gebeurtenis worden verzonden, maar fungeert meer als een seriële opslagservice wanneer gebeurtenissen worden gelezen; het lijkt enigszins op een tape drive. De client kiest een verschuiving in de beschikbare gegevensstroom en wordt vervolgens alle gebeurtenissen van die offset tot de laatst beschikbare gebeurtenis geserveerd.

Het AMQP 1.0-protocol is ontworpen om uitbreidbaar te zijn, waardoor verdere specificaties de mogelijkheden kunnen verbeteren. De drie uitbreidingsspecificaties die in dit document worden besproken, illustreren dit. Voor communicatie via bestaande HTTPS/WebSockets-infrastructuur kan het configureren van de native AMQP TCP-poorten moeilijk zijn. Een bindende specificatie definieert hoe amqp over WebSockets kan worden laag. Voor interactie met de berichteninfrastructuur op een aanvraag-/antwoordmodus voor beheerdoeleinden of om geavanceerde functionaliteit te bieden, definieert de AMQP-beheerspecificatie de vereiste basisinteractieprimitieven. Voor federatieve autorisatiemodelintegratie bepaalt de OP AMQP-claims gebaseerde beveiligingsspecificatie hoe autorisatietokens die zijn gekoppeld aan koppelingen kunnen worden gekoppeld en verlengd.

## <a name="basic-amqp-scenarios"></a>Basis-AMQP-scenario's

In dit gedeelte wordt het basisgebruik van AMQP 1.0 met Azure Service Bus uitgelegd, waaronder het maken van verbindingen, sessies en koppelingen en het overbrengen van berichten van en naar servicebusentiteiten zoals wachtrijen, onderwerpen en abonnementen.

De meest gezaghebbende bron om te leren over hoe AMQP werkt is de AMQP 1.0-specificatie, maar de specificatie is geschreven om de implementatie nauwkeurig te begeleiden en het protocol niet te onderwijzen. Deze sectie richt zich op het introduceren van zoveel terminologie als nodig is voor het beschrijven van hoe Service Bus AMQP 1.0 gebruikt. Voor een uitgebreidere introductie tot AMQP, evenals een bredere discussie over AMQP 1.0, u [deze videocursus][this video course]bekijken.

### <a name="connections-and-sessions"></a>Verbindingen en sessies

AMQP noemt de communicatieprogramma's *containers*; die *knooppunten*bevatten, die de communicerende entiteiten binnen die containers zijn. Een wachtrij kan zo'n knooppunt zijn. AMQP zorgt voor multiplexing, dus een enkele verbinding kan worden gebruikt voor veel communicatiepaden tussen knooppunten; Een toepassingsclient kan bijvoorbeeld gelijktijdig van de ene wachtrij ontvangen en via dezelfde netwerkverbinding naar een andere wachtrij verzenden.

![][1]

De netwerkverbinding is dus verankerd op de container. Deze wordt geïnitieerd door de container in de clientrol die een uitgaande TCP-socketverbinding maakt met een container in de ontvangerrol, die naar binnenkomende TCP-verbindingen luistert en accepteert. De verbindingshandshake omvat het onderhandelen over de protocolversie, het declareren of onderhandelen over het gebruik van Transport Level Security (TLS/SSL) en een verificatie/autorisatiehandshake op de verbindingsscope die is gebaseerd op SASL.

Azure Service Bus vereist te allen tijde het gebruik van TLS. Het ondersteunt verbindingen via TCP-poort 5671, waarbij de TCP-verbinding eerst wordt bedekt met TLS voordat u de AMQP-protocolhandshake invoert, en ondersteunt ook verbindingen via TCP-poort 5672, waarbij de server onmiddellijk een verplichte upgrade van de verbinding aanbiedt tls met behulp van het amqp-voorgeschreven model. De AMQP WebSockets-binding maakt een tunnel over TCP-poort 443 die vervolgens gelijk is aan AMQP 5671-verbindingen.

Na het instellen van de verbinding en TLS biedt Service Bus twee SASL-mechanismeopties:

* SASL PLAIN wordt vaak gebruikt voor het doorgeven van gebruikersnaam- en wachtwoordreferenties aan een server. Service Bus heeft geen accounts, maar wel [gedeelde toegangsbeveiligingsregels,](service-bus-sas.md)die rechten verlenen en zijn gekoppeld aan een sleutel. De naam van een regel wordt gebruikt als gebruikersnaam en de sleutel (als basis64 gecodeerde tekst) wordt gebruikt als wachtwoord. De rechten die aan de gekozen regel zijn gekoppeld, bepalen de bewerkingen die op de verbinding zijn toegestaan.
* SASL ANONYMOUS wordt gebruikt voor het omzeilen van SASL-autorisatie wanneer de klant het op claims gebaseerde beveiliging (CBS)-model wil gebruiken dat later wordt beschreven. Met deze optie kan een klantverbinding voor een korte tijd anoniem tot stand worden gebracht waarin de klant alleen met het CBS-eindpunt kan communiceren en de CBS-handdruk moet worden voltooid.

Nadat de transportverbinding is ingesteld, geven de containers elk de maximale framegrootte aan die ze bereid zijn te hanteren, en na een niet-actieve time-out zullen ze eenzijdig de verbinding verbreken als er geen activiteit op de verbinding is.

Ze geven ook aan hoeveel gelijktijdige kanalen worden ondersteund. Een kanaal is een unidirectioneel, uitgaand, virtueel transferpad bovenop de verbinding. Een sessie neemt een kanaal van elk van de onderling verbonden containers om een bidirectioneel communicatiepad te vormen.

Sessies hebben een op venster gebaseerd stroombesturingselementmodel; wanneer een sessie wordt gemaakt, verklaart elke partij hoeveel frames zij bereid is te accepteren in het ontvangstvenster. Als de partijen frames uitwisselen, vullen overgedragen frames dat venster en overdrachten stoppen wanneer het venster vol is en totdat het venster wordt gereset of uitgebreid met behulp van de *stroom performatief* *(performatief* is de AMQP-term voor protocol-niveau gebaren uitgewisseld tussen de twee partijen).

Dit venstergebaseerde model is ongeveer analoog aan het TCP-concept van venstergebaseerde stroomregeling, maar op sessieniveau in de socket. Het protocol concept van het toestaan van meerdere gelijktijdige sessies bestaat, zodat hoge prioriteit verkeer kan worden gehaast langs gasgevend normaal verkeer, zoals op een snelweg snelstrook.

Azure Service Bus gebruikt momenteel precies één sessie voor elke verbinding. De maximale framegrootte van de Service Bus is 262.144 bytes (256-K bytes) voor Service Bus Standard en Event Hubs. Het is 1.048.576 (1 MB) voor Service Bus Premium. Service Bus legt geen specifieke throttlingvensters op sessieniveau op, maar stelt het venster regelmatig opnieuw in als onderdeel van de stroomregeling op koppelingsniveau (zie [de volgende sectie).](#links)

Verbindingen, kanalen en sessies zijn vluchtig. Als de onderliggende verbinding samenstort, moeten verbindingen, TLS-tunnel, SASL-autorisatiecontext en sessies opnieuw worden ingesteld.

### <a name="amqp-outbound-port-requirements"></a>AMQP-uitgaande poortvereisten

Clients die AMQP-verbindingen via TCP gebruiken, vereisen dat poorten 5671 en 5672 worden geopend in de lokale firewall. Samen met deze poorten kan het nodig zijn om extra poorten te openen als de [enablelinkredirect-functie](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.enablelinkredirect?view=azure-dotnet) is ingeschakeld. `EnableLinkRedirect`is een nieuwe berichtenfunctie die helpt bij het overslaan van one-hop tijdens het ontvangen van berichten, waardoor de doorvoer wordt verbeterd. De klant zou direct gaan communiceren met de back-end service via poortbereik 104XX, zoals in de volgende afbeelding wordt weergegeven. 

![Lijst van bestemmingshavens][4]

Een .NET-client zou mislukken met een SocketException ("Er is een poging gedaan om toegang te krijgen tot een socket op een manier die verboden is door de toegangsmachtigingen") als deze poorten door de firewall worden geblokkeerd. De functie kan worden `EnableAmqpLinkRedirect=false` uitgeschakeld door in te stellen in de connectiong-tekenreeks, waardoor de clients worden dwingt om met de externe service via poort 5671 te communiceren.


### <a name="links"></a>Koppelingen

AMQP brengt berichten over links. Een koppeling is een communicatiepad dat is gemaakt over een sessie waarmee berichten in één richting kunnen worden overgedragen. de onderhandeling over de overdrachtsstatus is over de verbinding en bi-directioneel tussen de verbonden partijen.

![][2]

Links kunnen worden gemaakt door een container op elk gewenst moment en over een bestaande sessie, waardoor AMQP anders is dan veel andere protocollen, waaronder HTTP en MQTT, waar het initiëren van overdrachten en overdrachtspad een exclusief voorrecht is van de partij die de verbinding met de aansluiting.

De container die door de koppeling wordt gestart, vraagt de tegenovergestelde container om een koppeling te accepteren en kiest een rol van afzender of ontvanger. Daarom kan een van beide containers het maken van unidirectionele of bidirectionele communicatiepaden initiëren, waarbij de laatste is gemodelleerd als koppelingenparen.

Koppelingen worden benoemd en gekoppeld aan knooppunten. Zoals in het begin vermeld, zijn knooppunten de communicerende entiteiten in een container.

In ServiceBus is een knooppunt direct gelijk aan een wachtrij, een onderwerp, een abonnement of een dodelettersubwachtrij van een wachtrij of abonnement. De naam van het knooppunt dat in AMQP wordt gebruikt, is daarom de relatieve naam van de entiteit binnen de naamruimte van de servicebus. Als een wachtrij `myqueue`wordt genoemd, is dat ook de naam van het AMQP-knooppunt. Een onderwerp abonnement volgt de HTTP API conventie door te worden gesorteerd in een "abonnementen" resource collectie en dus een abonnement **sub** op een onderwerp **mytopic** heeft de AMQP node naam **mytopic / abonnementen / sub**.

De verbindende client is ook verplicht om een lokale knooppuntnaam te gebruiken voor het maken van koppelingen; Service Bus is niet prescriptief over die knooppuntnamen en interpreteert ze niet. AMQP 1.0 client stacks maken over het algemeen gebruik van een schema om ervoor te zorgen dat deze kortstondige node namen uniek zijn in het bereik van de client.

### <a name="transfers"></a>Transfers

Zodra een link is vastgesteld, kunnen berichten worden overgedragen via die link. In AMQP wordt een overdracht uitgevoerd met een *transfer* expliciete protocolgebaar (het overdrachtsperformatief) waarmee een bericht van afzender naar ontvanger via een koppeling wordt verplaatst. Een overdracht is voltooid wanneer deze "geregeld" is, wat betekent dat beide partijen een gedeeld begrip hebben vastgesteld van de uitkomst van die overdracht.

![][3]

In het eenvoudigste geval kan de afzender ervoor kiezen om berichten te verzenden "vooraf geregeld", wat betekent dat de klant niet geïnteresseerd is in het resultaat en de ontvanger geen feedback geeft over de uitkomst van de bewerking. Deze modus wordt ondersteund door Service Bus op HET NIVEAU van het AMQP-protocol, maar wordt niet weergegeven in een van de client-API's.

Het reguliere geval is dat berichten worden verzonden onrustig, en de ontvanger geeft dan acceptatie of afwijzing met behulp van de *dispositie* performatief. Afwijzing vindt plaats wanneer de ontvanger het bericht om welke reden dan ook niet kan accepteren en het afwijzingsbericht informatie bevat over de reden, wat een foutstructuur is die door AMQP is gedefinieerd. Als berichten worden geweigerd vanwege interne fouten in Service Bus, retourneert de service extra informatie in die structuur die kan worden gebruikt voor het verstrekken van diagnostische tips om personeel te ondersteunen als u ondersteuningsverzoeken indient. U leert later meer informatie over fouten.

Een bijzondere vorm van afwijzing is de *vrijgegeven* staat, die aangeeft dat de curator geen technisch bezwaar heeft tegen de overdracht, maar ook geen belang heeft bij de afwikkeling van de overdracht. Die aanvraag bestaat bijvoorbeeld wanneer een bericht wordt afgeleverd bij een Service Bus-client en de client ervoor kiest het bericht te "verlaten" omdat het niet het werk kan uitvoeren dat voortvloeit uit het verwerken van het bericht; de berichtlevering zelf is niet in gebreke. Een variatie van die status is de *gewijzigde* status, waarmee wijzigingen in het bericht als het wordt vrijgegeven. Deze staat wordt momenteel niet gebruikt door Service Bus.

De AMQP 1.0-specificatie definieert een verdere dispositie status genaamd *ontvangen*, die specifiek helpt om link herstel te behandelen. Linkherstel maakt het mogelijk om de status van een koppeling en eventuele in behandeling zijnde leveringen opnieuw in te stellen bovenop een nieuwe verbinding en sessie, wanneer de eerdere verbinding en sessie verloren zijn gegaan.

Service Bus ondersteunt geen linkherstel; Als de client de verbinding met Service Bus verliest met een ongeregelde berichtoverdracht in behandeling, gaat die berichtoverdracht verloren en moet de client opnieuw verbinding maken, de koppeling herstellen en de overdracht opnieuw proberen.

Als zodanig ondersteunen Service Bus en Event Hubs "ten minste eenmaal" overdracht waarbij de afzender kan worden verzekerd voor het bericht is opgeslagen en geaccepteerd, maar geen ondersteuning bieden voor "precies een keer" transfers op AMQP-niveau, waar het systeem zou proberen om de link te herstellen en blijven onderhandelen over de leveringsstatus om duplicatie van de berichtoverdracht te voorkomen.

Om mogelijke dubbele verzendingen te compenseren, ondersteunt Service Bus dubbele detectie als een optionele functie voor wachtrijen en onderwerpen. Dubbele detectie registreert de bericht-id's van alle binnenkomende berichten tijdens een door de gebruiker gedefinieerd tijdvenster en laat vervolgens in stilte alle berichten die in hetzelfde venster met dezelfde bericht-id's worden verzonden, vallen.

### <a name="flow-control"></a>Stroomregeling

Naast het eerder besproken flow control-model op sessieniveau heeft elke koppeling zijn eigen flow control-model. Flow control op sessieniveau beschermt de container tegen het tegelijk verwerken van te veel frames, waarbij de stroomregeling op koppelingsniveau de leiding geeft over het aantal berichten dat de container vanuit een koppeling wil verwerken en wanneer.

![][4]

Op een link kunnen overdrachten alleen plaatsvinden als de afzender voldoende *linktegoed*heeft. Linkcredit is een teller die *flow* door de ontvanger is ingesteld met behulp van de stroomperformatieve, die is ingesteld op een link. Wanneer de afzender koppelingstegoed is toegewezen, probeert deze dat tegoed te gebruiken door berichten af te leveren. Elke berichtlevering decrements het resterende verbindingskrediet door 1. Wanneer het linktegoed is opgebruikt, stoppen de leveringen.

Wanneer Service Bus zich in de ontvangerrol bevindt, biedt deze de afzender direct voldoende linktegoed, zodat berichten onmiddellijk kunnen worden verzonden. Als linkkrediet wordt gebruikt, stuurt Service Bus af en toe een *stroompertief* naar de afzender om het linkkredietsaldo bij te werken.

In de verzendrol stuurt Service Bus berichten om een uitstaand linktegoed op te gebruiken.

Een "ontvangen" oproep op API-niveau vertaalt zich in een *flow* formatief dat door de client naar Service Bus wordt verzonden, en Service Bus verbruikt dat krediet door het eerste beschikbare, ontgrendelde bericht uit de wachtrij te nemen, het te vergrendelen en over te dragen. Als er geen bericht beschikbaar is voor levering, blijft elk uitstaand krediet door een link die met die specifieke entiteit is vastgesteld, geregistreerd in volgorde van aankomst, en worden berichten vergrendeld en overgedragen zodra ze beschikbaar zijn, om een uitstaand krediet te gebruiken.

Het slot op een bericht wordt vrijgegeven wanneer de overdracht wordt verrekend in een van de terminalstaten *die zijn geaccepteerd*, *afgewezen*of *vrijgegeven*. Het bericht wordt uit de servicebus verwijderd wanneer de terminalstatus wordt *geaccepteerd.* Het blijft in Service Bus en wordt geleverd aan de volgende ontvanger wanneer de overdracht een van de andere staten bereikt. Service Bus verplaatst het bericht automatisch naar de wachtrij voor deadletter van de entiteit wanneer het het maximale aantal verzendkosten bereikt dat is toegestaan voor de entiteit als gevolg van herhaalde afwijzingen of releases.

Hoewel de Service Bus API's niet direct een dergelijke optie bloot vandaag, een lager niveau AMQP protocol client kan gebruik maken van de link-credit model om de "pull-style" interactie van de afgifte van een eenheid van krediet voor elke ontvangen verzoek om te zetten in een "push-stijl" model door het uitgeven van een groot aantal linkcredits en ontvang vervolgens berichten zodra ze beschikbaar komen zonder verdere interactie. Push wordt ondersteund via de instellingen van de eigenschap [MessagingFactory.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) of [MessageReceiver.PrefetchCount.](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) Wanneer ze niet-nul zijn, gebruikt de AMQP-client het als het linkkrediet.

In deze context is het belangrijk om te begrijpen dat de klok voor het verstrijken van het bericht in het bericht in de entiteit begint wanneer het bericht van de entiteit wordt overgenomen, niet wanneer het bericht op de draad wordt geplaatst. Wanneer de klant aangeeft dat de klant bereid is om berichten te ontvangen door linkkrediet uit te geven, wordt daarom verwacht dat hij actief berichten over het netwerk trekt en klaar is om ze te verwerken. Anders is het berichtslot mogelijk verlopen voordat het bericht wordt bezorgd. Het gebruik van link-credit flow control moet direct de onmiddellijke bereidheid weerspiegelen om de beschikbare berichten die naar de ontvanger worden verzonden, te behandelen.

Samengevat geven de volgende secties een schematisch overzicht van de performatieve stroom tijdens verschillende API-interacties. Elke sectie beschrijft een andere logische bewerking. Sommige van deze interacties kunnen "lui" zijn, wat betekent dat ze alleen kunnen worden uitgevoerd wanneer dat nodig is. Het maken van een afzender van een bericht kan geen netwerkinteractie veroorzaken totdat het eerste bericht is verzonden of aangevraagd.

De pijlen in de volgende tabel geven de performatieve stroomrichting weer.

#### <a name="create-message-receiver"></a>Berichtontvanger maken

| Client | Service Bus |
| --- | --- |
| --> hechten(<br/>naam={linknaam},<br/>handle={numerieke handgreep},<br/>rol=**ontvanger**,<br/>source={entity name},<br/>target={clientlink-ID}<br/>) |Client hecht aan entiteit als ontvanger |
| Service Bus antwoordt het toevoegen van het einde van de link |<.<br/>naam={linknaam},<br/>handle={numerieke handgreep},<br/>rol=**afzender**,<br/>source={entity name},<br/>target={clientlink-ID}<br/>) |

#### <a name="create-message-sender"></a>Afzender van berichten maken

| Client | Service Bus |
| --- | --- |
| --> hechten(<br/>naam={linknaam},<br/>handle={numerieke handgreep},<br/>rol=**afzender**,<br/>source={client link ID},<br/>doel={naam van de entiteit}<br/>) |Geen actie |
| Geen actie |<.<br/>naam={linknaam},<br/>handle={numerieke handgreep},<br/>rol=**ontvanger**,<br/>source={client link ID},<br/>doel={naam van de entiteit}<br/>) |

#### <a name="create-message-sender-error"></a>Afzender van berichten maken (fout)

| Client | Service Bus |
| --- | --- |
| --> hechten(<br/>naam={linknaam},<br/>handle={numerieke handgreep},<br/>rol=**afzender**,<br/>source={client link ID},<br/>doel={naam van de entiteit}<br/>) |Geen actie |
| Geen actie |<.<br/>naam={linknaam},<br/>handle={numerieke handgreep},<br/>rol=**ontvanger**,<br/>source=null,<br/>target=null<br/>)<br/><br/><.<br/>handle={numerieke handgreep},<br/>gesloten=**waar**,<br/>error={error info}<br/>) |

#### <a name="close-message-receiversender"></a>Ontvanger/afzender van berichten sluiten

| Client | Service Bus |
| --- | --- |
| --> losmaken.<br/>handle={numerieke handgreep},<br/>closed=**true**<br/>) |Geen actie |
| Geen actie |<.<br/>handle={numerieke handgreep},<br/>closed=**true**<br/>) |

#### <a name="send-success"></a>Verzenden (succes)

| Client | Service Bus |
| --- | --- |
| --> overdracht(<br/>delivery-id={numerieke handgreep},<br/>delivery-tag={binaire handgreep},<br/>geregeld=**vals**,,meer=**vals**,<br/>staat=**null**,<br/>cv=**false**<br/>) |Geen actie |
| Geen actie |<.<br/>role=receiver,<br/>first={delivery ID},<br/>last={delivery ID},<br/>geregeld=**waar**,<br/>staat=**geaccepteerd**<br/>) |

#### <a name="send-error"></a>Verzenden (fout)

| Client | Service Bus |
| --- | --- |
| --> overdracht(<br/>delivery-id={numerieke handgreep},<br/>delivery-tag={binaire handgreep},<br/>geregeld=**vals**,,meer=**vals**,<br/>staat=**null**,<br/>cv=**false**<br/>) |Geen actie |
| Geen actie |<.<br/>role=receiver,<br/>first={delivery ID},<br/>last={delivery ID},<br/>geregeld=**waar**,<br/>staat=**afgewezen**(<br/>error={error info}<br/>)<br/>) |

#### <a name="receive"></a>Ontvangen

| Client | Service Bus |
| --- | --- |
| --> stroom(<br/>link-credit=1<br/>) |Geen actie |
| Geen actie |< transfer.<br/>delivery-id={numerieke handgreep},<br/>delivery-tag={binaire handgreep},<br/>geregeld=**vals**,<br/>meer=**vals**,<br/>staat=**null**,<br/>cv=**false**<br/>) |
| --> dispositie(<br/>rol=**ontvanger**,<br/>first={delivery ID},<br/>last={delivery ID},<br/>geregeld=**waar**,<br/>staat=**geaccepteerd**<br/>) |Geen actie |

#### <a name="multi-message-receive"></a>Ontvangen meerdere berichten

| Client | Service Bus |
| --- | --- |
| --> stroom(<br/>link-credit=3<br/>) |Geen actie |
| Geen actie |< transfer.<br/>delivery-id={numerieke handgreep},<br/>delivery-tag={binaire handgreep},<br/>geregeld=**vals**,<br/>meer=**vals**,<br/>staat=**null**,<br/>cv=**false**<br/>) |
| Geen actie |< transfer.<br/>delivery-id={numerieke handgreep+1},<br/>delivery-tag={binaire handgreep},<br/>geregeld=**vals**,<br/>meer=**vals**,<br/>staat=**null**,<br/>cv=**false**<br/>) |
| Geen actie |< transfer.<br/>delivery-id={numerieke handgreep+2},<br/>delivery-tag={binaire handgreep},<br/>geregeld=**vals**,<br/>meer=**vals**,<br/>staat=**null**,<br/>cv=**false**<br/>) |
| --> dispositie(<br/>role=receiver,<br/>first={delivery ID},<br/>last={delivery ID+2},<br/>geregeld=**waar**,<br/>staat=**geaccepteerd**<br/>) |Geen actie |

### <a name="messages"></a>Berichten

In de volgende secties wordt uitgelegd welke eigenschappen van de standaard AMQP-berichtsecties worden gebruikt door Service Bus en hoe deze worden toegewezen aan de API-set servicebus.

Elke eigenschap die toepassing moet definieert, moet worden `application-properties` toegewezen aan de kaart van AMQP.

#### <a name="header"></a>koptekst

| Veldnaam | Gebruik | API-naam |
| --- | --- | --- |
| Duurzaam |- |- |
| priority |- |- |
| ttl |Tijd om te leven voor dit bericht |[TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| eerste-acquirer |- |- |
| aantal levering |- |[Aantal bezorging](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="properties"></a>properties

| Veldnaam | Gebruik | API-naam |
| --- | --- | --- |
| bericht-id |Toepassingsgedefinieerde, vrije-form id voor dit bericht. Wordt gebruikt voor dubbele detectie. |[Messageid](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| user-id |Door toepassingen gedefinieerde gebruikers-id, niet geïnterpreteerd door Service Bus. |Niet toegankelijk via de Api servicebus. |
| tot |Toepassingsgedefinieerde bestemmings-id, niet geïnterpreteerd door Service Bus. |[Aan](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| Onderwerp |Toepassingsgedefinieerde berichtdoel-id, niet geïnterpreteerd door Service Bus. |[Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| reply-to |Toepassingsgedefinieerde antwoordpadindicator, niet geïnterpreteerd door Service Bus. |[ReplyTo](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| correlation-id |Door toepassing gedefinieerde correlatie-id, niet geïnterpreteerd door Service Bus. |[CorrelationId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| inhoudstype |Toepassingsgedefinieerde inhoudstype-indicator voor de body, niet geïnterpreteerd door Service Bus. |[Contenttype](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| inhoudscodering |Toepassingsgedefinieerde content-encoding-indicator voor de body, niet geïnterpreteerd door Service Bus. |Niet toegankelijk via de Api servicebus. |
| absolute-expiry-time |Verklaart op welk moment het bericht verloopt. Genegeerd op input (header TTL wordt waargenomen), gezaghebbend op output. |[Verlooptatutc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| creatie-tijd |Verklaart op welk moment het bericht is gemaakt. Niet gebruikt door Service Bus |Niet toegankelijk via de Api servicebus. |
| groeps-id |Toepassingsgedefinieerde id voor een gerelateerde set berichten. Gebruikt voor Service Bus sessies. |[Sessionid](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| groepsreeks |Teller die het relatieve volgordenummer van het bericht in een sessie identificeert. Genegeerd door Service Bus. |Niet toegankelijk via de Api servicebus. |
| reply-to-group-id |- |[ReplyToSessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="message-annotations"></a>Aantekeningen voor berichten

Er zijn weinig andere servicebusberichtberichten, die geen deel uitmaken van amqp-berichteigenschappen en worden doorgegeven zoals `MessageAnnotations` in het bericht.

| Kaarttoets annotatie | Gebruik | API-naam |
| --- | --- | --- |
| x-opt-scheduled-enqueue-time | Verklaart op welk tijdstip het bericht op de entiteit moet worden weergegeven |[Geplandewachtrijtijd](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.scheduledenqueuetimeutc?view=azure-dotnet) |
| x-opt-partitietoets | Toepassingsgedefinieerde sleutel die bepaalt in welke partitie het bericht moet landen. | [PartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.partitionkey?view=azure-dotnet) |
| x-opt-via-partition-key | Toepassingsgedefinieerde partitiesleutelwaarde wanneer een transactie moet worden gebruikt om berichten via een transferwachtrij te verzenden. | [ViaPartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.viapartitionkey?view=azure-dotnet) |
| x-opt-inqueued-time | Servicegedefinieerde UTC-tijd die de werkelijke tijd van het uiten van het bericht weergeeft. Genegeerd bij invoer. | [InqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc?view=azure-dotnet) |
| x-opt-sequence-nummer | Servicegedefinieerd uniek nummer dat is toegewezen aan een bericht. | [SequenceNummer](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber?view=azure-dotnet) |
| x-opt-offset | Service-gedefinieerd volgordenummer van het bericht in de wachtrij. | [In het aantal in de wachtrij plaatsen](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedsequencenumber?view=azure-dotnet) |
| x-opt-locked-until | Service-gedefinieerd. De datum en tijd tot welke het bericht wordt vergrendeld in de wachtrij/abonnement. | [Vergrendeldtotutc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.lockeduntilutc?view=azure-dotnet) |
| x-opt-deadletter-bron | Service gedefinieerd. Als het bericht wordt ontvangen uit de wachtrij met dode letters, wordt de bron van het oorspronkelijke bericht weergegeven. | [DeadLetterSource](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deadlettersource?view=azure-dotnet) |

### <a name="transaction-capability"></a>Transactiemogelijkheden

Een transactie groepeert twee of meer bewerkingen in een uitvoeringsbereik. Een dergelijke transactie moet er van nature voor zorgen dat alle transacties die tot een bepaalde groep transacties behoren, gezamenlijk slagen of mislukken.
De bewerkingen worden gegroepeerd `txn-id`op een id .

Voor transactionele interactie fungeert de `transaction controller` client als een , die de bewerkingen regelt die moeten worden gegroepeerd. Service Bus Service `transactional resource` fungeert als een en `transaction controller`voert werkzaamheden uit zoals gevraagd door de .

De klant en service `control link` communiceren over een , die is vastgesteld door de klant. De `declare` `discharge` berichten en berichten worden verzonden door de controller over de controle link toe te wijzen en transacties te voltooien respectievelijk (ze vertegenwoordigen niet de afbakening van transactionele werk). Het daadwerkelijke verzenden/ontvangen wordt niet uitgevoerd op deze link. Elke gevraagde transactionele bewerking wordt expliciet geïdentificeerd `txn-id` met de gewenste en kan daarom plaatsvinden op elke link op de verbinding. Als de controlekoppeling wordt gesloten terwijl er niet-geloste transacties zijn die zijn gemaakt, worden al deze transacties onmiddellijk teruggedraaid en zullen pogingen om verdere transactionele werkzaamheden aan deze transacties uit te voeren, leiden tot mislukken. Berichten op de controlekoppeling mogen niet vooraf worden vereffend.

Elke verbinding moet een eigen controlekoppeling initiëren om transacties te kunnen starten en beëindigen. De service definieert een speciaal `coordinator`doel dat functioneert als een . De client/controller legt een besturingskoppeling naar dit doel. Controlekoppeling valt buiten de grens van een entiteit, dat wil zeggen dat dezelfde controlekoppeling kan worden gebruikt om transacties voor meerdere entiteiten te initiëren en te ontladen.

#### <a name="starting-a-transaction"></a>Een transactie starten

Om te beginnen met transactioneel werk. de verwerkingsverantwoordelijke `txn-id` moet een van de coördinator verkrijgen. Het doet dit `declare` door het verzenden van een type bericht. Als de verklaring succesvol is, reageert de coördinator `txn-id`met een dispositieresultaat, dat toegewezen draagt.

| Client (controller) | | ServiceBus (Coördinator) |
| --- | --- | --- |
| bevestig(<br/>naam={linknaam},<br/>... ,<br/>rol=**afzender**,<br/>target=**Coördinator**<br/>) | ------> |  |
|  | <------ | bevestig(<br/>naam={linknaam},<br/>... ,<br/>target=Coördinator()<br/>) |
| overdracht(<br/>delivery-id=0, ...)<br/>{ AmqpValue (**Declareer()**)}| ------> |  |
|  | <------ | beschikking( <br/> first=0, last=0, <br/>staat=**Aangegeven**(<br/>**txn-id**={transactie-ID}<br/>))|

#### <a name="discharging-a-transaction"></a>Het uitvoeren van een transactie

De controller rondt het transactionele werk af door een `discharge` bericht te sturen naar de coördinator. De verwerkingsverantwoordelijke geeft aan dat hij het transactionele `fail` werk wil vastleggen of terugdraaien door de vlag op de ontladingsinstantie in te stellen. Als de coördinator niet in staat is om de `transaction-error`kwijting te voltooien, wordt het bericht afgewezen met deze uitkomst met de .

> Opmerking: fail=true verwijst naar terugdraaien van een transactie en fail=false verwijst naar Commit.

| Client (controller) | | ServiceBus (Coördinator) |
| --- | --- | --- |
| overdracht(<br/>delivery-id=0, ...)<br/>{ AmqpValue (Declare())}| ------> |  |
|  | <------ | beschikking( <br/> first=0, last=0, <br/>state=Declared(<br/>txn-id={transactie-ID}<br/>))|
| | . . . <br/>Transactioneel werk<br/>op andere links<br/> . . . |
| overdracht(<br/>delivery-id=57,...)<br/>{ AmqpValue (<br/>**Ontlading(txn-id=0,<br/>fail=false)**)}| ------> |  |
| | <------ | beschikking( <br/> first=57, last=57, <br/>staat=**Geaccepteerd()**)|

#### <a name="sending-a-message-in-a-transaction"></a>Een bericht verzenden in een transactie

Al het transactionele werk wordt gedaan `transactional-state` met de transactionele leveringsstatus die de txn-id draagt. In het geval van het verzenden van berichten wordt de transactionele status gedragen door het overdrachtsframe van het bericht. 

| Client (controller) | | ServiceBus (Coördinator) |
| --- | --- | --- |
| overdracht(<br/>delivery-id=0, ...)<br/>{ AmqpValue (Declare())}| ------> |  |
|  | <------ | beschikking( <br/> first=0, last=0, <br/>state=Declared(<br/>txn-id={transactie-ID}<br/>))|
| overdracht(<br/>handle=1,<br/>delivery-id=1, <br/>**staat=<br/>TransactionalState(<br/>txn-id=0)**)<br/>{ payload }| ------> |  |
| | <------ | beschikking( <br/> first=1, last=1, <br/>staat=**<br/>TransactionalState( txn-id=0,<br/>outcome=Accepted()**))|

#### <a name="disposing-a-message-in-a-transaction"></a>Een bericht in een transactie verwijderen

Berichtdispositie `Complete`  /  `Abandon`  /  `DeadLetter`  /  `Defer`omvat bewerkingen zoals . Als u deze bewerkingen binnen `transactional-state` een transactie wilt uitvoeren, moet u de met de dispositie doorgeven.

| Client (controller) | | ServiceBus (Coördinator) |
| --- | --- | --- |
| overdracht(<br/>delivery-id=0, ...)<br/>{ AmqpValue (Declare())}| ------> |  |
|  | <------ | beschikking( <br/> first=0, last=0, <br/>state=Declared(<br/>txn-id={transactie-ID}<br/>))|
| | <------ |overdracht(<br/>handle=2,<br/>delivery-id=11, <br/>state=null)<br/>{ payload }|  
| beschikking( <br/> first=11, last=11, <br/>staat=**<br/>TransactionalState( txn-id=0,<br/>outcome=Accepted()**))| ------> |


## <a name="advanced-service-bus-capabilities"></a>Geavanceerde servicebusmogelijkheden

Deze sectie behandelt geavanceerde mogelijkheden van Azure Service Bus die zijn gebaseerd op concept-extensies voor AMQP, die momenteel worden ontwikkeld in het OASIS Technical Committee voor AMQP. Service Bus implementeert de nieuwste versies van deze concepten en keurt wijzigingen aan die zijn ingevoerd omdat deze concepten de standaardstatus bereiken.

> [!NOTE]
> Geavanceerde services busberichten worden ondersteund via een aanvraag-/antwoordpatroon. De details van deze bewerkingen worden beschreven in het artikel [AMQP 1.0 in Service Bus: request-response-based operations](service-bus-amqp-request-response.md).
> 
> 

### <a name="amqp-management"></a>AMQP-beheer

De AMQP-beheerspecificatie is de eerste van de conceptextensies die in dit artikel worden besproken. Deze specificatie definieert een set protocollen die bovenop het AMQP-protocol zijn gelaagd en die beheerinteracties met de berichteninfrastructuur via AMQP mogelijk maken. De specificatie definieert algemene bewerkingen zoals *maken,* *lezen,* *bijwerken*en *verwijderen* voor het beheren van entiteiten binnen een berichteninfrastructuur en een set querybewerkingen.

Al deze gebaren vereisen een interactie tussen verzoek/antwoord tussen de client en de berichteninfrastructuur, en daarom bepaalt de specificatie hoe u dat interactiepatroon boven OP AMQP modelleren: de client maakt verbinding met de berichteninfrastructuur, initieert een sessie en maakt vervolgens een paar koppelingen. Op de ene link fungeert de klant als afzender en aan de andere kant fungeert hij als ontvanger, waardoor een paar links ontstaat die als bidirectioneel kanaal kunnen fungeren.

| Logische bewerking | Client | Service Bus |
| --- | --- | --- |
| Pad voor het antwoord op aanvragen maken |--> hechten(<br/>naam={*linknaam*},<br/>handle={*numerieke handgreep*},<br/>rol=**afzender**,<br/>bron=**null**,<br/>target="myentity/$management"<br/>) |Geen actie |
| Pad voor het antwoord op aanvragen maken |Geen actie |\<-- hechten(<br/>naam={*linknaam*},<br/>handle={*numerieke handgreep*},<br/>rol=**ontvanger**,<br/>source=null,<br/>target="myentity"<br/>) |
| Pad voor het antwoord op aanvragen maken |--> hechten(<br/>naam={*linknaam*},<br/>handle={*numerieke handgreep*},<br/>rol=**ontvanger**,<br/>bron="myentity/$management",<br/>target="myclient$id"<br/>) | |
| Pad voor het antwoord op aanvragen maken |Geen actie |\<-- hechten(<br/>naam={*linknaam*},<br/>handle={*numerieke handgreep*},<br/>rol=**afzender**,<br/>source="myentity",<br/>target="myclient$id"<br/>) |

Met dat paar koppelingen is de implementatie van het verzoek/antwoord eenvoudig: een verzoek is een bericht dat wordt verzonden naar een entiteit binnen de berichteninfrastructuur die dit patroon begrijpt. In dat verzoekbericht wordt het *antwoordveld* in de sectie *eigenschappen* ingesteld op de *doel-id* voor de koppeling waarop het antwoord moet worden geleverd. De verwerkingsentiteit verwerkt de aanvraag en geeft het antwoord vervolgens via de koppeling waarvan de *doel-id* overeenkomt met de aangegeven *reply-to-id.*

Het patroon vereist uiteraard dat de clientcontainer en de door de client gegenereerde id voor de antwoordbestemming uniek zijn voor alle clients en om veiligheidsredenen ook moeilijk te voorspellen zijn.

De berichtuitwisselingen die worden gebruikt voor het beheerprotocol en voor alle andere protocollen die hetzelfde patroon gebruiken, vinden plaats op toepassingsniveau; ze definiëren geen nieuwe AMQP-protocol-niveau gebaren. Dat is opzettelijk, zodat toepassingen direct kunnen profiteren van deze extensies met compatibele AMQP 1.0-stacks.

Service Bus implementeert momenteel geen van de kernkenmerken van de managementspecificatie, maar het aanvraag-/antwoordpatroon dat wordt gedefinieerd door de managementspecificatie is fundamenteel voor de op claims gebaseerde beveiligingsfunctie en voor bijna alle geavanceerde mogelijkheden die in de volgende secties worden besproken:

### <a name="claims-based-authorization"></a>Autorisatie op basis van claims

Het CBS-specificatieconcept (AMQP Claims-Based-Authorization) bouwt voort op het aanvraag/responspatroon voor managementspecificatie en beschrijft een algemeen model voor het gebruik van federatieve beveiligingstokens met AMQP.

Het standaard beveiligingsmodel van AMQP dat in de inleiding wordt besproken, is gebaseerd op SASL en integreert met de AMQP-verbindingshandshake. Het gebruik van SASL heeft het voordeel dat het een uitbreidbaar model biedt waarvoor een reeks mechanismen is gedefinieerd waarvan elk protocol dat formeel op SASL leunt, kan profiteren. Onder deze mechanismen zijn "PLAIN" voor de overdracht van gebruikersnamen en wachtwoorden, "EXTERN" te binden aan TLS-niveau beveiliging, "ANONIEM" om de afwezigheid van expliciete authenticatie / autorisatie uit te drukken, en een breed scala aan extra mechanismen die het mogelijk maken het passeren van authenticatie en / of autorisatie referenties of tokens.

AmQP's SASL integratie heeft twee nadelen:

* Alle referenties en tokens zijn scoped aan de verbinding. Een berichten-infrastructuur kan een gedifferentieerde toegangscontrole per entiteit willen bieden; bijvoorbeeld, zodat de drager van een token naar wachtrij A kan worden verzonden, maar niet in wachtrij B. Met de autorisatiecontext die is verankerd in de verbinding, is het niet mogelijk om één verbinding te gebruiken en toch verschillende toegangstokens te gebruiken voor wachtrij A en wachtrij B.
* Toegangstokens zijn meestal slechts tijdelijk geldig. Deze geldigheid vereist dat de gebruiker periodiek tokens opnieuw verwerft en biedt de tokenuitgever de mogelijkheid om de uitgifte van een nieuw token te weigeren als de toegangsmachtigingen van de gebruiker zijn gewijzigd. AMQP-verbindingen kunnen voor langere tijd duren. Het SASL-model biedt alleen een kans om een token in te stellen op verbindingstijd, wat betekent dat de berichteninfrastructuur de client moet loskoppelen wanneer het token verloopt, ofwel het risico moet accepteren dat verdere communicatie wordt toegestaan met een klant die toegangsrechten kunnen tussentijds zijn ingetrokken.

De AMQP CBS-specificatie, geïmplementeerd door Service Bus, maakt een elegante oplossing mogelijk voor beide problemen: het stelt een client in staat om toegangstokens aan elk knooppunt te koppelen en deze tokens bij te werken voordat ze verlopen, zonder de berichtstroom te onderbreken.

CBS definieert een virtueel beheerknooppunt, genaamd *$cbs,* dat wordt geleverd door de berichteninfrastructuur. Het beheerknooppunt accepteert tokens namens andere knooppunten in de berichteninfrastructuur.

Het protocolgebaar is een uitwisseling tussen aanvragen en antwoorden zoals gedefinieerd in de beheerspecificatie. Dat betekent dat de client een paar koppelingen met het *$cbs* knooppunt maakt en vervolgens een verzoek doorgeeft op de uitgaande koppeling en vervolgens wacht op het antwoord op de binnenkomende koppeling.

Het aanvraagbericht heeft de volgende toepassingseigenschappen:

| Sleutel | Optioneel | Waardetype | Inhoud van waarde |
| --- | --- | --- | --- |
| Bewerking |Nee |tekenreeks |**put-token** |
| type |Nee |tekenreeks |Het type token dat wordt geplaatst. |
| name |Nee |tekenreeks |Het "publiek" waarop het token van toepassing is. |
| Verloop |Ja |tijdstempel |De vervaldatum van het token. |

De *eigenschap naam* identificeert de entiteit waarmee het token moet worden gekoppeld. In Service Bus is dit het pad naar de wachtrij, of onderwerp / abonnement. De *eigenschap type* identificeert het tokentype:

| Tokentype | Tokenbeschrijving | Lichaamstype | Opmerkingen |
| --- | --- | --- | --- |
| amqp:jwt |JSON-webtoken (JWT) |AMQP-waarde (tekenreeks) |Nog niet beschikbaar. |
| amqp:swt |Eenvoudige webtoken (SWT) |AMQP-waarde (tekenreeks) |Alleen ondersteund voor SWT-tokens uitgegeven door AAD/ACS |
| servicebus.windows.net:sastoken |Servicebus SAS-token |AMQP-waarde (tekenreeks) |- |

Tokens verlenen rechten. Service Bus kent drie grondrechten: "Send" maakt verzenden, "Luisteren" mogelijk en "Beheren" maakt het manipuleren van entiteiten mogelijk. SWT-tokens uitgegeven door AAD/ACS bevatten deze rechten expliciet als claims. SAS-tokens voor servicebussen verwijzen naar regels die zijn geconfigureerd op de naamruimte of entiteit en deze regels zijn geconfigureerd met rechten. Als u het token ondertekent met de sleutel die aan die regel is gekoppeld, drukt het token dus de betreffende rechten uit. Het token dat is gekoppeld aan een entiteit die *put-token* gebruikt, stelt de verbonden client in staat om te communiceren met de entiteit per de tokenrechten. Een koppeling waarbij de client de *verzendrol* op zich neemt, vereist het recht 'Verzenden'; het nemen van de *ontvanger* rol vereist de "Luisteren" recht.

Het antwoordbericht heeft de volgende *waarden voor toepassingseigenschappen*

| Sleutel | Optioneel | Waardetype | Inhoud van waarde |
| --- | --- | --- | --- |
| statuscode |Nee |int |HTTP-antwoordcode **[RFC2616]**. |
| statusbeschrijving |Ja |tekenreeks |Beschrijving van de status. |

De client kan herhaaldelijk *put-token* bellen en voor elke entiteit in de berichteninfrastructuur. De tokens zijn scoped naar de huidige client en verankerd op de huidige verbinding, wat betekent dat de server alle bewaarde tokens laat vallen wanneer de verbinding daalt.

De huidige Service Bus implementatie staat alleen CBS in combinatie met de SASL methode "ANONIEM." Er moet altijd een SSL/TLS-verbinding bestaan vóór de SASL-handdruk.

Het ANONYMOUS mechanisme moet daarom ondersteund worden door de gekozen AMQP 1.0 client. Anonieme toegang betekent dat de eerste verbindingshanddruk, inclusief het maken van de eerste sessie, plaatsvindt zonder dat Service Bus weet wie de verbinding maakt.

Zodra de verbinding en sessie is vastgesteld, zijn het koppelen van de koppelingen aan het *$cbs* knooppunt en het verzenden van de *put-token* aanvraag de enige toegestane bewerkingen. Een geldig token moet binnen 20 seconden na het tot stand zijn gebracht met een *put-tokenaanvraag* voor een entiteitsknooppunt worden ingesteld, anders wordt de verbinding eenzijdig verwijderd door Service Bus.

De klant is vervolgens verantwoordelijk voor het bijhouden van token expiratie. Wanneer een token verloopt, laat Service Bus onmiddellijk alle koppelingen op de verbinding met de desbetreffende entiteit vallen. Om te voorkomen dat er problemen optreden, kan de client het token *$cbs* voor het knooppunt op elk gewenst moment vervangen door een nieuw token door het virtuele $cbs-beheerknooppunt met hetzelfde *put-token-gebaar* en zonder het payload-verkeer dat op verschillende links stroomt in de weg te staan.

### <a name="send-via-functionality"></a>Send-via-functionaliteit

[Verzend-via / Transfer afzender](service-bus-transactions.md#transfers-and-send-via) is een functionaliteit waarmee service bus doorsturen van een bepaald bericht naar een doelentiteit via een andere entiteit. Deze functie wordt gebruikt om bewerkingen uit te voeren tussen entiteiten in één transactie.

Met deze functionaliteit maakt u een afzender en `via-entity`legt u de koppeling naar de . Bij het leggen van de link wordt aanvullende informatie doorgegeven om de werkelijke bestemming van de berichten/overdrachten op deze link vast te stellen. Zodra de attached succesvol is, worden alle berichten die op deze link worden verzonden automatisch doorgestuurd naar de *bestemmingsentiteit* via *de via-entiteit*. 

> Opmerking: Verificatie moet worden uitgevoerd voor zowel *via-entiteit* als *bestemmingsentiteit* voordat deze koppeling wordt gemaakt.

| Client | | Service Bus |
| --- | --- | --- |
| bevestig(<br/>naam={linknaam},<br/>role=sender,<br/>source={client link ID},<br/>doel=**{via-entiteit}**,<br/>**eigenschappen=kaart [(<br/>com.microsoft:transfer-destination-address=<br/>{destination-entity} )]** ) | ------> | |
| | <------ | bevestig(<br/>naam={linknaam},<br/>role=receiver,<br/>source={client link ID},<br/>target={via-entity},<br/>properties=map [(<br/>com.microsoft:transfer-destination-address=<br/>{destination-entity} )] ) |

## <a name="next-steps"></a>Volgende stappen

Ga voor meer informatie over AMQP naar de volgende links:

* [Overzicht servicebus AMQP]
* [AMQP 1.0-ondersteuning voor wachtrijen en onderwerpen voor servicebus]
* [AMQP in servicebus voor Windows Server]

[this video course]: https://www.youtube.com/playlist?list=PLmE4bZU0qx-wAP02i0I7PJWvDWoCytEjD
[1]: ./media/service-bus-amqp-protocol-guide/amqp1.png
[2]: ./media/service-bus-amqp-protocol-guide/amqp2.png
[3]: ./media/service-bus-amqp-protocol-guide/amqp3.png
[4]: ./media/service-bus-amqp-protocol-guide/amqp4.png

[Overzicht servicebus AMQP]: service-bus-amqp-overview.md
[AMQP 1.0-ondersteuning voor wachtrijen en onderwerpen voor servicebus]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[AMQP in servicebus voor Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
