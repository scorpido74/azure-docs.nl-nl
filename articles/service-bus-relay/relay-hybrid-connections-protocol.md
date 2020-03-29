---
title: Protocolhandleiding azure relay hybride verbindingen | Microsoft Documenten
description: In dit artikel worden de interacties aan de clientzijde beschreven met het relay Hybrid Connections voor het verbinden van clients in listener- en zenderrollen.
services: service-bus-relay
documentationcenter: na
author: clemensv
manager: timlt
editor: ''
ms.assetid: 149f980c-3702-4805-8069-5321275bc3e8
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: clemensv
ms.openlocfilehash: 68668452152064584d1c419a3053ccb642b103f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514949"
---
# <a name="azure-relay-hybrid-connections-protocol"></a>Azure Relay Hybrid Connections-protocol

Azure Relay is een van de belangrijkste pijlers van het Azure Service Bus-platform. De nieuwe _Hybrid Connections-mogelijkheid_ van Relay is een veilige, open-protocol evolutie op basis van HTTP en WebSockets. Het vervangt de voormalige, ook wel _BizTalk Services-functie_ die is gebouwd op een eigen protocolstichting. De integratie van hybride verbindingen in Azure App Services blijft functioneren zoals het is.

Hybrid Connections maakt bidirectionele, binaire stroomcommunicatie en eenvoudige datagramstroom tussen twee netwerktoepassingen mogelijk. Een van beide of beide partijen kan zich achter NAT's of firewalls bevinden.

In dit artikel worden de interacties aan de clientzijde beschreven met het relay Hybrid Connections voor het verbinden van clients in listener- en zenderrollen. Het beschrijft ook hoe luisteraars nieuwe verbindingen en verzoeken accepteren.

## <a name="interaction-model"></a>Interactiemodel

De Hybrid Connections relay verbindt twee partijen door een rendez-vouspunt in de Azure-cloud te bieden waar partijen vanuit het perspectief van hun eigen netwerk verbinding mee kunnen maken. Het rendez-vouspunt wordt in deze en andere documentatie, in de API's en ook in de Azure-portal "Hybride verbinding" genoemd. Het eindpunt van de hybride verbindingenservice wordt de "service" voor de rest van dit artikel genoemd.

De service maakt het mogelijk om Web Socket-verbindingen en HTTP(S)-verzoeken en -antwoorden opnieuw uit te leggen.

Het interactiemodel is afhankelijk van de nomenclatuur die door veel andere netwerk-API's is opgesteld. Er is een luisteraar die eerst aangeeft dat ze klaar zijn om binnenkomende verbindingen te verwerken en deze vervolgens accepteert wanneer ze aankomen. Aan de andere kant maakt een client verbinding met de luisteraar, in de verwachting dat die verbinding wordt geaccepteerd voor het tot stand brengen van een bidirectioneel communicatiepad. 'Connect', 'Luisteren' en 'Accepteren' zijn dezelfde termen die u in de meeste API's van sockets vindt.

Elk doorgegeven communicatiemodel heeft een van beide partijen die uitgaande verbindingen maken naar een serviceeindpunt. Dit maakt de "luisteraar" ook een "client" in informele gebruik, en kan ook leiden tot andere terminologie overbelasting. De precieze terminologie die daarom voor hybrid connections wordt gebruikt, is als volgt:

De programma's aan beide zijden van een verbinding worden "clients" genoemd, omdat ze clients zijn voor de service. De client die wacht op en accepteert verbindingen is de "luisteraar", of wordt gezegd dat in de "luisteraar rol." De client die via de service een nieuwe verbinding naar een listener initieert, wordt de 'afzender' genoemd of bevindt zich in de 'afzenderrol'.

### <a name="listener-interactions"></a>Listenerinteracties

De luisteraar heeft vijf interacties met de dienst; alle draaddetails worden later in dit artikel in de referentiesectie beschreven.

De berichten Luisteren, Accepteren en Aanvragen worden ontvangen van de service. De bewerkingen Vernieuwen en Ping worden verzonden door de listener.

#### <a name="listen-message"></a>Luisterbericht

Als u de service gereed wilt maken dat een listener klaar is om verbindingen te accepteren, wordt een uitgaande WebSocket-verbinding gemaakt. De verbindingshandshake draagt de naam van een hybride verbinding die is geconfigureerd op de naamruimte Relay en een beveiligingstoken dat het 'Luisteren' recht op die naam verleent.

Wanneer de WebSocket door de service wordt geaccepteerd, is de registratie voltooid en wordt de gevestigde WebSocket in leven gehouden als het "controlekanaal" voor het inschakelen van alle volgende interacties. De dienst staat tot 25 gelijktijdige luisteraars voor één Hybride Verbinding toe. Het quotum voor AppHooks moet worden bepaald.

Voor hybride verbindingen, als er twee of meer actieve listeners zijn, worden binnenkomende verbindingen in willekeurige volgorde over deze verbindingen gebalanceerd; eerlijke distributie wordt met de beste inspanning geprobeerd.

#### <a name="accept-message"></a>Bericht accepteren

Wanneer een afzender een nieuwe verbinding op de service opent, kiest en waarschuwt de service een van de actieve listeners op de hybride verbinding. Deze melding wordt naar de luisteraar verzonden via het open controlekanaal als json-bericht. Het bericht bevat de URL van het WebSocket-eindpunt waarmee de listener verbinding moet maken voor het accepteren van de verbinding.

