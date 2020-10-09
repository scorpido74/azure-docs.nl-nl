---
title: Azure Storage- en Service Bus-wachtrijen vergelijken
description: Analyseer verschillen en overeenkomsten tussen twee typen wacht rijen die door Azure worden aangeboden.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: a64000741de68518dd459b105a093ccf4cb6ab7b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85337650"
---
# <a name="storage-queues-and-service-bus-queues---compared-and-contrasted"></a>Storage Queues and Service Bus Queues - Compared and Contrasted (Storage-wachtrijen en Service Bus-wachtrijen: overeenkomsten en verschillen)
In dit artikel worden de verschillen en overeenkomsten geanalyseerd tussen de twee typen wacht rijen die momenteel worden aangeboden door Microsoft Azure: opslag wachtrijen en Service Bus wacht rijen. Met behulp van deze informatie kunt u de respectieve technologieën vergelijken en er contrast van ondervinden, en kunt u beter gefundeerde beslissingen nemen over welke oplossing het beste aan uw behoeften voldoet.

## <a name="introduction"></a>Inleiding
Azure ondersteunt twee typen wachtrij mechanismen: **opslag wachtrijen** en **wacht rijen voor service bus**.

**Opslag wachtrijen**, die deel uitmaken van de [Azure-opslag](https://azure.microsoft.com/services/storage/) infrastructuur, bieden een eenvoudige, op rest gebaseerde interface voor get/put/Peek, die betrouw bare, permanente berichten binnen en tussen services biedt.

**Service bus-wacht rijen** maken deel uit van een ruimere [Azure Messa ging](https://azure.microsoft.com/services/service-bus/) -infra structuur die ondersteuning biedt voor zowel Queuing als publiceren/abonneren en geavanceerde integratie patronen. Zie [overzicht van service bus](service-bus-messaging-overview.md)voor meer informatie over service bus wacht rijen/onderwerpen/abonnementen.

Hoewel beide Queuing-technologieën gelijktijdig bestaan, werden er eerst opslag wachtrijen geïntroduceerd, als een toegewezen wachtrij opslag mechanisme op basis van Azure Storage services. Service Bus-wacht rijen zijn gebouwd op basis van de ruimere berichten infrastructuur die is ontworpen om toepassingen of toepassings onderdelen te integreren die meerdere communicatie protocollen, gegevens contracten, vertrouwens domeinen en/of netwerk omgevingen kunnen omvatten.

## <a name="technology-selection-considerations"></a>Overwegingen voor technologie selectie
Zowel opslag wachtrijen als Service Bus wacht rijen zijn implementaties van de Message Queuing-service die momenteel wordt aangeboden door Microsoft Azure. Elk heeft een iets andere set met functies, wat betekent dat u er een of een kunt kiezen, of beide, afhankelijk van de behoeften van uw specifieke oplossing of zakelijk of technisch probleem dat u wilt oplossen.

Bij het bepalen van welke Queuing-technologie het doel is voor een bepaalde oplossing, moeten oplossings architecten en ontwikkel aars deze aanbevelingen overwegen. Zie de volgende sectie voor meer informatie.

Als oplossings architect/ontwikkelaar **kunt u overwegen om opslag wachtrijen te gebruiken** wanneer:

* Uw toepassing moet meer dan 80 GB aan berichten in een wachtrij opslaan.
* Uw toepassing wil de voortgang bijhouden voor het verwerken van een bericht in de wachtrij. Dit is handig als de werk nemer een bericht vastloopt. Een volgende werk nemer kan die informatie vervolgens gebruiken om door te gaan vanaf het punt waar de vorige werk nemer was gebleven.
* U moet logboeken aan de server zijde hebben van alle trans acties die worden uitgevoerd op uw wacht rijen.

Als oplossings architect/ontwikkelaar **kunt u overwegen service bus-wacht rijen te gebruiken** wanneer:

* Uw oplossing moet berichten kunnen ontvangen zonder dat de wachtrij hoeft te worden gecontroleerd. Met Service Bus kan dit worden bereikt met de lange polling-ontvangst bewerking met behulp van de op TCP gebaseerde protocollen die door Service Bus worden ondersteund.
* Voor uw oplossing moet de wachtrij een gegarandeerde, First-in-first-out (FIFO) bestelde levering bieden.
* Uw oplossing moet ondersteuning bieden voor automatische duplicaten detectie.
* U wilt dat uw toepassing berichten verwerkt als parallelle, langlopende streams (berichten worden gekoppeld aan een stroom met behulp van de eigenschap [SessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid) in het bericht). In dit model maakt elk knoop punt in de verbruiks toepassing concurrentie voor streams, in tegens telling tot berichten. Wanneer een stroom aan een verbruikt knoop punt wordt gegeven, kan het knoop punt de status van de status van de toepassings stroom controleren met behulp van trans acties.
* Uw oplossing vereist transactie gedrag en atomiciteit bij het verzenden of ontvangen van meerdere berichten vanuit een wachtrij.
* Uw toepassing verwerkt berichten die 64 KB kunnen overschrijden, maar niet waarschijnlijk de limiet van 256 KB.
* U hebt een vereiste voor het leveren van een op rollen gebaseerd toegangs model voor de wacht rijen en andere rechten/machtigingen voor afzenders en ontvangers. Raadpleeg voor meer informatie de volgende artikelen:
    - [Verifiëren met beheerde identiteiten](service-bus-managed-service-identity.md)
    - [Verifiëren vanuit een toepassing](authenticate-application.md)
* Uw wachtrijgrootte niet groter wordt dan 80 GB.
* U wilt het AMQP 1,0-standaard berichten protocol gebruiken. Zie [Service Bus overzicht van AMQP](service-bus-amqp-overview.md)voor meer informatie over AMQP.
* U kunt een uiteindelijke migratie van punt-naar-punt-communicatie op basis van een wachtrij voor een uitwisselings patroon van berichten voor een naadloze integratie van extra ontvangers (abonnees), die elk een onafhankelijke kopie van een of alle berichten ontvangen die naar de wachtrij worden verzonden. De laatste verwijst naar de functie voor publiceren/abonneren, die standaard wordt verschaft door Service Bus.
* Uw Messa ging-oplossing moet ondersteuning bieden voor de ' at-ly-Once '-leverings garantie zonder dat u de extra infrastructuur onderdelen hoeft te bouwen.
* U graag de mogelijkheid wilt hebben om batches van berichten te publiceren en gebruiken.

## <a name="comparing-storage-queues-and-service-bus-queues"></a>Opslag wachtrijen en wacht rijen voor Service Bus vergelijken
De tabellen in de volgende secties bieden een logische groepering van wachtrij functies en u kunt in één oogopslag de beschik bare mogelijkheden in zowel Azure Storage wachtrijen als Service Bus wacht rijen vergelijken.

## <a name="foundational-capabilities"></a>Basis mogelijkheden
In deze sectie worden enkele van de fundamentele wachtrij mogelijkheden van opslag wachtrijen en Service Bus-wacht rijen vergeleken.

| Vergelijkings criteria | Opslagwachtrijen | Service Bus-wachtrijen |
| --- | --- | --- |
| Garantie best Ellen |**Nee** <br/><br>Zie de eerste opmerking in de sectie ' aanvullende informatie ' voor meer informatie.</br> |**Ja, eerst-in-first-out (FIFO)**<br/><br>(door het gebruik van Messa ging-sessies) |
| Leverings garantie |**Mini maal één keer** |**Mini maal één keer** (met behulp van de PeekLock-ontvangst modus-dit is de standaard instelling) <br/><br/>**Mini maal één keer** (met behulp van de ReceiveAndDelete-ontvangst modus) <br/> <br/> Meer informatie over verschillende [ontvangst modi](service-bus-queues-topics-subscriptions.md#receive-modes)  |
| Ondersteuning voor atomische bewerkingen |**Nee** |**Ja**<br/><br/> |
| Gedrag ontvangen |**Niet blok keren**<br/><br/>(wordt direct uitgevoerd als er geen nieuw bericht wordt gevonden) |**Blok keren met/zonder time-out**<br/><br/>(biedt lange polling of de [' Comet-techniek '](https://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>**Niet blok keren**<br/><br/>(alleen met behulp van .NET Managed API) |
| Push-stijl-API |**Nee** |**Ja**<br/><br/>[QueueClient. OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage#Microsoft_ServiceBus_Messaging_QueueClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__) en [MessageSessionHandler. ONMESSAGE](/dotnet/api/microsoft.servicebus.messaging.messagesessionhandler.onmessage#Microsoft_ServiceBus_Messaging_MessageSessionHandler_OnMessage_Microsoft_ServiceBus_Messaging_MessageSession_Microsoft_ServiceBus_Messaging_BrokeredMessage__) sessies .net API. |
| Ontvangst modus |**& lease bekijken** |**& vergrendeling bekijken**<br/><br/>**Ontvangen & verwijderen** |
| Modus voor exclusieve toegang |**Op basis van lease** |**Op basis van vergren deling** |
| Duur van de lease en vergren deling |**30 seconden (standaard)**<br/><br/>**7 dagen (Maxi maal)** (u kunt een bericht lease vernieuwen of vrijgeven met behulp van de [UpdateMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage) -API.) |**60 seconden (standaard)**<br/><br/>U kunt een bericht vergrendeling vernieuwen met behulp van de [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) -API. |
| Precisie van lease/vergren deling |**Bericht niveau**<br/><br/>(elk bericht kan een andere time-outwaarde hebben, die vervolgens naar behoefte kan worden bijgewerkt tijdens het verwerken van het bericht met behulp van de [UpdateMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage) -API) |**Wachtrij niveau**<br/><br/>(elke wachtrij heeft een vergrendelings precisie die is toegepast op alle berichten, maar u kunt de vergren deling vernieuwen met behulp van de [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) -API.) |
| Batch ontvangen |**Ja**<br/><br/>(expliciet opgeven van het aantal berichten bij het ophalen van berichten, Maxi maal 32 berichten) |**Ja**<br/><br/>(impliciet het inschakelen van een eigenschap vooraf ophalen of expliciet door het gebruik van trans acties) |
| Batch-verzen ding |**Nee** |**Ja**<br/><br/>(door het gebruik van trans acties of batch verwerking aan client zijde) |

### <a name="additional-information"></a>Aanvullende informatie
* Berichten in opslag wachtrijen zijn doorgaans eerst in eerste instantie, maar soms kunnen ze in de juiste volg orde staan. Wanneer de time-out voor de zicht baarheid van een bericht bijvoorbeeld verloopt (bijvoorbeeld als gevolg van een crash van een client toepassing tijdens de verwerking). Wanneer de time-out voor de zicht baarheid is verlopen, wordt het bericht weer zichtbaar in de wachtrij voor een andere werk nemer om het uit te stellen. Op dat moment kan het pas zicht bare bericht in de wachtrij worden geplaatst (om opnieuw in de wachtrij te worden gezet) na een bericht dat oorspronkelijk is ingecheckt.
* Voor het gegarandeerde FIFO-patroon in Service Bus wachtrijen is het gebruik van Messa ging-sessies vereist. In het geval dat de toepassing vastloopt tijdens het verwerken van een bericht dat in de modus **bekijken & Lock** is ontvangen, wordt de volgende keer dat een wachtrij ontvanger een berichten sessie accepteert, gestart met het mislukte bericht nadat de TTL-periode (time-to-Live) is verlopen.
* Opslag wachtrijen zijn ontworpen ter ondersteuning van standaard-Queuing-scenario's, zoals het loskoppelen van toepassings onderdelen om de schaal baarheid en tolerantie voor fouten te verg Roten, het verdelen van taken en het bouwen van proces werk stromen.
* Inconsistenties met betrekking tot de verwerking van berichten in de context van Service Bus sessies kunnen worden vermeden door sessie status te gebruiken om de status van de toepassing op te slaan ten opzichte van de voortgang van het verwerken van de bericht reeks van de sessie en door trans acties te gebruiken om ontvangen berichten te vereffenen en de sessie status bij te werken. Dit type consistentie-functie wordt soms aangeduid met *precies één keer per verwerking* in de producten van andere leveranciers, maar door transactie fouten zullen er uiteraard berichten opnieuw worden bezorgd, waardoor de term niet precies voldoende is.
* Opslag wachtrijen bieden een uniform en consistent programmeer model voor wacht rijen, tabellen en BLOBs, zowel voor ontwikkel aars als voor Operations-teams.
* Service Bus-wacht rijen bieden ondersteuning voor lokale trans acties in de context van één wachtrij.
* De modus **ontvangen en verwijderen** die door service bus wordt ondersteund, biedt de mogelijkheid om het aantal Messa ging-bewerkingen (en de bijbehorende kosten) in Exchange te verminderen voor verlaagde leverings garantie.
* Opslag wachtrijen bieden leases met de mogelijkheid om de leases voor berichten uit te breiden. Hierdoor kunnen de werk nemers korte leases op berichten onderhouden. Als een werk nemer vastloopt, kan het bericht dus snel opnieuw worden verwerkt door een andere werk nemer. Daarnaast kan een werk nemer de lease uitbreiden in een bericht als deze langer moet worden verwerkt dan de huidige lease tijd.
* Opslag wachtrijen bieden een time-out voor zicht baarheid die u kunt instellen op de enqueuing of de dequeuing van een bericht. Daarnaast kunt u tijdens runtime een bericht bijwerken met verschillende lease waarden en verschillende waarden voor alle berichten in dezelfde wachtrij bijwerken. Time-outService Busen voor de vergren deling worden gedefinieerd in de meta gegevens van de wachtrij. u kunt de vergren deling echter verlengen door de methode [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) aan te roepen.
* De maximale time-out voor een blokkerende ontvangst bewerking in Service Bus wacht rijen is 24 dagen. Op REST gebaseerde time-outs hebben echter een maximum waarde van 55 seconden.
* Door de batch verwerking aan de client zijde die door Service Bus wordt gegeven, kan een wachtrij client meerdere berichten batchgewijs in één verzend bewerking uitvoeren. Batch verwerking is alleen beschikbaar voor asynchrone verzend bewerkingen.
* Functies zoals het maximum van 200 TB aan opslag wachtrijen (meer bij het virtualiseren van accounts) en onbeperkte wacht rijen maken een ideaal platform voor SaaS-providers.
* Opslag wachtrijen bieden een flexibel en uitvoeren van een gegemachtigde toegangs beheer mechanisme.

## <a name="advanced-capabilities"></a>Geavanceerde mogelijkheden
In deze sectie worden de geavanceerde mogelijkheden van opslag wachtrijen en Service Bus wachtrijen vergeleken.

| Vergelijkings criteria | Opslagwachtrijen | Service Bus-wachtrijen |
| --- | --- | --- |
| Geplande bezorging |**Ja** |**Ja** |
| Automatische onbestelbare berichten |**Nee** |**Ja** |
| Time-to-Live-waarde van wachtrij verhogen |**Ja**<br/><br/>(via in-place update van time-out voor zicht baarheid) |**Ja**<br/><br/>(door gegeven via een speciale API-functie) |
| Ondersteuning voor verontreinigd bericht |**Ja** |**Ja** |
| In-place update |**Ja** |**Ja** |
| Transactie logboek aan de server zijde |**Ja** |**Nee** |
| Metrische opslag gegevens |**Ja**<br/><br/>**Minuut metrieken**: voorziet in realtime metrische gegevens voor beschik BAARHEID, TPS, API-oproep aantallen, fout aantallen en meer, in realtime (geaggregeerd per minuut en gerapporteerd binnen een paar minuten vanaf wat zojuist in de productie is opgetreden. Zie [informatie over Opslaganalyse metrische](/rest/api/storageservices/fileservices/About-Storage-Analytics-Metrics)gegevens voor meer informatie. |**Ja**<br/><br/>(bulk query's door het aanroepen van [GetQueues](/dotnet/api/microsoft.servicebus.namespacemanager.getqueues#Microsoft_ServiceBus_NamespaceManager_GetQueues)) |
| Statusbeheer |**Nee** |**Ja**<br/><br/>[Micro soft. ServiceBus. Messa ging. EntityStatus. Active](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [micro soft. ServiceBus. Messa ging. EntityStatus. disabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [micro soft. ServiceBus. Messa ging. EntityStatus. SendDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [micro soft. ServiceBus. Messa ging. EntityStatus. ReceiveDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus) |
| Bericht automatisch door sturen |**Nee** |**Ja** |
| Wachtrij functie leegmaken |**Ja** |**Nee** |
| Bericht groepen |**Nee** |**Ja**<br/><br/>(door het gebruik van Messa ging-sessies) |
| Toepassings status per bericht groep |**Nee** |**Ja** |
| Detectie van duplicaten |**Nee** |**Ja**<br/><br/>(configureerbaar aan de zijde van de afzender) |
| Bericht groepen door bladeren |**Nee** |**Ja** |
| Bericht sessies op ID ophalen |**Nee** |**Ja** |

### <a name="additional-information"></a>Aanvullende informatie
* Beide Queuing-technologieën zorgen ervoor dat een bericht op een later tijdstip kan worden gepland.
* Door de wachtrij automatisch door te sturen, kunnen duizenden wacht rijen hun berichten automatisch door sturen naar een enkele wachtrij, waaruit de ontvangende toepassing het bericht verbruikt. U kunt dit mechanisme gebruiken voor de beveiliging, controle stroom en het isoleren van opslag tussen elke uitgever van berichten.
* Opslag wachtrijen bieden ondersteuning voor het bijwerken van bericht inhoud. U kunt deze functie gebruiken voor het persistent maken van status informatie en incrementele voortgangs updates in het bericht, zodat deze kunnen worden verwerkt vanaf het laatste bekende controle punt, in plaats van helemaal vanaf het begin te beginnen. Met Service Bus wachtrijen kunt u hetzelfde scenario inschakelen via het gebruik van bericht sessies. Met sessies kunt u de verwerkings status van de toepassing opslaan en ophalen (met behulp van [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) en [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState)).
* [Onbestelbare](service-bus-dead-letter-queues.md)berichten, die alleen door service bus wacht rijen worden ondersteund, kunnen nuttig zijn voor het isoleren van een bericht dat niet kan worden verwerkt door de ontvangende toepassing of wanneer berichten hun bestemming niet kunnen bereiken vanwege een verlopen time-to-Live (TTL)-eigenschap. De TTL-waarde geeft aan hoe lang een bericht in de wachtrij blijft. Met Service Bus wordt het bericht verplaatst naar een speciale wachtrij met de naam $DeadLetterQueue wanneer de TTL-periode verloopt.
* Als u ' Poison ' berichten in de opslag wachtrijen wilt vinden, controleert de toepassing de eigenschap [DequeueCount](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage.dequeuecount) van het bericht wanneer een bericht wordt overgezet. Als **DequeueCount** groter is dan een bepaalde drempel waarde, verplaatst de toepassing het bericht naar een door de toepassing gedefinieerde wachtrij voor onbestelbare berichten.
* Met opslag wachtrijen kunt u een gedetailleerd logboek ophalen van alle trans acties die worden uitgevoerd op de wachtrij, evenals geaggregeerde metrische gegevens. Beide opties zijn handig voor het opsporen van fouten en het gebruik van de opslag wachtrijen in uw toepassing. Ze zijn ook handig voor het afstemmen van de prestaties van uw toepassing en het verminderen van de kosten voor het gebruik van wacht rijen.
* Met het concept van ' bericht sessies ' die door Service Bus worden ondersteund, kunnen berichten die deel uitmaken van een bepaalde logische groep worden gekoppeld aan een bepaalde ontvanger, die op zijn beurt een sessie lijkt te maken op de affiniteit tussen berichten en hun respectieve ontvangers. U kunt deze geavanceerde functionaliteit in Service Bus inschakelen door de eigenschap [SessionID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId) van een bericht in te stellen. Ontvangers kunnen vervolgens Luis teren naar een specifieke sessie-ID en berichten ontvangen die de opgegeven sessie-id delen.
* De duplicatie detectie functionaliteit die wordt ondersteund door Service Bus wachtrijen, verwijdert automatisch dubbele berichten die worden verzonden naar een wachtrij of onderwerp, op basis van de waarde van de eigenschap [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) .

## <a name="capacity-and-quotas"></a>Capaciteit en quota's
In deze sectie worden opslag wachtrijen en Service Bus wachtrijen vergeleken met het perspectief van de [capaciteit en quota's](service-bus-quotas.md) die van toepassing kunnen zijn.

| Vergelijkings criteria | Opslagwachtrijen | Service Bus-wachtrijen |
| --- | --- | --- |
| Maximale wachtrij grootte |**500 TB**<br/><br/>(beperkt tot een [capaciteit van één opslag account](../storage/common/storage-introduction.md#queue-storage)) |**1 GB tot 80 GB**<br/><br/>(gedefinieerd bij het maken van een wachtrij en het [inschakelen van partitionering](service-bus-partitioning.md) – Zie de sectie ' aanvullende informatie ') |
| Maximale bericht grootte |**64 kB**<br/><br/>(48 KB wanneer **Base64** -code ring wordt gebruikt)<br/><br/>Azure ondersteunt grote berichten door wacht rijen en blobs te combi neren. op die wijze kunt u Maxi maal 200 GB voor één item in de wachtrij plaatsen. |**256 KB** of **1 MB**<br/><br/>(inclusief header en hoofd tekst, maximale header grootte: 64 KB).<br/><br/>Is afhankelijk [van de servicelaag](service-bus-premium-messaging.md). |
| Maximale bericht-TTL |**Oneindig** (vanaf API-versie 2017-07-27) |**Time span. Max** |
| Maximum aantal wacht rijen |**Onbeperkt** |**10.000**<br/><br/>(per service naam ruimte) |
| Maximum aantal gelijktijdige clients |**Onbeperkt** |**Onbeperkt**<br/><br/>(100 gelijktijdige verbindings limiet geldt alleen voor communicatie op basis van TCP-protocol) |

### <a name="additional-information"></a>Aanvullende informatie
* Service Bus dwingt de limiet voor de wachtrij grootte af. De maximale wachtrij grootte is opgegeven bij het maken van de wachtrij en kan een waarde hebben tussen 1 en 80 GB. Als de waarde voor de wachtrij grootte die is ingesteld voor het maken van de wachtrij is bereikt, worden extra inkomende berichten geweigerd en wordt er een uitzonde ring ontvangen door de aanroepende code. Zie [Service Bus quota's](service-bus-quotas.md)voor meer informatie over quota's in service bus.
* Partitioneren wordt niet ondersteund in de [Premium-laag](service-bus-premium-messaging.md). In de laag standaard kunt u Service Bus-wacht rijen maken met een grootte van 1, 2, 3, 4 of 5 GB (de standaard waarde is 1 GB). In de laag standaard, waarbij partitioneren is ingeschakeld (de standaard instelling), maakt Service Bus 16 partities voor elke GB die u opgeeft. Als u een wachtrij maakt met een grootte van 5 GB, met 16 partities wordt de maximale wachtrij grootte (5 * 16) = 80 GB. U kunt de maximale grootte van de gepartitioneerde wachtrij of het onderwerp zien door de vermelding te bekijken op de [Azure Portal][Azure portal].
* Als de inhoud van het bericht niet XML-veilig is, moet er met opslag wachtrijen worden **gedecodeerd** . Als u het bericht met **Base64**versleutelt, kan de nettolading van de gebruiker maxi maal 48 KB zijn, in plaats van 64 kB.
* Met Service Bus wachtrijen bestaan elk bericht dat in een wachtrij is opgeslagen uit twee delen: een koptekst en een hoofd tekst. De totale grootte van het bericht kan niet groter zijn dan de maximale bericht grootte die wordt ondersteund door de servicelaag.
* Wanneer clients communiceren met Service Bus wachtrijen via het TCP-protocol, is het maximum aantal gelijktijdige verbindingen met één Service Bus wachtrij beperkt tot 100. Dit aantal wordt gedeeld tussen afzenders en ontvangers. Als dit quotum wordt bereikt, worden volgende aanvragen voor extra verbindingen afgewezen en wordt er een uitzonde ring ontvangen door de aanroepende code. Deze limiet wordt niet toegepast op clients die verbinding maken met de wacht rijen met behulp van op REST gebaseerde API.
* Als u meer dan 10.000 wacht rijen in één Service Bus naam ruimte nodig hebt, kunt u contact opnemen met het ondersteunings team van Azure en een toename aanvragen. Als u meer dan 10.000 wacht rijen met Service Bus wilt schalen, kunt u ook extra naam ruimten maken met behulp van de [Azure Portal][Azure portal].

## <a name="management-and-operations"></a>Beheer en bewerkingen
In deze sectie worden de beheer functies van opslag wachtrijen en Service Bus wachtrijen vergeleken.

| Vergelijkings criteria | Opslagwachtrijen | Service Bus-wachtrijen |
| --- | --- | --- |
| Beheer Protocol |**REST over HTTP/HTTPS** |**REST over HTTPS** |
| Runtime-Protocol |**REST over HTTP/HTTPS** |**REST over HTTPS**<br/><br/>**AMQP 1,0 Standard (TCP met TLS)** |
| .NET API |**Ja**<br/><br/>(API voor .NET-opslag-client) |**Ja**<br/><br/>(.NET Service Bus-API) |
| Systeemeigen C++ |**Ja** |**Ja** |
| Java-API |**Ja** |**Ja** |
| PHP-API |**Ja** |**Ja** |
| Node.js-API |**Ja** |**Ja** |
| Ondersteuning voor wille keurige meta gegevens |**Ja** |**Nee** |
| Naamgevings regels voor Queue |**Maxi maal 63 tekens lang**<br/><br/>(De naam van een wachtrij moet kleine letters zijn.) |**Maxi maal 260 tekens lang**<br/><br/>(Wachtrij paden en-namen zijn niet hoofdletter gevoelig.) |
| Functie wachtrij lengte ophalen |**Ja**<br/><br/>(Geschatte waarde als berichten buiten de TTL verlopen zonder dat ze worden verwijderd.) |**Ja**<br/><br/>(Exacte waarde, punt in tijd) |
| Functie Peek |**Ja** |**Ja** |

### <a name="additional-information"></a>Aanvullende informatie
* Opslag wachtrijen bieden ondersteuning voor wille keurige kenmerken die kunnen worden toegepast op de wachtrij beschrijving, in de vorm van naam/waarde-paren.
* Beide Queue-technologieën bieden de mogelijkheid om een bericht te bekijken zonder dat het hoeft te worden vergrendeld. Dit kan nuttig zijn bij het implementeren van een wachtrij Verkenner/browser-hulp programma.
* De Service Bus .NET Brokered Messaging-Api's maken gebruik van Full-duplex TCP-verbindingen voor betere prestaties in vergelijking met REST over HTTP en ondersteunen het AMQP 1,0-standaard protocol.
* Namen van opslag wachtrijen kunnen 3-63 tekens lang zijn en mogen kleine letters, cijfers en afbreek streepjes bevatten. Zie de [naamgeving van wacht rijen en meta gegevens](/rest/api/storageservices/fileservices/Naming-Queues-and-Metadata)voor meer informatie.
* Service Bus wachtrij namen mogen Maxi maal 260 tekens lang zijn en minder beperkende naamgevings regels hebben. Service Bus wachtrij namen mogen letters, cijfers, punten, afbreek streepjes en onderstrepings tekens bevatten.

## <a name="authentication-and-authorization"></a>Verificatie en autorisatie
In deze sectie worden de verificatie-en autorisatie functies beschreven die worden ondersteund door opslag wachtrijen en Service Bus wachtrijen.

| Vergelijkings criteria | Opslagwachtrijen | Service Bus-wachtrijen |
| --- | --- | --- |
| Verificatie |**Symmetrische sleutel** |**Symmetrische sleutel** |
| Beveiligingsmodel |Gedelegeerde toegang via SAS-tokens. |SAS |
| Federatie van ID-provider |**Nee** |**Ja** |

### <a name="additional-information"></a>Aanvullende informatie
* Elke aanvraag aan een van de Queuing-technologieën moet worden geverifieerd. Open bare wacht rijen met anonieme toegang worden niet ondersteund. Met [SAS](service-bus-sas.md)kunt u dit scenario aanpakken door een alleen-schrijven SAS, alleen-lezen sa's of zelfs een SAS met volledige toegang te publiceren.
* Het verificatie schema dat wordt verschaft door opslag wachtrijen, omvat het gebruik van een symmetrische sleutel, een op hash gebaseerde Message Authentication Code (HMAC), berekend met het SHA-256-algoritme en gecodeerd als **Base64** -teken reeks. Zie [verificatie voor de Azure Storage services](/rest/api/storageservices/fileservices/Authentication-for-the-Azure-Storage-Services)voor meer informatie over het betreffende protocol. Service Bus-wacht rijen bieden ondersteuning voor een vergelijkbaar model met behulp van symmetrische sleutels. Zie [Shared Access Signature Authentication with Service Bus](service-bus-sas.md)(Engelstalig) voor meer informatie.

## <a name="conclusion"></a>Conclusie
Door een beter inzicht te krijgen in de twee technologieën, kunt u een meer weloverwogen beslissing nemen over de wachtrij technologie die moet worden gebruikt en wanneer. De beslissing over het gebruik van opslag wachtrijen of Service Bus wacht rijen is duidelijk afhankelijk van een aantal factoren. Deze factoren kunnen afhankelijk zijn van de individuele behoeften van uw toepassing en de bijbehorende architectuur. Als uw toepassing al gebruikmaakt van de kern mogelijkheden van Microsoft Azure, kunt u de voor keur geven aan opslag wachtrijen, met name als u basis communicatie en Messa ging tussen services nodig hebt of als u wacht rijen nodig hebt die groter kunnen zijn dan 80 GB.

Omdat Service Bus wacht rijen een aantal geavanceerde functies bieden, zoals sessies, trans acties, duplicaten detectie, automatische onbestelbare berichten en duurzame functies voor publiceren/abonneren, kunnen ze een voorkeurs keuze zijn als u een hybride toepassing bouwt of als uw toepassing anders deze functies vereist.

## <a name="next-steps"></a>Volgende stappen
De volgende artikelen bevatten meer richt lijnen en informatie over het gebruik van opslag wachtrijen of Service Bus wacht rijen.

* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [De Queue Storage-service gebruiken](../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Aanbevolen procedures voor prestatie verbeteringen met behulp van Service Bus Brokered Messaging](service-bus-performance-improvements.md)
* [Inleiding tot wacht rijen en onderwerpen in Azure Service Bus (blog bericht)](https://www.serverless360.com/blog/azure-service-bus-queues-vs-topics)
* [De hand leiding voor ontwikkel aars voor het Service Bus](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
* [De Queuing-service in azure gebruiken](https://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)

[Azure portal]: https://portal.azure.com

