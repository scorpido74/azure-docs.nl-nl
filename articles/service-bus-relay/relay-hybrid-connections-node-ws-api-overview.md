---
title: Overzicht van de AZURE Relay Node API's | Microsoft Documenten
description: In dit artikel vindt u een overzicht van de NOde.js API voor de Azure Relay-service. Het laat ook zien hoe het hyco-ws Node pakket te gebruiken.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: b7d6e822-7c32-4cb5-a4b8-df7d009bdc85
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 2877284c419da4999e23490fc986e5da44e5d92e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514507"
---
# <a name="relay-hybrid-connections-node-api-overview"></a>Overzicht van Api voor knooppunt verbinding tussen relayen

## <a name="overview"></a>Overzicht

Het [`hyco-ws`](https://www.npmjs.com/package/hyco-ws) Node-pakket voor Azure Relay Hybrid Connections is gebouwd op en breidt het ['ws'](https://www.npmjs.com/package/ws) NPM-pakket uit. Met dit pakket wordt alle exporten van dat basispakket opnieuw geëxporteerd en worden nieuwe exporten toegevoegd die integratie mogelijk maken met de functie Hybride verbindingen van azure relayservice. 

Bestaande toepassingen `require('ws')` die dit `require('hyco-ws')` pakket kunnen gebruiken met in plaats daarvan, die ook hybride scenario's mogelijk maakt waarin een toepassing lokaal kan luisteren naar WebSocket-verbindingen vanuit "binnen de firewall" en via hybride verbindingen, allemaal tegelijkertijd.
  
## <a name="documentation"></a>Documentatie

De API's zijn [gedocumenteerd in het belangrijkste ws-pakket](https://github.com/websockets/ws/blob/master/doc/ws.md). In dit artikel wordt beschreven hoe dit pakket verschilt van die basislijn. 

De belangrijkste verschillen tussen het basispakket en deze 'hyco-ws' is dat `require('hyco-ws').RelayedServer`het een nieuwe serverklasse toevoegt, geëxporteerd via , en een paar helpermethoden.

### <a name="package-helper-methods"></a>Pakkethelpermethoden

Er zijn verschillende hulpprogrammamethoden beschikbaar op de pakketexport waaru als volgt naar verwijzen:

```JavaScript
const WebSocket = require('hyco-ws');

var listenUri = WebSocket.createRelayListenUri('namespace.servicebus.windows.net', 'path');
listenUri = WebSocket.appendRelayToken(listenUri, 'ruleName', '...key...')
...

```

De helpermethoden zijn voor gebruik met dit pakket, maar kunnen ook worden gebruikt door een node-server om web- of apparaatclients in te schakelen om listeners of afzenders te maken. De server gebruikt deze methoden door ze URI's te geven die kortstondige tokens insluiten. Deze URI's kunnen ook worden gebruikt met algemene WebSocket-stacks die geen ondersteuning bieden voor het instellen van HTTP-headers voor de WebSocket-handshake. Het insluiten van autorisatietokens in de URI wordt voornamelijk ondersteund voor die scenario's voor het gebruik van bibliotheek-extern gebruik. 

#### <a name="createrelaylistenuri"></a>createRelayListenUri

```JavaScript
var uri = createRelayListenUri([namespaceName], [path], [[token]], [[id]])
```

Hiermee maakt u een geldige Azure Relay Hybrid Connection-listener URI voor de opgegeven naamruimte en pad. Deze URI kan vervolgens worden gebruikt met de relay-versie van de klasse WebSocketServer.

- `namespaceName`(vereist) - de domeingekwalificeerde naam van de Azure Relay-naamruimte die moet worden gebruikt.
- `path`(vereist) - de naam van een bestaande Azure Relay Hybrid Connection in die naamruimte.
- `token`(optioneel) - een eerder uitgegeven relay-toegangstoken dat is ingesloten in de listener-URI (zie het volgende voorbeeld).
- `id`(optioneel) - een tracking-id waarmee end-to-end diagnostische gegevens van aanvragen kunnen worden bijgehouden.

De `token` waarde is optioneel en mag alleen worden gebruikt wanneer het niet mogelijk is om HTTP-headers samen met de WebSocket-handdruk te verzenden, zoals het geval is met de W3C WebSocket-stack.                  


#### <a name="createrelaysenduri"></a>relaySenduri maken
 
```JavaScript
var uri = createRelaySendUri([namespaceName], [path], [[token]], [[id]])
```

Hiermee maakt u een geldige Azure Relay Hybrid Connection send URI voor de opgegeven naamruimte en pad. Deze URI kan worden gebruikt met elke WebSocket-client.

- `namespaceName`(vereist) - de domeingekwalificeerde naam van de Azure Relay-naamruimte die moet worden gebruikt.
- `path`(vereist) - de naam van een bestaande Azure Relay Hybrid Connection in die naamruimte.
- `token`(optioneel) - een eerder uitgegeven relaytoegangstoken dat is ingesloten in de send URI (zie het volgende voorbeeld).
- `id`(optioneel) - een tracking-id waarmee end-to-end diagnostische gegevens van aanvragen kunnen worden bijgehouden.

De `token` waarde is optioneel en mag alleen worden gebruikt wanneer het niet mogelijk is om HTTP-headers samen met de WebSocket-handdruk te verzenden, zoals het geval is met de W3C WebSocket-stack.                   


#### <a name="createrelaytoken"></a>MakenRelayToken 

```JavaScript
var token = createRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Hiermee maakt u een SAS-token (Azure Relay Shared Access Signature) voor de opgegeven doel-URI-, SAS-regel- en SAS-regelsleutel die geldig is voor het opgegeven aantal seconden of gedurende een uur vanaf het huidige moment als het argument voor het vervallen wordt weggelaten.

- `uri`(verplicht) - de URI waarvoor het token moet worden uitgegeven. De URI wordt genormaliseerd om het HTTP-schema te gebruiken en querytekenreeksgegevens worden verwijderd.
- `ruleName`(vereist) - SAS-regelnaam voor de entiteit die wordt vertegenwoordigd door de gegeven URI, of voor de naamruimte die wordt vertegenwoordigd door het URI-hostgedeelte.
- `key`(vereist) - geldige sleutel voor de SAS-regel. 
- `expirationSeconds`(optioneel) - het aantal seconden totdat het gegenereerde token moet verlopen. Als dit niet is opgegeven, is de standaardinstelling 1 uur (3600).

Het uitgegeven token verleent de rechten die zijn gekoppeld aan de opgegeven SAS-regel voor de gegeven duur.

#### <a name="appendrelaytoken"></a>aanhangselRelayToken

```JavaScript
var uri = appendRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Deze methode is functioneel `createRelayToken` gelijkwaardig aan de eerder gedocumenteerde methode, maar retourneert het token correct toegevoegd aan de invoer URI.

### <a name="class-wsrelayedserver"></a>Klasse ws. RelayedServer

De `hycows.RelayedServer` klasse is een `ws.Server` alternatief voor de klasse die niet luistert op het lokale netwerk, maar afgevaardigden die luisteren naar de Azure Relay-service.

De twee klassen zijn meestal contract compatibel, wat `ws.Server` betekent dat een bestaande toepassing met behulp van de klasse gemakkelijk kan worden gewijzigd om de doorgegeven versie te gebruiken. De belangrijkste verschillen zitten in de constructeur en in de beschikbare opties.

#### <a name="constructor"></a>Constructor  

```JavaScript 
var ws = require('hyco-ws');
var server = ws.RelayedServer;

var wss = new server(
    {
        server: ws.createRelayListenUri(ns, path),
        token: function() { return ws.createRelayToken('http://' + ns, keyrule, key); }
    });
```

De `RelayedServer` constructor ondersteunt een andere `Server`set argumenten dan de , omdat het niet een standalone listener, of in staat om te worden ingebed in een bestaande HTTP listener framework. Er zijn ook minder opties beschikbaar omdat het WebSocket-beheer grotendeels is gedelegeerd aan de Relay-service.

Constructeurargumenten:

- `server`(vereist) - de volledig gekwalificeerde URI voor een hybride verbindingsnaam waarop u luisteren, meestal gebouwd met de Helpermethode WebSocket.createRelayListenUri().
- `token`(vereist) - dit argument bevat een eerder uitgegeven tokentekenreeks of een callback-functie die kan worden aangeroepen om een dergelijke tokentekenreeks te verkrijgen. De callback-optie heeft de voorkeur, omdat het token-verlenging mogelijk maakt.

#### <a name="events"></a>Gebeurtenissen

`RelayedServer`instanties zenden drie gebeurtenissen uit waarmee u binnenkomende aanvragen afhandelen, verbindingen maken en foutvoorwaarden detecteren. Je moet je `connect` abonneren op het evenement om berichten te verwerken. 

##### <a name="headers"></a>Headers

```JavaScript 
function(headers)
```

De `headers` gebeurtenis wordt verhoogd vlak voordat een binnenkomende verbinding wordt geaccepteerd, waardoor de headers naar de client kunnen worden verzonden. 

##### <a name="connection"></a>verbinding

```JavaScript
function(socket)
```

Wordt uitgestoten wanneer een nieuwe WebSocket-verbinding wordt geaccepteerd. Het object is `ws.WebSocket`van het type, hetzelfde als bij het basispakket.


##### <a name="error"></a>error

```JavaScript
function(error)
```

Als de onderliggende server een fout uitzendt, wordt deze hier doorgestuurd.  

#### <a name="helpers"></a>Helpers

Om het starten van een doorgegeven server te vereenvoudigen en onmiddellijk een abonnement te nemen op binnenkomende verbindingen, stelt het pakket een eenvoudige helperfunctie bloot, die ook in de voorbeelden wordt gebruikt, als volgt:

##### <a name="createrelayedlistener"></a>RelayedListener maken

```JavaScript
var WebSocket = require('hyco-ws');

var wss = WebSocket.createRelayedServer(
    {
        server: WebSocket.createRelayListenUri(ns, path),
        token: function() { return WebSocket.createRelayToken('http://' + ns, keyrule, key); }
    }, 
    function (ws) {
        console.log('connection accepted');
        ws.onmessage = function (event) {
            console.log(JSON.parse(event.data));
        };
        ws.on('close', function () {
            console.log('connection closed');
        });       
});
``` 

##### <a name="createrelayedserver"></a>RelayedServer maken

```javascript
var server = createRelayedServer([options], [connectCallback] )
```

Met deze methode wordt de constructor aanroepen om een nieuw exemplaar van de RelayedServer te maken en wordt vervolgens de meegeleverde callback geabonneerd op de gebeurtenis 'verbinding'.
 
##### <a name="relayedconnect"></a>relayedConnect

Gewoon spiegelen `createRelayedServer` van de `relayedConnect` helper in functie, creëert een client verbinding en abonneert zich op de 'open' gebeurtenis op de resulterende socket.

```JavaScript
var uri = WebSocket.createRelaySendUri(ns, path);
WebSocket.relayedConnect(
    uri,
    WebSocket.createRelayToken(uri, keyrule, key),
    function (socket) {
        ...
    }
);
```

## <a name="next-steps"></a>Volgende stappen
Ga voor meer informatie over Azure Relay naar de volgende koppelingen:
* [Wat is Azure Relay?](relay-what-is-it.md)
* [Beschikbare Relay-API's](relay-api-overview.md)