De URL kan en moet direct door de luisteraar worden gebruikt zonder extra werk.
De gecodeerde informatie is slechts voor een korte periode geldig, in wezen zolang de afzender bereid is te wachten tot de verbinding end-to-end tot stand komt. Het maximum is 30 seconden. De URL kan alleen worden gebruikt voor één geslaagde verbindingspoging. Zodra de WebSocket-verbinding met de rendez-vous-URL tot stand is gebracht, wordt alle verdere activiteit op deze WebSocket van en naar de afzender doorgestuurd. Dit gebeurt zonder tussenkomst of interpretatie door de dienst.

### <a name="request-message"></a>Bericht aanvragen

Naast WebSocket-verbindingen kan de listener ook HTTP-aanvraagframes van een afzender ontvangen, als deze mogelijkheid expliciet is ingeschakeld op de hybride verbinding.

Luisteraars die met HTTP-ondersteuning hybride `request` verbindingen koppelen, moeten het gebaar verwerken. Een listener die niet `request` verwerkt en dus veroorzaakt herhaalde time-out fouten tijdens het aansluiten kan worden op de zwarte lijst door de dienst in de toekomst.

HTTP frame header metadata wordt vertaald in JSON voor eenvoudigere verwerking door de listener framework, ook omdat HTTP header parsing bibliotheken zijn zeldzamer dan JSON parsers. HTTP-metagegevens die alleen relevant zijn voor de relatie tussen de afzender en de RELAY HTTP-gateway, inclusief autorisatiegegevens, worden niet doorgestuurd. HTTP-aanvraaginstanties worden transparant overgedragen als binaire WebSocket-frames.

De listener kan reageren op HTTP-verzoeken met een gelijkwaardige reactiebeweging.

De aanvraag-/antwoordstroom maakt standaard gebruik van het besturingskanaal, maar kan worden "geüpgraded" naar een afzonderlijke rendez-vous WebSocket wanneer dat nodig is. Verschillende WebSocket-verbindingen verbeteren de doorvoer voor elk clientgesprek, maar ze belasten de luisteraar met meer verbindingen die moeten worden afgehandeld, wat mogelijk niet wenselijk is voor lichtgewicht clients.

