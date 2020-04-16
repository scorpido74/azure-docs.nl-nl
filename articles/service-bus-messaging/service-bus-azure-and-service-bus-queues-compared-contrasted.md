---
title: Wachtrijen voor Azure Storage en wachtrijen voor servicebussen vergelijken
description: Analyseert verschillen en overeenkomsten tussen twee soorten wachtrijen die door Azure worden aangeboden.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: f07301dc-ca9b-465c-bd5b-a0f99bab606b
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 09/04/2019
ms.author: aschhab
ms.openlocfilehash: ffa98e511053edc75fd0e6f25f7b0e21ee9ddda0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414538"
---
# <a name="storage-queues-and-service-bus-queues---compared-and-contrasted"></a>Storage Queues and Service Bus Queues - Compared and Contrasted (Storage-wachtrijen en Service Bus-wachtrijen: overeenkomsten en verschillen)
In dit artikel worden de verschillen en overeenkomsten tussen de twee typen wachtrijen die Microsoft Azure vandaag de dag biedt, geanalyseerd: opslagwachtrijen en wachtrijen voor servicebussen. Door deze informatie te gebruiken, u de respectieve technologieën vergelijken en contrasteren en een beter geïnformeerde beslissing kunnen nemen over welke oplossing het beste aan uw behoeften voldoet.

## <a name="introduction"></a>Inleiding
Azure ondersteunt twee typen wachtrijmechanismen: **opslagwachtrijen** en **wachtrijen voor servicebussen**.