Op het controlekanaal zijn aanvraag- en antwoordorganen beperkt tot maximaal 64 kB groot. HTTP header metadata is beperkt tot een totaal van 32 kB. Als het verzoek of het antwoord die drempel waarde overschrijdt, moet de listener upgraden naar een rendez-vous WebSocket met een gebaar dat gelijkwaardig is aan het verwerken van de [Accepteren](#accept-message).

Voor aanvragen bepaalt de service of aanvragen via het controlekanaal worden doorgestuurd. Dit omvat, maar mag niet worden beperkt tot gevallen waarin een verzoek meer dan 64 kB (headers plus body) ronduit overschrijdt, of als het verzoek wordt verzonden met ["chunked" transfer-encoding](https://tools.ietf.org/html/rfc7230#section-4.1) en de service reden heeft om te verwachten dat het verzoek hoger is dan 64kB of het lezen van het verzoek niet onmiddellijk is. Als de service ervoor kiest om het verzoek te leveren via rendez-vous, geeft het alleen het rendez-vousadres door aan de luisteraar.
De luisteraar moet dan de rendez-vous WebSocket en de dienst onmiddellijk levert de volledige aanvraag met inbegrip van organen over de rendez-vous WebSocket. Het antwoord moet ook gebruik maken van de rendez-vous WebSocket.

Voor verzoeken die via het controlekanaal aankomen, beslist de luisteraar of hij via het controlekanaal of via rendez-vous reageert. De dienst MOET een rendez-vousadres bevatten bij elk verzoek dat over het controlekanaal wordt gerouteerd. Dit adres is alleen geldig voor het upgraden van de huidige aanvraag.

Als de luisteraar ervoor kiest om te upgraden, verbindt en levert het onmiddellijk het antwoord via de gevestigde rendez-vous socket.

Zodra de rendez-vous WebSocket is vastgesteld, moet de luisteraar te handhaven voor de verdere behandeling van verzoeken en antwoorden van dezelfde client. De service onderhoudt de WebSocket zolang de HTTPS-socketverbinding met de afzender blijft bestaan en stuurt alle volgende aanvragen van die afzender over de onderhouden WebSocket. Als de listener ervoor kiest om de rendez-vous WebSocket van zijn kant te laten vallen, zal de service ook de verbinding met de afzender laten vallen, ongeacht of er al een volgende aanvraag aan de gang is.

#### <a name="renew-operation"></a>Bewerking verlengen

Het beveiligingstoken dat moet worden gebruikt om de listener te registreren en het controlekanaal te onderhouden, kan verlopen terwijl de listener actief is. Het verlopen van het token heeft geen invloed op lopende verbindingen, maar zorgt er wel voor dat het controlekanaal door de service wordt verwijderd op of kort na het verstrijken. De bewerking 'vernieuwen' is een JSON-bericht dat de listener kan verzenden om het token te vervangen dat is gekoppeld aan het controlekanaal, zodat het controlekanaal gedurende langere perioden kan worden gehandhaafd.

#### <a name="ping-operation"></a>Ping-bewerking

Als het controlekanaal lange tijd inactief blijft, kunnen tussenpersonen onderweg, zoals load balancers of NAT's, de TCP-verbinding laten vallen. De "ping" operatie vermijdt dat door het verzenden van een kleine hoeveelheid gegevens op het kanaal dat iedereen op de netwerkroute eraan herinnert dat de verbinding is bedoeld om te leven, en het dient ook als een "live" test voor de luisteraar. Als de ping mislukt, moet het controlekanaal als onbruikbaar worden beschouwd en moet de luisteraar opnieuw verbinding maken.

### <a name="sender-interaction"></a>Interactie met afzenders

De afzender heeft twee interacties met de service: het verbindt een websocket of stuurt aanvragen via HTTPS. Aanvragen kunnen niet via een websocket worden verzonden vanuit de afzenderrol.

#### <a name="connect-operation"></a>Bewerking verbinden

De bewerking 'verbinden' opent een WebSocket op de service, met de naam van de hybride verbinding en (optioneel, maar standaard vereist) een beveiligingstoken dat 'Verzenden' toestemming verleent in de querytekenreeks. De service werkt vervolgens samen met de luisteraar op de eerder beschreven manier en de listener maakt een rendez-vousverbinding die is verbonden met deze WebSocket. Nadat de WebSocket is geaccepteerd, zijn alle verdere interacties op die WebSocket met een verbonden listener.

#### <a name="request-operation"></a>Aanvraagbewerking

Voor hybride verbindingen waarvoor de functie is ingeschakeld, kan de afzender grotendeels onbeperkte HTTP-aanvragen naar luisteraars verzenden.

Behalve voor een relay-toegangstoken dat is ingesloten in de querytekenreeks of in een HTTP-header van de aanvraag, is het relay volledig transparant voor alle HTTP-bewerkingen op het relayadres en alle achtervoegsels van het relay-adrespad, waardoor de luisteraar volledig de controle heeft over end-to-end autorisatie en zelfs HTTP-extensiefuncties zoals [CORS](https://www.w3.org/TR/cors/).

Afzenderautorisatie met het relay-eindpunt is standaard ingeschakeld, maar is OPTIONEEL. De eigenaar van de hybride verbinding kan ervoor kiezen om anonieme afzenders toe te staan. De service onderschept, inspecteert en stript autorisatiegegevens als volgt:

1. Als de querytekenreeks `sb-hc-token` een expressie bevat, wordt de expressie ALTIJD ontdaan van de querytekenreeks. Het wordt geëvalueerd als relayautorisatie is ingeschakeld.
2. Als de aanvraagkoppen `ServiceBusAuthorization` een koptekst bevatten, wordt de koptekstexpressie ALTIJD uit de headerverzameling verwijderd.
   Het wordt geëvalueerd als relayautorisatie is ingeschakeld.
3. Alleen als relayautorisatie is ingeschakeld en als de `Authorization` aanvraagkoppen een koptekst bevatten en geen van de eerdere expressies aanwezig is, wordt de koptekst geëvalueerd en gestript. Anders wordt `Authorization`het altijd doorgegeven zoals het is.

Als er geen actieve listener is, retourneert de service een foutcode van 502 'Bad Gateway'. Als de service de aanvraag niet lijkt af te handelen, retourneert de service na 60 seconden een 504 "Gateway Timeout".

### <a name="interaction-summary"></a>Interactieoverzicht

Het resultaat van dit interactiemodel is dat de afzenderclient uit de handdruk komt met een "schone" WebSocket, die is verbonden met een luisteraar en die geen verdere voorbereidingen of voorbereiding nodig heeft. Dit model maakt het mogelijk vrijwel elke bestaande WebSocket-clientimplementatie om gemakkelijk te profiteren van de hybrid connections-service door een correct geconstrueerde URL in hun WebSocket-clientlaag te leveren.

De rendez-vous verbinding WebSocket die de luisteraar verkrijgt door middel van de interactie accepteren is ook schoon en kan worden overhandigd aan een bestaande WebSocket server implementatie met een aantal minimale extra abstractie die onderscheid maakt tussen "accepteren" operaties op hun framework lokale netwerk luisteraars en Hybride Verbindingen remote "accepteren" operaties.

Het HTTP-aanvraag-/antwoordmodel geeft de afzender een grotendeels onbeperkt HTTP-protocoloppervlak met een optionele autorisatielaag. De luisteraar krijgt een vooraf geparseerde HTTP-aanvraagheadersectie die kan worden omgezet in een downstream HTTP-aanvraag of wordt behandeld zoals het is, met binaire frames die HTTP-instanties dragen. Reacties gebruiken dezelfde indeling. Interacties met minder dan 64 KB aan aanvraag- en antwoordbody kunnen worden afgehandeld via één socket die voor alle afzenders wordt gedeeld. Grotere aanvragen en antwoorden kunnen worden behandeld met behulp van het rendez-vousmodel.

## <a name="protocol-reference"></a>Protocolverwijzing

In deze sectie worden de details beschreven van de eerder beschreven protocolinteracties.

Alle WebSocket-verbindingen worden gemaakt op poort 443 als een upgrade van HTTPS 1.1, die vaak wordt geabstraheerd door een websocket-framework of API. De beschrijving hier wordt gehouden implementatie neutraal, zonder een specifiek kader te suggereren.

### <a name="listener-protocol"></a>Listenerprotocol

Het listenerprotocol bestaat uit twee verbindingsgebaren en drie berichtbewerkingen.

#### <a name="listener-control-channel-connection"></a>Verbinding met listenerbeheerkanaal

Het besturingskanaal wordt geopend met het maken van een WebSocket-verbinding met:

`wss://{namespace-address}/$hc/{path}?sb-hc-action=...[&sb-hc-id=...]&sb-hc-token=...`

Het `namespace-address` is de volledig gekwalificeerde domeinnaam van de Azure Relay-naamruimte die `{myname}.servicebus.windows.net`de hybride verbinding host, meestal van het formulier .

De parameteropties voor querytekenreeksen zijn als volgt.

| Parameter        | Vereist | Beschrijving
| ---------------- | -------- | -------------------------------------------
| `sb-hc-action`   | Ja      | Voor de listenerrol moet de parameter **sb-hc-action=listen=listen**
| `{path}`         | Ja      | Het door URL gecodeerde naamruimtepad van de vooraf geconfigureerde hybride verbinding om deze listener op te registreren. Deze expressie wordt toegevoegd `$hc/` aan het vaste padgedeelte.
| `sb-hc-token`    | Ja\*    | De listener moet een geldig, URL-gecodeerd Service Bus Shared Access Token opgeven voor de naamruimte of hybride verbinding die het **rechter luisteren** verleent.
| `sb-hc-id`       | Nee       | Deze door de client geleverde optionele ID maakt end-to-end diagnostische tracering mogelijk.

Als de WebSocket-verbinding mislukt omdat het pad voor hybride verbinding niet is geregistreerd, of een ongeldig of ontbrekend token of een andere fout, wordt de foutfeedback gegeven met behulp van het reguliere HTTP 1.1-statusfeedbackmodel. De statusbeschrijving bevat een fouttracking-id die kan worden gecommuniceerd naar Azure-ondersteuningspersoneel:

| Code | Fout          | Beschrijving
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Niet gevonden      | Het pad Hybride verbinding is ongeldig of de basis-URL is vervormd.
| 401  | Niet geautoriseerd   | Het beveiligingstoken ontbreekt, misvormd of ongeldig.
| 403  | Verboden      | Het beveiligingstoken is niet geldig voor dit pad voor deze actie.
| 500  | Interne fout | Er ging iets mis in de dienst.

Als de WebSocket-verbinding opzettelijk wordt afgesloten door de service nadat deze in eerste instantie is ingesteld, wordt de reden hiervoor gecommuniceerd met behulp van een geschikte WebSocket-protocolfoutcode, samen met een beschrijvend foutbericht dat ook een tracking-id bevat. De service sluit het controlekanaal niet af zonder een foutvoorwaarde te ondervinden. Elke schone shutdown wordt door de klant gecontroleerd.

| WS-status | Beschrijving
| --------- | -------------------------------------------------------------------------------
| 1001      | Het pad Hybride verbinding is verwijderd of uitgeschakeld.
| 1008      | Het beveiligingstoken is verlopen, daarom wordt het autorisatiebeleid geschonden.
| 1011      | Er ging iets mis in de dienst.

#### <a name="accept-handshake"></a>Handdruk accepteren

De melding 'accepteren' wordt door de service naar de listener verzonden via het eerder ingestelde controlekanaal als json-bericht in een WebSocket-tekstkader. Er is geen antwoord op dit bericht.

Het bericht bevat een JSON-object met de naam 'accepteren', dat op dit moment de volgende eigenschappen definieert:

* **adres** : de URL-tekenreeks die moet worden gebruikt voor het instellen van de WebSocket op de service om een binnenkomende verbinding te accepteren.
* **id** – de unieke id voor deze verbinding. Als de ID is geleverd door de afzender client, het is de afzender opgegeven waarde, anders is het een systeem gegenereerde waarde.
* **connectHeaders** – alle HTTP-headers die door de afzender aan het Relay-eindpunt zijn geleverd, waaronder ook het Sec-WebSocket-Protocol en de headers Sec-WebSocket-Extensions.

```json
{
    "accept" : {
        "address" : "wss://dc-node.servicebus.windows.net:443/$hc/{path}?..."
        "id" : "4cb542c3-047a-4d40-a19f-bdc66441e736",
        "connectHeaders" : {
            "Host" : "...",
            "Sec-WebSocket-Protocol" : "...",
            "Sec-WebSocket-Extensions" : "..."
        }
     }
}
```

De adres-URL in het JSON-bericht wordt door de listener gebruikt om de WebSocket vast te stellen voor het accepteren of weigeren van de afzendersocket.

##### <a name="accepting-the-socket"></a>De socket accepteren

Als u dit wilt accepteren, maakt de listener een WebSocket-verbinding met het opgegeven adres.

Als het bericht 'accepteren' `Sec-WebSocket-Protocol` een koptekst bevat, wordt verwacht dat de listener alleen de WebSocket accepteert als het dat protocol ondersteunt. Bovendien stelt het de koptekst in zoals de WebSocket is ingesteld.

Hetzelfde geldt `Sec-WebSocket-Extensions` voor de koptekst. Als het framework een extensie ondersteunt, moet de koptekst worden `Sec-WebSocket-Extensions` ingesteld op het antwoord aan de serverzijde van de vereiste handdruk voor de extensie.

De URL moet worden gebruikt voor het instellen van de acceptsocket, maar bevat de volgende parameters:

| Parameter      | Vereist | Beschrijving
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Ja      | Voor het accepteren van een socket moet de parameter`sb-hc-action=accept`
| `{path}`       | Ja      | (zie de volgende alinea)
| `sb-hc-id`     | Nee       | Zie eerdere beschrijving van **id**.

`{path}`is het door de URL gecodeerde naamruimtepad van de vooraf geconfigureerde hybride verbinding waarop deze listener kan worden geregistreerd. Deze expressie wordt toegevoegd `$hc/` aan het vaste padgedeelte.

De `path` expressie kan worden uitgebreid met een achtervoegsel en een querytekenreeksexpressie die de geregistreerde naam volgt na een scheiding sperslash.
Hierdoor kan de afzenderclient verzendargumenten doorgeven aan de accepterende listener wanneer het niet mogelijk is http-headers op te nemen. De verwachting is dat het listenerframework het vaste padgedeelte en de geregistreerde naam van het pad ontleedt en de rest, mogelijk zonder argumenten voor querytekenreeksen vooraf, `sb-`beschikbaar maakt voor de toepassing om te beslissen of de verbinding moet worden geaccepteerd.

Zie de volgende sectie 'Sender Protocol' voor meer informatie.

Als er een fout optreedt, kan de service als volgt antwoorden:

| Code | Fout          | Beschrijving
| ---- | -------------- | -----------------------------------
| 403  | Verboden      | De URL is ongeldig.
| 500  | Interne fout | Er ging iets mis in de dienst

 Nadat de verbinding is gemaakt, schakelt de server de WebSocket af wanneer de afzender WebSocket wordt afgesloten of met de volgende status:

| WS-status | Beschrijving                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | De afzenderclient sluit de verbinding af.                                    |
| 1001      | Het pad Hybride verbinding is verwijderd of uitgeschakeld.                        |
| 1008      | Het beveiligingstoken is verlopen, daarom wordt het autorisatiebeleid geschonden. |
| 1011      | Er ging iets mis in de dienst.                                            |

##### <a name="rejecting-the-socket"></a>De socket weigeren

 Als u de socket `accept` weigert na het inspecteren van het bericht, is een vergelijkbare handdruk vereist, zodat de statuscode en statusbeschrijving die de reden voor de afwijzing communiceren, naar de afzender kunnen vloeien.

 De keuze voor het protocolontwerp is om een WebSocket-handshake te gebruiken (die is ontworpen om te eindigen in een gedefinieerde foutstatus), zodat listenerclientimplementaties kunnen blijven vertrouwen op een WebSocket-client en geen extra, kale HTTP-client hoeft in dienst te nemen.

 Als u de socket wilt weigeren, `accept` neemt de client het adres URI uit het bericht en voegt hij er als volgt twee querytekenreeksparameters aan toe:

| Param                   | Vereist | Beschrijving                              |
| ----------------------- | -------- | ---------------------------------------- |
| sb-hc-statusCode        | Ja      | Numerieke HTTP-statuscode.                |
| sb-hc-statusBeschrijving | Ja      | Menselijke leesbare reden voor de afwijzing. |

De resulterende URI wordt vervolgens gebruikt om een WebSocket-verbinding tot stand te brengen.

Bij het correct invullen mislukt deze handdruk opzettelijk met een HTTP-foutcode 410, omdat er geen WebSocket is ingesteld. Als er iets misgaat, worden de fout in de volgende codes beschreven:

| Code | Fout          | Beschrijving                          |
| ---- | -------------- | ------------------------------------ |
| 403  | Verboden      | De URL is ongeldig.                |
| 500  | Interne fout | Er ging iets mis in de dienst. |

#### <a name="request-message"></a>Bericht aanvragen

Het `request` bericht wordt door de service naar de luisteraar over het controlekanaal verzonden. Hetzelfde bericht wordt ook verzonden via de rendez-vous WebSocket eenmaal vastgesteld.

De `request` bestaat uit twee delen: een kop- en binaire bodyframe(s).
Als er geen lichaam is, worden de lichaamskaders weggelaten. De indicator voor de vraag of `body` een lichaam aanwezig is, is de booleaanse eigenschap in het verzoekbericht.

Voor een aanvraag bij een aanvraaginstantie kan de structuur er als volgt uitzien:

``` text
----- Web Socket text frame ----
{
    "request" :
    {
        "body" : true,
        ...
    }
}
----- Web Socket binary frame ----
FEFEFEFEFEFEFEFEFEFEF...
----- Web Socket binary frame ----
FEFEFEFEFEFEFEFEFEFEF...
----- Web Socket binary frame -FIN
FEFEFEFEFEFEFEFEFEFEF...
----------------------------------
```

De listener MOET de ontvangst van de aanvraagbody over meerdere binaire frames afhandelen (zie [WebSocket-fragmenten).](https://tools.ietf.org/html/rfc6455#section-5.4)
De aanvraag eindigt wanneer een binair frame met de FIN-vlagset is ontvangen.

Voor een aanvraag zonder lichaam is er maar één tekstkader.

``` text
----- Web Socket text frame ----
{
    "request" :
    {
        "body" : false,
        ...
    }
}
----------------------------------
```

De JSON-inhoud voor `request` is als volgt:

* **adres** - URI-tekenreeks. Dit is het rendez-vous adres om te gebruiken voor dit verzoek. Als de binnenkomende aanvraag groter is dan 64 kB, blijft de rest van dit bericht `accept` leeg en moet de client een rendez-voushanddruk starten die gelijkwaardig is aan de onderstaande bewerking. De service plaatst vervolgens `request` de volledige op de gevestigde websocket. Als het antwoord naar verwachting meer dan 64 kB zal bedragen, moet de luisteraar ook een rendez-voushanddruk starten en vervolgens het antwoord overbrengen via de gevestigde websocket.
* **id** – string. De unieke id voor dit verzoek.
* **requestHeaders** – dit object bevat alle HTTP-headers die door de afzender aan het eindpunt zijn geleverd, met uitzondering van autorisatiegegevens zoals [hierboven](#request-operation)uiteengezet, en kopteksten die strikt betrekking hebben op de verbinding met de gateway. In het bijzonder worden ALLE kopteksten gedefinieerd of `Via`gereserveerd in [RFC7230,](https://tools.ietf.org/html/rfc7230)behalve , gestript en niet doorgestuurd:

  * `Connection`(RFC7230, punt 6.1)
  * `Content-Length`(RFC7230, punt 3.3.2)
  * `Host`(RFC7230, punt 5.4)
  * `TE`(RFC7230, punt 4.3)
  * `Trailer`(RFC7230, punt 4.4)
  * `Transfer-Encoding`(RFC7230, punt 3.3.1)
  * `Upgrade`(RFC7230, punt 6.7)
  * `Close`(RFC7230, punt 8.1)

* **requestTarget** – tekenreeks. Deze eigenschap bevat het ["Aanvraagdoel" (RFC7230, sectie 5.3)](https://tools.ietf.org/html/rfc7230#section-5.3) van de aanvraag. Dit omvat het gedeelte van de `sb-hc-` querytekenreeks, dat is ontdaan van ALLE vooraf vastgestelde parameters.
* **methode** - tekenreeks. Dit is de methode van het verzoek, per [RFC7231, sectie 4](https://tools.ietf.org/html/rfc7231#section-4). De `CONNECT` methode MAG NIET worden gebruikt.
* **lichaam** – booleaan. Hiermee geeft u aan of een of meer binaire bodyframe volgt.

``` JSON
{
    "request" : {
        "address" : "wss://dc-node.servicebus.windows.net:443/$hc/{path}?...",
        "id" : "42c34cb5-7a04-4d40-a19f-bdc66441e736",
        "requestTarget" : "/abc/def?myarg=value&otherarg=...",
        "method" : "GET",
        "requestHeaders" : {
            "Host" : "...",
            "Content-Type" : "...",
            "User-Agent" : "..."
        },
        "body" : true
     }
}
```

##### <a name="responding-to-requests"></a>Reageren op verzoeken

De ontvanger MOET reageren. Herhaaldelijk niet reageren op verzoeken met behoud van de verbinding kan ertoe leiden dat de listener op de zwarte lijst komt te staan.

Antwoorden kunnen in elke volgorde worden verzonden, maar elk verzoek moet binnen 60 seconden worden beantwoord of de levering wordt gerapporteerd als mislukt. De deadline van 60 seconden `response` wordt geteld totdat het frame door de service is ontvangen. Een doorlopende reactie met meerdere binaire frames kan niet langer dan 60 seconden worden gebruikt of wordt beëindigd.

Als het verzoek via het controlekanaal wordt ontvangen, moet het antwoord worden verzonden op het controlekanaal van waaruit het verzoek is ontvangen, ofwel moet het via een rendez-vouskanaal worden verzonden.

Het antwoord is een JSON-object met de naam "response". De regels voor het verwerken van `request` lichaamsinhoud zijn `body` precies zoals bij het bericht en gebaseerd op het pand.

* **requestId** – string. Vereist. De `id` waarde van `request` de eigenschap van het bericht dat wordt gereageerd.
* **statusCode** – getal. Vereist. een numerieke HTTP-statuscode die de uitkomst van de melding aangeeft. Alle statuscodes van [RFC7231, sectie 6](https://tools.ietf.org/html/rfc7231#section-6) zijn toegestaan, behalve [502 "Bad Gateway"](https://tools.ietf.org/html/rfc7231#section-6.6.3) en [504 "Gateway Timeout".](https://tools.ietf.org/html/rfc7231#section-6.6.5)
* **statusBeschrijving** - tekenreeks. Optionele. HTTP-coderedenzin per [RFC7230, punt 3.1.2](https://tools.ietf.org/html/rfc7230#section-3.1.2)
* **responseHeaders** – HTTP-headers die moeten worden ingesteld in een extern HTTP-antwoord.
  Net als `request`bij de , RFC7230 gedefinieerde headers MAG NIET worden gebruikt.
* **lichaam** – booleaan. Hiermee geeft u aan of binaire body frame(s) volgen(en).

``` text
----- Web Socket text frame ----
{
    "response" : {
        "requestId" : "42c34cb5-7a04-4d40-a19f-bdc66441e736",
        "statusCode" : "200",
        "responseHeaders" : {
            "Content-Type" : "application/json",
            "Content-Encoding" : "gzip"
        }
         "body" : true
     }
}
----- Web Socket binary frame -FIN
{ "hey" : "mydata" }
----------------------------------
```

##### <a name="responding-via-rendezvous"></a>Reageren via rendez-vous

Voor reacties van meer dan 64 kB moet het antwoord via een rendez-voussocket worden geleverd. Als de aanvraag meer dan 64 `request` kB bedraagt en het enige adresveld bevat, moet er een rendez-voussocket worden ingesteld om de `request`. Zodra een rendez-vous socket is vastgesteld, antwoorden op de respectieve client en de daaropvolgende verzoeken van die respectieve client moet worden geleverd over de rendez-vous socket, terwijl het blijft bestaan.

De `address` URL `request` in de moet worden gebruikt als-is voor het instellen van de rendez-vous socket, maar bevat de volgende parameters:

| Parameter      | Vereist | Beschrijving
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Ja      | Voor het accepteren van een socket moet de parameter`sb-hc-action=request`

Als er een fout optreedt, kan de service als volgt antwoorden:

| Code | Fout           | Beschrijving
| ---- | --------------- | -----------------------------------
| 400  | Ongeldig verzoek | Niet-herkende actie of URL niet geldig.
| 403  | Verboden       | De URL is verlopen.
| 500  | Interne fout  | Er ging iets mis in de dienst

 Nadat de verbinding is ingesteld, schakelt de server de WebSocket af wanneer de HTTP-socket van de client wordt afgesloten of met de volgende status:

| WS-status | Beschrijving                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | De afzenderclient sluit de verbinding af.                                    |
| 1001      | Het pad Hybride verbinding is verwijderd of uitgeschakeld.                        |
| 1008      | Het beveiligingstoken is verlopen, daarom wordt het autorisatiebeleid geschonden. |
| 1011      | Er ging iets mis in de dienst.                                            |


#### <a name="listener-token-renewal"></a>Listenertokenverlenging

Wanneer het listenertoken bijna verloopt, kan het worden vervangen door een tekstkaderbericht naar de service te sturen via het gevestigde controlekanaal. Het bericht bevat een `renewToken`JSON-object genaamd , dat de volgende eigenschap op dit moment definieert:

* **token** : een geldig, URL-gecodeerd Service Bus Shared Access-token voor de naamruimte of hybride verbinding die het **rechter luisteren** verleent.

```json
{
  "renewToken": {
    "token":
      "SharedAccessSignature sr=http%3a%2f%2fcontoso.servicebus.windows.net%2fhyco%2f&amp;sig=XXXXXXXXXX%3d&amp;se=1471633754&amp;skn=SasKeyName"
  }
}
```

Als de tokenvalidatie mislukt, wordt de toegang geweigerd en sluit de cloudservice het besturingskanaal WebSocket met een fout. Anders is er geen antwoord.

| WS-status | Beschrijving                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1008      | Het beveiligingstoken is verlopen, daarom wordt het autorisatiebeleid geschonden. |

### <a name="web-socket-connect-protocol"></a>Web Socket connect-protocol

Het afzenderprotocol is in feite identiek aan de manier waarop een listener is ingesteld.
Het doel is maximale transparantie voor de end-to-end WebSocket. Het adres waarmee u verbinding wilt maken is hetzelfde als voor de luisteraar, maar de "actie" verschilt en het token heeft een andere toestemming nodig:

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...&sb-hc-id=...&sbc-hc-token=...
```

Het _naamruimteadres_ is de volledig gekwalificeerde domeinnaam van de azure relay-naamruimte die `{myname}.servicebus.windows.net`de hybride verbinding host, meestal van het formulier .

De aanvraag kan willekeurige extra HTTP-headers bevatten, inclusief door toepassingen gedefinieerde headers. Alle meegeleverde headers stromen naar de luisteraar `connectHeader` en zijn te vinden op het object van het acceptatie-controlebericht. **accept**

De parameteropties voor querytekenreeksen zijn als volgt:

| Param          | Vereist? | Beschrijving
| -------------- | --------- | -------------------------- |
| `sb-hc-action` | Ja       | Voor de verzendrol moet `sb-hc-action=connect`de parameter .
| `{path}`       | Ja       | (zie de volgende alinea)
| `sb-hc-token`  | Ja\*     | De listener moet een geldig, URL-gecodeerd Service Bus Shared Access Token opgeven voor de naamruimte of hybride verbinding die het **recht verzenden** verleent.
| `sb-hc-id`     | Nee        | Een optionele ID die end-to-end diagnostische tracering mogelijk maakt en beschikbaar wordt gesteld aan de luisteraar tijdens de accepteren handdruk.

 Het `{path}` is het URL-gecodeerde naamruimtepad van de vooraf geconfigureerde hybride verbinding waarop deze listener kan worden geregistreerd. De `path` expressie kan worden uitgebreid met een achtervoegsel en een querytekenreeksexpressie om verder te communiceren. Als de hybride verbinding onder `hyco`het `path` pad `hyco/suffix?param=value&...` is geregistreerd, kan de expressie worden gevolgd door de querytekenreeksparameters die hier zijn gedefinieerd. Een volledige uitdrukking kan dan als volgt zijn:

```
wss://{namespace-address}/$hc/hyco/suffix?param=value&sb-hc-action=...[&sb-hc-id=...&]sbc-hc-token=...
```

De `path` expressie wordt doorgegeven aan de luisteraar in het adres URI in het controlebericht accepteren.

Als de WebSocket-verbinding mislukt omdat het pad voor hybride verbindingen niet is geregistreerd, een ongeldig of ontbrekend token of een andere fout is, wordt de foutfeedback gegeven met behulp van het reguliere HTTP 1.1-statusfeedbackmodel. De statusbeschrijving bevat een fouttracking-id die kan worden gecommuniceerd naar Azure-ondersteuningspersoneel:

| Code | Fout          | Beschrijving
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Niet gevonden      | Het pad Hybride verbinding is ongeldig of de basis-URL is vervormd.
| 401  | Niet geautoriseerd   | Het beveiligingstoken ontbreekt, misvormd of ongeldig.
| 403  | Verboden      | Het beveiligingstoken is niet geldig voor dit pad en voor deze actie.
| 500  | Interne fout | Er ging iets mis in de dienst.

Als de WebSocket-verbinding opzettelijk wordt afgesloten door de service nadat deze in eerste instantie is ingesteld, wordt de reden hiervoor gecommuniceerd met behulp van een geschikte WebSocket-protocolfoutcode, samen met een beschrijvend foutbericht dat ook een tracking-id bevat .

| WS-status | Beschrijving
| --------- | ------------------------------------------------------------------------------- 
| 1000      | De luisteraar heeft de aansluiting uitgeschakeld.
| 1001      | Het pad Hybride verbinding is verwijderd of uitgeschakeld.
| 1008      | Het beveiligingstoken is verlopen, daarom wordt het autorisatiebeleid geschonden.
| 1011      | Er ging iets mis in de dienst.

### <a name="http-request-protocol"></a>HTTP-aanvraagprotocol

Het HTTP-aanvraagprotocol staat willekeurige HTTP-aanvragen toe, behalve protocol-upgrades.
HTTP-aanvragen worden gericht op het reguliere runtime-adres van de entiteit, zonder de $hc-infix die wordt gebruikt voor websocketclients voor hybride verbindingen.

```
https://{namespace-address}/{path}?sbc-hc-token=...
```

Het _naamruimteadres_ is de volledig gekwalificeerde domeinnaam van de azure relay-naamruimte die `{myname}.servicebus.windows.net`de hybride verbinding host, meestal van het formulier .

De aanvraag kan willekeurige extra HTTP-headers bevatten, inclusief door toepassingen gedefinieerde headers. Alle meegeleverde headers, behalve die rechtstreeks gedefinieerd in RFC7230 (zie [verzoek bericht)](#Request message)stromen naar de luisteraar en zijn te vinden op het `requestHeader` object van het **verzoekbericht.**

De parameteropties voor querytekenreeksen zijn als volgt:

| Param          | Vereist? | Beschrijving
| -------------- | --------- | ---------------- |
| `sb-hc-token`  | Ja\*     | De listener moet een geldig, URL-gecodeerd Service Bus Shared Access Token opgeven voor de naamruimte of hybride verbinding die het **recht verzenden** verleent.

Het token kan ook worden `ServiceBusAuthorization` `Authorization` uitgevoerd in de of HTTP-header. Het token kan worden weggelaten als de hybride verbinding is geconfigureerd om anonieme aanvragen mogelijk te maken.

Omdat de service effectief fungeert als een proxy, zelfs als niet `Via` als een echte HTTP-proxy, voegt het een header toe of annoteert de bestaande `Via` header die voldoet aan [RFC7230, sectie 5.7.1](https://tools.ietf.org/html/rfc7230#section-5.7.1).
De service voegt de hostnaam `Via`relay toe aan .

| Code | Bericht  | Beschrijving                    |
| ---- | -------- | ------------------------------ |
| 200  | OK       | De aanvraag is afgehandeld door ten minste één luisteraar.  |
| 202  | Geaccepteerd | Het verzoek is geaccepteerd door ten minste één luisteraar. |

Als er een fout optreedt, kan de service als volgt antwoorden. Of het antwoord afkomstig is van de service of van `Via` de luisteraar kan worden geïdentificeerd door de aanwezigheid van de header. Als de koptekst aanwezig is, is het antwoord van de listener.

| Code | Fout           | Beschrijving
| ---- | --------------- |--------- |
| 404  | Niet gevonden       | Het pad Hybride verbinding is ongeldig of de basis-URL is vervormd.
| 401  | Niet geautoriseerd    | Het beveiligingstoken ontbreekt, misvormd of ongeldig.
| 403  | Verboden       | Het beveiligingstoken is niet geldig voor dit pad en voor deze actie.
| 500  | Interne fout  | Er ging iets mis in de dienst.
| 503  | Slechte gateway     | Het verzoek kan niet worden doorgestuurd naar een luisteraar.
| 504  | Gateway-time-out | Het verzoek werd doorgestuurd naar een luisteraar, maar de luisteraar heeft de ontvangst niet in de vereiste tijd erkend.

## <a name="next-steps"></a>Volgende stappen

* [Veelgestelde vragen over Relay](relay-faq.md)
* [Een naamruimte maken](relay-create-namespace-portal.md)
* [Aan de slag met .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Aan de slag met knooppunten](relay-hybrid-connections-node-get-started.md)