**Opslagwachtrijen**, die deel uitmaken van de [Azure-opslaginfrastructuur,](https://azure.microsoft.com/services/storage/) zijn voorzien van een eenvoudige REST-gebaseerde GET/PUT/PEEK-interface, die betrouwbare, permanente berichten binnen en tussen services biedt.

**Servicebuswachtrijen** maken deel uit van een bredere [Azure-berichteninfrastructuur](https://azure.microsoft.com/services/service-bus/) die wachtrijen ondersteunt, evenals publiceren/abonneren en meer geavanceerde integratiepatronen. Zie het [overzicht van Service Bus](service-bus-messaging-overview.md)voor meer informatie over wachtrijen voor servicebus/onderwerpen/abonnementen.

Hoewel beide wachtrijtechnologieën gelijktijdig bestaan, werden opslagwachtrijen als eerste geïntroduceerd, als een speciaal wachtrijopslagmechanisme dat is gebouwd bovenop Azure Storage-services. ServiceBuswachtrijen zijn gebouwd bovenop de bredere berichteninfrastructuur die is ontworpen om toepassingen of toepassingscomponenten te integreren die meerdere communicatieprotocollen, gegevenscontracten, vertrouwensdomeinen en/of netwerkomgevingen kunnen omvatten.

## <a name="technology-selection-considerations"></a>Overwegingen voor de selectie van technologie
Zowel opslagwachtrijen als wachtrijen voor servicebus zijn implementaties van de berichtenwachtrijservice die momenteel wordt aangeboden door Microsoft Azure. Elk heeft een iets andere functieset, wat betekent dat u het ene of het andere kiezen, of beide gebruiken, afhankelijk van de behoeften van uw specifieke oplossing of bedrijfs-/technische probleem dat u oplost.

Bij het bepalen welke rijtechnologie past bij het doel van een bepaalde oplossing, moeten oplossingsarchitecten en ontwikkelaars deze aanbevelingen in overweging nemen. Zie het volgende gedeelte voor meer informatie.

Als oplossingsarchitect/ontwikkelaar **kunt u opslagwachtrijen gebruiken** wanneer:

* Uw toepassing moet meer dan 80 GB aan berichten in een wachtrij opslaan.
* Uw toepassing wil de voortgang bijhouden voor het verwerken van een bericht in de wachtrij. Dit is handig als de werknemer die een bericht verwerkt, vastloopt. Een volgende werknemer kan die informatie vervolgens gebruiken om verder te gaan vanaf de plaats waar de vorige werknemer was gebleven.
* U hebt serverlogboeken nodig van alle transacties die zijn uitgevoerd in het tegen uw wachtrijen.

Als oplossingsarchitect/ontwikkelaar **moet u overwegen wachtrijen voor servicebus te gebruiken** wanneer:

* Uw oplossing moet berichten kunnen ontvangen zonder de wachtrij te hoeven peilen. Met Service Bus kan dit worden bereikt door het gebruik van de long-polling receive-bewerking met behulp van de TCP-gebaseerde protocollen die Service Bus ondersteunt.
* Uw oplossing vereist dat de wachtrij een gegarandeerde first-in-first-out (FIFO) bestelde levering biedt.
* Uw oplossing moet automatische dubbele detectie kunnen ondersteunen.
* U wilt dat uw toepassing berichten verwerkt als parallelle langlopende streams (berichten zijn gekoppeld aan een stream met de eigenschap [SessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid) in het bericht). In dit model concurreert elk knooppunt in de verbruikende toepassing voor streams, in tegenstelling tot berichten. Wanneer een stream wordt gegeven aan een verbruikend knooppunt, kan het knooppunt de status van de toepassingsstroomstatus onderzoeken met behulp van transacties.
* Uw oplossing vereist transactioneel gedrag en atomaire bij het verzenden of ontvangen van meerdere berichten vanuit een wachtrij.
* Uw toepassing verwerkt berichten die 64 KB kunnen overschrijden, maar zal waarschijnlijk niet de 256 KB-limiet benaderen.
* U hebt te maken met een vereiste om een op rollen gebaseerd toegangsmodel voor de wachtrijen en verschillende rechten/machtigingen voor afzenders en ontvangers te bieden. Raadpleeg voor meer informatie de volgende artikelen:
    - [Verifiëren met beheerde identiteiten](service-bus-managed-service-identity.md)
    - [Verifiëren vanuit een toepassing](authenticate-application.md)
* Uw wachtrijgrootte niet groter wordt dan 80 GB.
* U wilt het op AMQP 1.0-standaarden gebaseerde berichtenprotocol gebruiken. Zie [Service Bus AMQP Overview](service-bus-amqp-overview.md)voor meer informatie over AMQP.
* U zich een eventuele migratie voorstellen van op wachtrijen gebaseerde point-to-point-communicatie naar een berichtuitwisselingspatroon dat naadloze integratie van extra ontvangers (abonnees) mogelijk maakt, die elk onafhankelijke kopieën ontvangen van sommige of alle berichten die naar de wachtrij worden verzonden. Deze laatste verwijst naar de publicatie/subscribe-mogelijkheid die native wordt geleverd door Service Bus.
* Uw berichtenoplossing moet de leveringsgarantie "At-Most-Once" kunnen ondersteunen zonder dat u de extra infrastructuurcomponenten hoeft te bouwen.
* U graag de mogelijkheid wilt hebben om batches van berichten te publiceren en gebruiken.

## <a name="comparing-storage-queues-and-service-bus-queues"></a>Opslagwachtrijen en wachtrijen voor servicebussen vergelijken
De tabellen in de volgende secties bieden een logische groepering van wachtrijfuncties en laten u in één oogopslag de mogelijkheden vergelijken die beschikbaar zijn in zowel Azure Storage-wachtrijen als wachtrijen voor servicebussen.

## <a name="foundational-capabilities"></a>Fundamentele capaciteiten
In deze sectie worden enkele van de fundamentele wachtrijmogelijkheden van opslagwachtrijen en wachtrijen voor servicebussen vergeleken.

| Vergelijkingscriteria | Opslagwachtrijen | Service Bus-wachtrijen |
| --- | --- | --- |
| Bestelgarantie |**Nee** <br/><br>Zie de eerste notitie in de sectie Aanvullende informatie voor meer informatie.</br> |**Ja - First-In-First-Out (FIFO)**<br/><br>(door het gebruik van berichtensessies) |
| Leveringsgarantie |**Ten minste één keer** |**At-Least-Once** (met behulp van PeekLock ontvangen modus - dit is de standaard) <br/><br/>**At-Most-Once** (met receive-modus ontvangen) <br/> <br/> Meer informatie over verschillende [modus Ontvangen](service-bus-queues-topics-subscriptions.md#receive-modes)  |
| Ondersteuning voor atoombewerking |**Nee** |**Ja**<br/><br/> |
| Gedrag ontvangen |**Niet-blokkeren**<br/><br/>(voltooit onmiddellijk als er geen nieuw bericht wordt gevonden) |**Blokkeren met/zonder time-out**<br/><br/>(biedt lange polling, of de ["Comet techniek"](https://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>**Niet-blokkeren**<br/><br/>(alleen via het gebruik van .NET managed API) |
| API in pushstijl |**Nee** |**Ja**<br/><br/>[QueueClient.OnMessage en](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage#Microsoft_ServiceBus_Messaging_QueueClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__) [MessageSessionHandler.OnMessage-sessies](/dotnet/api/microsoft.servicebus.messaging.messagesessionhandler.onmessage#Microsoft_ServiceBus_Messaging_MessageSessionHandler_OnMessage_Microsoft_ServiceBus_Messaging_MessageSession_Microsoft_ServiceBus_Messaging_BrokeredMessage__) .NET API. |
| Ontvangstmodus |**Peek & Lease** |**Peek & Slot**<br/><br/>**& verwijderen ontvangen** |
| Exclusieve toegangsmodus |**Lease-gebaseerde** |**Op basis van vergrendeling** |
| Huur-/vergrendelingsduur |**30 seconden (standaard)**<br/><br/>**7 dagen (maximaal)** (U een berichtenlease verlengen of vrijgeven met behulp van de [UpdateMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage) API.) |**60 seconden (standaard)**<br/><br/>U een berichtvergrendeling vernieuwen met de [RenewLock-API.](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) |
| Lease/Lock precisie |**Berichtniveau**<br/><br/>(elk bericht kan een andere time-outwaarde hebben, die u vervolgens bijwerken als dat nodig is tijdens het verwerken van het bericht, met behulp van de [UpdateMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage) API) |**Wachtrijniveau**<br/><br/>(in elke wachtrij wordt een vergrendelingsprecisie toegepast op alle berichten, [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) maar u de vergrendeling vernieuwen met behulp van de RenewLock-API.) |
| Batched ontvangen |**Ja**<br/><br/>(expliciet het aantal berichten opgeven bij het ophalen van berichten, tot een maximum van 32 berichten) |**Ja**<br/><br/>(impliciet een pre-fetch eigenschap inschakelen of expliciet door het gebruik van transacties) |
| Batched send |**Nee** |**Ja**<br/><br/>(door het gebruik van transacties of client-side batching) |

### <a name="additional-information"></a>Aanvullende informatie
* Berichten in opslagwachtrijen zijn meestal first-in-first-out, maar soms kunnen ze niet in orde zijn; bijvoorbeeld wanneer de time-outduur van een bericht verloopt (bijvoorbeeld als gevolg van een clienttoepassing die crasht tijdens de verwerking). Wanneer de time-out van de zichtbaarheid verloopt, wordt het bericht weer zichtbaar in de wachtrij voor een andere werknemer om de wachtrij te ontzetten. Op dat moment kan het nieuw zichtbare bericht in de wachtrij worden geplaatst (om opnieuw in de wachtrij te worden geplaatst) na een bericht dat oorspronkelijk in de wachtrij stond.
* Het gegarandeerde FIFO-patroon in wachtrijen voor servicebussen vereist het gebruik van berichtensessies. In het geval dat de toepassing vastloopt tijdens het verwerken van een bericht dat is ontvangen in de modus **Peek & vergrendelen,** de volgende keer dat een wachtrijontvanger een berichtensessie accepteert, begint deze met het mislukte bericht nadat de time-to-live (TTL)-periode is verlopen.
* Opslagwachtrijen zijn ontworpen om standaardwachtrijscenario's te ondersteunen, zoals het ontkoppelen van toepassingscomponenten om de schaalbaarheid en tolerantie voor fouten, taaknivellering en het bouwen van procesworkflows te verhogen.
* Inconsistenties met betrekking tot de afhandeling van berichten in het kader van Service Bus-sessies kunnen worden vermeden door de sessiestatus te gebruiken om de status van de toepassing op te slaan ten opzichte van de voortgang van de afhandeling van de berichtreeks van de sessie, en door transacties te gebruiken rond het vereffenen van ontvangen berichten en het bijwerken van de sessiestatus. Dit soort consistentie functie wordt soms gelabeld *Exactly-Once Processing* in producten van andere leveranciers, maar transactie fouten zal uiteraard leiden tot berichten worden opnieuw geleverd en dus de term is niet precies voldoende.
* Opslagwachtrijen bieden een uniform en consistent programmeermodel voor wachtrijen, tabellen en BLOB's, zowel voor ontwikkelaars als voor operationele teams.
* Wachtrijen voor servicebussen bieden ondersteuning voor lokale transacties in de context van één wachtrij.
* De modus **Ontvangen en verwijderen** die wordt ondersteund door Service Bus biedt de mogelijkheid om het aantal berichtenbewerking (en de bijbehorende kosten) te verminderen in ruil voor een verlaagde leveringsgarantie.
* Opslagwachtrijen bieden leases met de mogelijkheid om de leases voor berichten uit te breiden. Hierdoor kunnen de werknemers korte leases op berichten houden. Als een werknemer vastloopt, kan het bericht dus snel opnieuw worden verwerkt door een andere werknemer. Bovendien kan een werknemer de lease op een bericht verlengen als deze langer moet worden verwerkt dan de huidige leasetijd.
* Opslagwachtrijen bieden een zichtbaarheidstime-out die u instellen bij het uitchecken of dequeuderen van een bericht. Bovendien u een bericht met verschillende leasewaarden bij verloop van tijd bijwerken en verschillende waarden bijwerken voor berichten in dezelfde wachtrij. Time-outs van de servicebusworden gedefinieerd in de metagegevens van de wachtrij. U het slot echter verlengen door de [renewlock-methode](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) aan te roepen.
* De maximale time-out voor een blokkeringsactie in wachtrijen voor servicebussen is 24 dagen. Time-outs op basis van rest hebben echter een maximale waarde van 55 seconden.
* Client-side batching die door Service Bus kan een wachtrij client om meerdere berichten batch in een enkele bewerking verzenden. Batching is alleen beschikbaar voor asynchrone verzendbewerkingen.
* Functies zoals het 200 TB-plafond van opslagwachtrijen (meer wanneer u accounts virtualiseert) en onbeperkte wachtrijen maken het een ideaal platform voor SaaS-providers.
* Opslagwachtrijen bieden een flexibel en performant gedelegeerd toegangscontrolemechanisme.

## <a name="advanced-capabilities"></a>Geavanceerde mogelijkheden
In deze sectie worden geavanceerde mogelijkheden van opslagwachtrijen en wachtrijen voor servicebussen vergeleken.

| Vergelijkingscriteria | Opslagwachtrijen | Service Bus-wachtrijen |
| --- | --- | --- |
| Geplande bezorging |**Ja** |**Ja** |
| Automatische dode letters |**Nee** |**Ja** |
| De time-to-live-waarde van de wachtrij verhogen |**Ja**<br/><br/>(via in-place update van zichtbaarheid time-out) |**Ja**<br/><br/>(geleverd via een speciale API-functie) |
| Ondersteuning voor gifberichten |**Ja** |**Ja** |
| In-place update |**Ja** |**Ja** |
| Transactielogboek aan de serverzijde |**Ja** |**Nee** |
| Opslagstatistieken |**Ja**<br/><br/>**Minute Metrics:** biedt realtime statistieken voor beschikbaarheid, TPS, API-oproeptellingen, fouttellingen en meer, allemaal in realtime (geaggregeerd per minuut en gerapporteerd binnen een paar minuten van wat er net in de productie is gebeurd. Zie [Over storageanalytics-statistieken](/rest/api/storageservices/fileservices/About-Storage-Analytics-Metrics)voor meer informatie . |**Ja**<br/><br/>(bulkquery's door [GetQueues te](/dotnet/api/microsoft.servicebus.namespacemanager.getqueues#Microsoft_ServiceBus_NamespaceManager_GetQueues)bellen) |
| Statusbeheer |**Nee** |**Ja**<br/><br/>[Microsoft.servicebus.messaging.entitystatus.Active](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.servicebus.messaging.entitystatus.disabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.servicebus.messaging.entitystatus.SendDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.servicebus.messaging.entitystatus.receiveDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus) |
| Automatisch doorsturen van berichten |**Nee** |**Ja** |
| Wachtrij, functie wissen |**Ja** |**Nee** |
| Berichtengroepen |**Nee** |**Ja**<br/><br/>(door het gebruik van berichtensessies) |
| Toepassingsstatus per berichtgroep |**Nee** |**Ja** |
| Detectie van duplicaten |**Nee** |**Ja**<br/><br/>(configureerbaar aan de afzenderkant) |
| Door berichtengroepen bladeren |**Nee** |**Ja** |
| Berichtensessies ophalen op id |**Nee** |**Ja** |

### <a name="additional-information"></a>Aanvullende informatie
* Beide wachtrijtechnologieën maken het mogelijk om een bericht op een later tijdstip te laten in- en oplevering.
* Met automatisch doorsturen in de wachtrij kunnen duizenden wachtrijen hun berichten automatisch doorsturen naar één wachtrij, van waaruit de ontvangende toepassing het bericht verbruikt. U dit mechanisme gebruiken om beveiliging te bereiken, de stroom te beheren en opslag tussen elke berichtuitgever te isoleren.
* Opslagwachtrijen bieden ondersteuning voor het bijwerken van berichtinhoud. U deze functionaliteit gebruiken voor aanhoudende statusinformatie en incrementele voortgangsupdates in het bericht, zodat het kan worden verwerkt vanaf het laatst bekende controlepunt, in plaats van helemaal opnieuw te beginnen. Met wachtrijen voor servicebus u hetzelfde scenario inschakelen door het gebruik van berichtsessies. Met sessies u de status van de verwerking van toepassingen opslaan en ophalen (met [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) en [GetState).](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState)
* [Dode letters](service-bus-dead-letter-queues.md), die alleen wordt ondersteund door wachtrijen van ServiceBus, kan handig zijn voor het isoleren van berichten die niet succesvol kunnen worden verwerkt door de ontvangende toepassing of wanneer berichten hun bestemming niet kunnen bereiken vanwege een verlopen time-to-live (TTL) eigenschap. De TTL-waarde geeft aan hoe lang een bericht in de wachtrij blijft staan. Met Service Bus wordt het bericht verplaatst naar een speciale wachtrij met de naam $DeadLetterQueue wanneer de TTL-periode verloopt.
* Als u 'gifberichten' wilt vinden in opslagwachtrijen, onderzoekt de toepassing bij het dequeuleren van een bericht de eigenschap [DequeueCount](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage.dequeuecount) van het bericht. Als **DequeueCount** groter is dan een bepaalde drempelwaarde, verplaatst de toepassing het bericht naar een door de toepassing gedefinieerde wachtrij voor 'dode letter'.
* Met opslagwachtrijen u een gedetailleerd logboek van alle transacties verkrijgen die zijn uitgevoerd in de wachtrij, evenals geaggregeerde statistieken. Beide opties zijn handig voor het opsporen en begrijpen hoe uw toepassing opslagwachtrijen gebruikt. Ze zijn ook handig voor het afstemmen van uw toepassing en het verlagen van de kosten van het gebruik van wachtrijen.
* Het concept van "message sessions" ondersteund door Service Bus maakt het mogelijk berichten die behoren tot een bepaalde logische groep te worden gekoppeld aan een bepaalde ontvanger, die op zijn beurt creëert een sessie-achtige affiniteit tussen berichten en hun respectieve ontvangers. U deze geavanceerde functionaliteit in Service Bus inschakelen door de eigenschap [SessionID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId) in te stellen op een bericht. Ontvangers kunnen vervolgens luisteren op een specifieke sessie-ID en berichten ontvangen die de opgegeven sessie-id delen.
* De duplicatiedetectiefunctionaliteit die wordt ondersteund door wachtrijen voor ServiceBus verwijdert automatisch dubbele berichten die naar een wachtrij of onderwerp worden verzonden, op basis van de waarde van de eigenschap [MessageId.](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId)

## <a name="capacity-and-quotas"></a>Capaciteit en contingenten
In deze sectie worden opslagwachtrijen en wachtrijen voor servicebussen vergeleken vanuit het perspectief van [capaciteit en quota](service-bus-quotas.md) die van toepassing kunnen zijn.

| Vergelijkingscriteria | Opslagwachtrijen | Service Bus-wachtrijen |
| --- | --- | --- |
| Maximale wachtrijgrootte |**500 TB**<br/><br/>(beperkt tot [één opslagcapaciteit)](../storage/common/storage-introduction.md#queue-storage) |**1 GB tot 80 GB**<br/><br/>(gedefinieerd bij het aanmaken van een wachtrij en [het inschakelen van partitionering](service-bus-partitioning.md) – zie de sectie 'Aanvullende informatie') |
| Maximale berichtgrootte |**64 kB**<br/><br/>(48 KB bij gebruik **van Base64** codering)<br/><br/>Azure ondersteunt grote berichten door wachtrijen en blobs te combineren, waarna u maximaal 200 GB inzetten voor één item. |**256 KB** of **1 MB**<br/><br/>(inclusief zowel kop- als hoofdtekst, maximale kopgrootte: 64 KB).<br/><br/>Afhankelijk van de [servicelaag.](service-bus-premium-messaging.md) |
| Maximaal bericht TTL |**Oneindig** (vanaf api-versie 2017-07-27) |**TimeSpan.Max** |
| Maximum aantal wachtrijen |**Onbeperkt** |**10.000**<br/><br/>(per servicenaamruimte) |
| Maximum aantal gelijktijdige clients |**Onbeperkt** |**Onbeperkt**<br/><br/>(100 gelijktijdige verbindingslimiet geldt alleen voor tcp-protocolgebaseerde communicatie) |

### <a name="additional-information"></a>Aanvullende informatie
* ServiceBus handhaaft limieten voor de grootte van de wachtrij. De maximale wachtrijgrootte wordt opgegeven bij het maken van de wachtrij en kan een waarde hebben tussen 1 en 80 GB. Als de waarde van de wachtrijgrootte bij het maken van de wachtrij is bereikt, worden extra binnenkomende berichten geweigerd en wordt een uitzondering ontvangen door de oproepcode. Zie [ServiceBusquota](service-bus-quotas.md)voor meer informatie over quota in servicebus.
* Partitionering wordt niet ondersteund in de [premiumlaag](service-bus-premium-messaging.md). In de standaardlaag u wachtrijen voor servicebussen maken in de grootte van 1, 2, 3, 4 of 5 GB (standaard is 1 GB). In de standaardlaag, waarbij partitionering is ingeschakeld (wat de standaardinstelling is), maakt Service Bus 16 partities voor elke GB die u opgeeft. Als u dus een wachtrij maakt met een grootte van 5 GB, met 16 partities wordt de maximale wachtrijgrootte (5 * 16) = 80 GB. U de maximale grootte van uw partitiewachtrij of onderwerp zien door te kijken naar de vermelding ervan op de [Azure-portal.][Azure portal]
* Met Opslagrijen, als de inhoud van het bericht niet XML-veilig is, dan moet het **Base64** gecodeerd zijn. Als u **Base64**-codeer het bericht, kan de gebruiker payload tot 48 KB, in plaats van 64 KB.
* Met wachtrijen voor servicebus bestaat elk bericht dat in een wachtrij is opgeslagen uit twee delen: een koptekst en een hoofdtekst. De totale grootte van het bericht mag niet hoger zijn dan de maximale berichtgrootte die wordt ondersteund door de servicelaag.
* Wanneer clients communiceren met servicebuswachtrijen via het TCP-protocol, is het maximum aantal gelijktijdige verbindingen met één servicebuswachtrij beperkt tot 100. Dit nummer wordt gedeeld tussen afzenders en ontvangers. Als dit quotum is bereikt, worden volgende aanvragen voor aanvullende verbindingen afgewezen en wordt een uitzondering ontvangen door de belcode. Deze limiet wordt niet opgelegd aan clients die verbinding maken met de wachtrijen met behulp van REST-gebaseerde API.
* Als u meer dan 10.000 wachtrijen in één naamruimte van servicebus nodig hebt, u contact opnemen met het Azure-ondersteuningsteam en een verhoging aanvragen. Als u verder wilt schalen dan 10.000 wachtrijen met Service Bus, u ook extra naamruimten maken met behulp van de [Azure-portal.][Azure portal]

## <a name="management-and-operations"></a>Beheer en bedrijfsvoering
In deze sectie worden de beheerfuncties van opslagwachtrijen en wachtrijen voor servicebussen vergeleken.

| Vergelijkingscriteria | Opslagwachtrijen | Service Bus-wachtrijen |
| --- | --- | --- |
| Beheerprotocol |**REST over HTTP/HTTPS** |**REST over HTTPS** |
| Runtime-protocol |**REST over HTTP/HTTPS** |**REST over HTTPS**<br/><br/>**AMQP 1.0-standaard (TCP met TLS)** |
| .NET API |**Ja**<br/><br/>(.NET Storage Client API) |**Ja**<br/><br/>(.NET Service Bus API) |
| Native C++ |**Ja** |**Ja** |
| Java-API |**Ja** |**Ja** |
| PHP-API |**Ja** |**Ja** |
| Node.js API |**Ja** |**Ja** |
| Ondersteuning voor willekeurige metagegevens |**Ja** |**Nee** |
| Wachtrijnaamgevingsregels |**Maximaal 63 tekens lang**<br/><br/>(Letters in een wachtrijnaam moeten kleine letters zijn.) |**Maximaal 260 tekens lang**<br/><br/>(Wachtrijpaden en namen zijn hoofdletters ongevoelig.) |
| De functie Wachtrijlengte oppakken |**Ja**<br/><br/>(Geschatte waarde als berichten verlopen buiten de TTL zonder te worden verwijderd.) |**Ja**<br/><br/>(Exact, point-in-time waarde.) |
| Peek, functie |**Ja** |**Ja** |

### <a name="additional-information"></a>Aanvullende informatie
* Opslagwachtrijen bieden ondersteuning voor willekeurige kenmerken die kunnen worden toegepast op de wachtrijbeschrijving, in de vorm van naam/waardeparen.
* Beide wachtrijtechnologieën bieden de mogelijkheid om een bericht te bekijken zonder het te hoeven vergrendelen, wat handig kan zijn bij het implementeren van een wachtrijverkenner/browsertool.
* De Service Bus .NET brokered messaging API's maken gebruik van full-duplex TCP-verbindingen voor betere prestaties in vergelijking met REST over HTTP, en ze ondersteunen het AMQP 1.0-standaardprotocol.
* Namen van opslagwachtrijen kunnen 3-63 tekens lang zijn, kunnen kleine letters, cijfers en koppeltekens bevatten. Zie [Wachtrijen en metagegevens voor](/rest/api/storageservices/fileservices/Naming-Queues-and-Metadata)meer informatie .
* Wachtrijnamen voor servicebus kunnen maximaal 260 tekens lang zijn en hebben minder beperkende naamgevingsregels. Wachtrijnamen voor servicebus kunnen letters, cijfers, perioden, koppeltekens en underscores bevatten.

## <a name="authentication-and-authorization"></a>Verificatie en autorisatie
In dit gedeelte worden de verificatie- en autorisatiefuncties besproken die worden ondersteund door opslagwachtrijen en wachtrijen voor servicebussen.

| Vergelijkingscriteria | Opslagwachtrijen | Service Bus-wachtrijen |
| --- | --- | --- |
| Verificatie |**Symmetrische sleutel** |**Symmetrische sleutel** |
| Beveiligingsmodel |Gedelegeerde toegang via SAS-tokens. |Sas |
| Federatie van identiteitsprovideren |**Nee** |**Ja** |

### <a name="additional-information"></a>Aanvullende informatie
* Elk verzoek aan een van de wachtrijtechnologieën moet worden geverifieerd. Openbare wachtrijen met anonieme toegang worden niet ondersteund. Met [SAS](service-bus-sas.md)u dit scenario aanpakken door een alleen-schrijven SAS, alleen-lezen SAS of zelfs een SAS met volledige toegang te publiceren.
* Het verificatieschema dat door opslagwachtrijen wordt geboden, omvat het gebruik van een symmetrische sleutel, een op hash gebaseerde Message Authentication Code (HMAC), berekend met het SHA-256-algoritme en gecodeerd als een **Base64-tekenreeks.** Zie [Verificatie voor de Azure Storage Services voor](/rest/api/storageservices/fileservices/Authentication-for-the-Azure-Storage-Services)meer informatie over het desbetreffende protocol. Servicebuswachtrijen ondersteunen een vergelijkbaar model met symmetrische toetsen. Zie [Verificatie van gedeelde toegangshandtekeningen met servicebus](service-bus-sas.md)voor meer informatie .

## <a name="conclusion"></a>Conclusie
Door het verkrijgen van een dieper begrip van de twee technologieën, zult u in staat zijn om een meer geïnformeerde beslissing te nemen over welke wachtrij technologie te gebruiken, en wanneer. De beslissing over het gebruik van opslagwachtrijen of wachtrijen voor servicebussen is duidelijk afhankelijk van een aantal factoren. Deze factoren kunnen sterk afhangen van de individuele behoeften van uw toepassing en de architectuur ervan. Als uw toepassing al gebruik maakt van de kernmogelijkheden van Microsoft Azure, kiest u mogelijk opslagwachtrijen, vooral als u basiscommunicatie en berichten tussen services nodig hebt of wachtrijen nodig hebt die groter kunnen zijn dan 80 GB.

Omdat wachtrijen voor servicebus een aantal geavanceerde functies bieden, zoals sessies, transacties, dubbele detectie, automatische dead-lettering en duurzame mogelijkheden voor publiceren/abonneren, kunnen ze een voorkeurskeuze zijn als u een hybride toepassing bouwt of als uw toepassing deze functies anderszins vereist.

## <a name="next-steps"></a>Volgende stappen
De volgende artikelen bieden meer richtlijnen en informatie over het gebruik van opslagwachtrijen of wachtrijen voor servicebussen.

* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [De wachtrijopslagservice gebruiken](../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Aanbevolen procedures voor prestatieverbeteringen met servicebus-berichten met een broker](service-bus-performance-improvements.md)
* [Wachtrijen en onderwerpen introduceren in Azure Service Bus (blogbericht)](https://www.serverless360.com/blog/azure-service-bus-queues-vs-topics)
* [De handleiding voor de service van de ontwikkelaar](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
* [De Queuing-service gebruiken in Azure](https://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)

[Azure portal]: https://portal.azure.com

