---
title: Bing Speech WebSocket-Protocol | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Protocol documentatie voor Bing Speech op basis van websockets
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e7f51d49624d5019bec058a2d12f6ca2f1366938
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966888"
---
# <a name="bing-speech-websocket-protocol"></a>Bing Speech WebSocket-Protocol

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Bing Speech is een op de cloud gebaseerd platform dat de meest geavanceerde algoritmen bevat die beschikbaar zijn voor het converteren van gesp roken audio naar tekst. Het Bing Speech-protocol definieert de [Verbindings instellingen](#connection-establishment) tussen client toepassingen en de service en de spraak herkennings berichten die worden uitgewisseld tussen de tegen Hangers (door de[client afkomstige berichten](#client-originated-messages) en door de [service afkomstige berichten](#service-originated-messages) ) ). Daarnaast worden [telemetrie-berichten](#telemetry-schema) en [fout afhandeling](#error-handling) beschreven.

## <a name="connection-establishment"></a>Verbinding tot stand brengen

Het Speech-Service Protocol volgt de WebSocket Standard Specification [IETF RFC 6455](https://tools.ietf.org/html/rfc6455). Een WebSocket-verbinding wordt gestart als een HTTP-aanvraag die HTTP-headers bevat die aangeven dat de client de verbinding met een WebSocket moet upgraden in plaats van HTTP-semantiek te gebruiken. De server geeft aan dat het gaat om deel te nemen aan de WebSocket- `101 Switching Protocols` verbinding door een HTTP-antwoord te retour neren. Na de uitwisseling van deze Handshake behoudt de-client en-service de socket open en begint met het gebruik van een op berichten gebaseerd protocol voor het verzenden en ontvangen van informatie.

De client toepassing stuurt een HTTPS GET-aanvraag naar de service om de WebSocket-Handshake te starten. Het bevat standaard WebSocket-upgrade headers samen met andere headers die specifiek zijn voor spraak.

```HTTP
GET /speech/recognition/interactive/cognitiveservices/v1 HTTP/1.1
Host: speech.platform.bing.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
Sec-WebSocket-Version: 13
Authorization: t=EwCIAgALBAAUWkziSCJKS1VkhugDegv7L0eAAJqBYKKTzpPZOeGk7RfZmdBhYY28jl&p=
X-ConnectionId: A140CAF92F71469FA41C72C7B5849253
Origin: https://speech.platform.bing.com
```

De service reageert met:

```HTTP
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: upgrade
Sec-WebSocket-Key: 2PTTXbeeBXlrrUNsY15n01d/Pcc=
Set-Cookie: SpeechServiceToken=AAAAABAAWTC8ncb8COL; expires=Wed, 17 Aug 2016 15:39:06 GMT; domain=bing.com; path="/"
Date: Wed, 17 Aug 2016 15:03:52 GMT
```

Voor alle spraak aanvragen is [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security) -versleuteling vereist. Het gebruik van niet-versleutelde spraak aanvragen wordt niet ondersteund. De volgende TLS-versie wordt ondersteund:

* TLS 1.2

### <a name="connection-identifier"></a>Verbindings-id

Voor de spraak service is vereist dat alle clients een unieke ID bevatten om de verbinding te identificeren. Clients *moeten* de *X-ConnectionId* -header bevatten wanneer ze een WebSocket-Handshake starten. De *X-ConnectionId* -header moet een UUID-waarde ( [Universally Unique Identifier](https://en.wikipedia.org/wiki/Universally_unique_identifier) ) zijn. WebSocket-upgrade aanvragen die geen *x-ConnectionId*bevatten, geven geen waarde op voor de *x-ConnectionId* -header of bevatten geen geldige UUID-waarde die door de service wordt genegeerd met een http- `400 Bad Request` antwoord.

### <a name="authorization"></a>Authorization

In aanvulling op de standaard-WebSocket-Handshake-headers vereist spraak aanvragen een *autorisatie* -header. Verbindings aanvragen zonder deze header worden door de service afgewezen met een http `403 Forbidden` -antwoord.

De *autorisatie* -header moet een JSON Web token (JWT)-toegangs token bevatten.

Voor informatie over het abonneren en verkrijgen van API-sleutels die worden gebruikt om geldige JWT-toegangs tokens op te halen, raadpleegt u de pagina [Cognitive Services abonnement](https://azure.microsoft.com/try/cognitive-services/) .

De API-sleutel wordt door gegeven aan de token service. Bijvoorbeeld:

``` HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

De volgende header-informatie is vereist voor toegang tot tokens.

| Name | Indeling | Description |
|----|----|----|
| OCP-Apim-Subscription-Key | ASCII | Uw abonnementssleutel |

De token service retourneert het JWT-toegangs token `text/plain`als. Vervolgens wordt de JWT als een aan `Base64 access_token` de handshake door gegeven als een *autorisatie* header die wordt voorafgegaan door `Bearer`de teken reeks. Bijvoorbeeld:

`Authorization: Bearer [Base64 access_token]`

### <a name="cookies"></a>Cookies

Clients *moeten* HTTP-cookies ondersteunen, zoals opgegeven in [RFC 6265](https://tools.ietf.org/html/rfc6265).

### <a name="http-redirection"></a>HTTP-omleiding

Clients *moeten* ondersteuning bieden voor de standaard-omleidings mechanismen die zijn opgegeven door de specificatie van het [http-protocol](https://www.w3.org/Protocols/rfc2616/rfc2616.html).

### <a name="speech-endpoints"></a>Spraak-eind punten

Clients *moeten* een geschikt eind punt van de speech-service gebruiken. Het eind punt is gebaseerd op de herkennings modus en taal. In de tabel ziet u enkele voor beelden.

| Modus | Path | Service-URI |
| -----|-----|-----|
| Interactief | /speech/recognition/interactive/cognitiveservices/v1 | https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=pt-BR |
| Gesprek | /speech/recognition/conversation/cognitiveservices/v1 | https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US |
| Dicteren | /speech/recognition/dictation/cognitiveservices/v1 | https://speech.platform.bing.com/speech/recognition/dictation/cognitiveservices/v1?language=fr-FR |

Zie de [service-URI](../GetStarted/GetStartedREST.md#service-uri) -pagina voor meer informatie.

### <a name="report-connection-problems"></a>Verbindings problemen rapporteren

Clients moeten onmiddellijk alle problemen rapporteren die zijn opgetreden tijdens het maken van een verbinding. Het bericht protocol voor het rapporteren van mislukte verbindingen wordt beschreven in [telemetrie van verbindings fouten](#connection-failure-telemetry).

### <a name="connection-duration-limitations"></a>Beperkingen voor de verbindings duur

In vergelijking met typische HTTP-verbindingen van de webservice, de WebSocket-verbindingen voor het laatst *lang* . Met de spraak service worden beperkingen voor de duur van de WebSocket-verbindingen met de service geplaatst:

 * De maximale duur voor een actieve WebSocket-verbinding is 10 minuten. Er is een verbinding actief als de service of de client WebSocket-berichten via die verbinding verzendt. De service beëindigt de verbinding zonder waarschuwing wanneer de limiet is bereikt. Clients moeten gebruikers scenario's ontwikkelen die de verbinding niet nodig hebben om actief te blijven op of in de buurt van de maximale verbindings duur.

 * De maximale duur voor een inactieve WebSocket-verbinding is 180 seconden. Een verbinding is inactief als de service of de client geen WebSocket-bericht via de verbinding heeft verzonden. Nadat de maximale inactieve levens duur is bereikt, wordt de inactieve WebSocket-verbinding door de service beëindigd.

## <a name="message-types"></a>Bericht typen

Nadat een WebSocket-verbinding tot stand is gebracht tussen de client en de service, kan de client en de service berichten verzenden. In deze sectie wordt de indeling van deze WebSocket-berichten beschreven.

[IETF RFC 6455](https://tools.ietf.org/html/rfc6455) geeft aan dat de WebSocket-berichten gegevens kunnen verzenden met behulp van een tekst of binaire code ring. De twee code ringen gebruiken verschillende on-the-Wire-indelingen. Elke indeling is geoptimaliseerd voor efficiënte code ring, verzen ding en decodering van de bericht lading.

### <a name="text-websocket-messages"></a>Text-WebSocket-berichten

Tekst-WebSocket-berichten bevatten een payload van tekstuele informatie die bestaat uit een sectie met kopteksten en een hoofd tekst gescheiden door de vertrouwde combi natie van het teken voor dubbele regel terugloop die wordt gebruikt voor HTTP-berichten. Net als HTTP-berichten geven text-socket-berichten kopteksten op *naam: waarde* -indeling gescheiden door een combi natie van een nieuwe regel met één regel terugloop. Alle tekst in een text-WebSocket- bericht moet [UTF-8-](https://tools.ietf.org/html/rfc3629) code ring gebruiken.

Text-WebSocket-berichten moeten een pad naar een bericht opgeven in het *pad*van de header. De waarde van deze header moet een van de spraak protocol bericht typen die verderop in dit document zijn gedefinieerd.

### <a name="binary-websocket-messages"></a>Binaire WebSocket-berichten

Binaire WebSocket-berichten bevatten een binaire nettolading. In het Speech-Service Protocol wordt audio verzonden naar en ontvangen van de service met behulp van binaire WebSocket-berichten. Alle andere berichten zijn text-WebSocket-berichten.

Net als tekst-WebSocket-berichten bestaan binaire WebSocket-berichten uit een sectie met koptekst en hoofd tekst. De eerste 2 bytes van het binaire bericht van de WebSocket opgeven, in de volg orde [big endian](https://en.wikipedia.org/wiki/Endianness) , de 16-bits integer-grootte van de koptekst sectie. De minimale sectie grootte van de header is 0 bytes. De maximale grootte is 8.192 bytes. De tekst in de headers van binaire WebSocket- berichten moet [US-ASCII-](https://tools.ietf.org/html/rfc20) code ring gebruiken.

Headers in een binaire WebSocket-bericht worden gecodeerd in dezelfde indeling als in text-WebSocket-berichten. De indeling *naam: waarde* wordt gescheiden door een combi natie van een nieuwe regel met één regel terugloop. Voor binaire WebSocket-berichten moet een pad naar een bericht worden opgegeven in het *pad*van de header. De waarde van deze header moet een van de spraak protocol bericht typen die verderop in dit document zijn gedefinieerd.

Zowel tekst als binaire WebSocket-berichten worden gebruikt in het Speech-Service Protocol.

## <a name="client-originated-messages"></a>Berichten die afkomstig zijn van de client

Nadat de verbinding tot stand is gebracht, kunnen de client en de service beginnen met het verzenden van berichten. In deze sectie worden de indeling en Payload beschreven van berichten die door client toepassingen naar de speech-service worden verzonden. De sectie [service-afkomstige berichten](#service-originated-messages) geeft de berichten weer die afkomstig zijn van de spraak service en worden verzonden naar de client toepassingen.

De belangrijkste berichten die door de client naar de services worden `speech.config`verzonden `audio`, zijn `telemetry` , en berichten. Voordat we elk bericht in detail beschouwen, worden de algemene vereiste bericht koppen voor al deze berichten beschreven.

### <a name="required-message-headers"></a>Vereiste bericht headers

De volgende headers zijn vereist voor alle door de client afkomstige berichten.

| Header | Value |
|----|----|
| Path | Het pad naar het bericht zoals opgegeven in dit document |
| X-RequestId | UUID in indeling zonder streepje |
| X-Time Stamp | Tijds tempel van UTC-klok tijd van client in ISO 8601-indeling |

#### <a name="x-requestid-header"></a>X-aanvraag-header

Aanvragen die afkomstig zijn van de client, worden uniek geïdentificeerd door de bericht header *X-aanvraag-* id. Deze header is vereist voor alle door de client afkomstige berichten. De waarde van de *X-aanvraag-* header moet een uuid in het formulier zonder streepje zijn, bijvoorbeeld *123e4567e89b12d3a456426655440000*. Het *mag niet* in de canonieke vorm *123e4567-e89b-12d3-A456-426655440000*zijn. Aanvragen zonder een *X-aanvraag-* header of met een header-waarde die de onjuiste indeling voor uuid gebruikt, zorgt ervoor dat de service de WebSocket-verbinding beëindigt.

#### <a name="x-timestamp-header"></a>X-Time Stamp-kop

Elk bericht dat wordt verzonden naar de spraak service door een client toepassing *moet* een *X-Time Stamp-* kop bevatten. De waarde voor deze header is het tijdstip waarop de client het bericht verzendt. Aanvragen zonder een *X-Time Stamp-* kop of met een header waarde die de onjuiste indeling gebruikt, zorgen ervoor dat de service de WebSocket-verbinding kan beëindigen.

De waarde van de *X-Time Stamp* -header moet de vorm ' YYYY'-'MM'-'dd'T'HH ': ' mm ': ' ss ' zijn. fffffffZ ' waarbij ' fffffff ' een fractie van een seconde is. Bijvoorbeeld: ' 12,5 ' betekent ' 12 + 5/10 seconden ' en ' 12,526 ' betekent ' 12 plus 526/1000 seconden '. Deze indeling voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en, in tegens telling tot de standaard HTTP *date* -kop, kan de resolutie van milliseconden opleveren. Client toepassingen kunnen tijds tempels afronden op de dichtstbijzijnde milliseconde. Client toepassingen moeten ervoor zorgen dat de klok van het apparaat nauw keurig wordt getraceerd door gebruik te maken van een [NTP-server (Network Time Protocol)](https://en.wikipedia.org/wiki/Network_Time_Protocol).

### <a name="message-speechconfig"></a>Bericht`speech.config`

De spraak service moet weten wat de kenmerken van uw toepassing zijn om de best mogelijke spraak herkenning mogelijk te maken. De vereiste kenmerken gegevens bevatten informatie over het apparaat en het besturings systeem dat uw toepassing aanstuurt. U geeft deze informatie op in `speech.config` het bericht.

Clients *moeten* onmiddellijk een `speech.config` bericht verzenden nadat ze de verbinding hebben gemaakt met de spraak service en `audio` voordat ze berichten verzenden. U hoeft slechts één keer `speech.config` per verbinding een bericht te verzenden.

| Veld | Description |
|----|----|
| WebSocket-bericht codering | Text |
| Hoofdtekst | De payload als een JSON-structuur |

#### <a name="required-message-headers"></a>Vereiste bericht headers

| Headernaam | Value |
|----|----|
| Path | `speech.config` |
| X-Time Stamp | Tijds tempel van UTC-klok tijd van client in ISO 8601-indeling |
| Content-Type | application/json; charset=utf-8 |

Net als bij alle client-afkomstige berichten in het Speech-Service protocol `speech.config` *moet* het bericht een *X-Time Stamp-* header bevatten die de klok tijd van de client vastlegt wanneer het bericht is verzonden naar de service. Het `speech.config` bericht vereist *geen* *X-aanvraag-* header omdat dit bericht niet aan een bepaalde spraak aanvraag is gekoppeld.

#### <a name="message-payload"></a>Bericht lading
De payload van het `speech.config` bericht is een JSON-structuur die informatie over de toepassing bevat. In het volgende voor beeld wordt deze informatie weer gegeven. Informatie over de context van de client en het apparaat is opgenomen in het *context* element van de JSON-structuur.

```JSON
{
  "context": {
    "system": {
      "version": "2.0.12341",
    },
    "os": {
      "platform": "Linux",
      "name": "Debian",
      "version": "2.14324324"
    },
    "device": {
      "manufacturer": "Contoso",
      "model": "Fabrikan",
      "version": "7.341"
      }
    },
  }
}
```

##### <a name="system-element"></a>Systeem element

Het element System. version van het `speech.config` bericht bevat de versie van de Speech SDK-software die wordt gebruikt door de client toepassing of het apparaat. De waarde heeft de indeling *Major. minor. build. Branch*. U kunt het *vertakkings* onderdeel weglaten als het niet van toepassing is.

##### <a name="os-element"></a>BESTURINGSSYSTEEM element

| Veld | Description | Gebruik |
|-|-|-|
| os.platform | Het OS-platform dat als host fungeert voor de toepassing, bijvoorbeeld Windows, Android, iOS of Linux |Vereist |
| os.name | De product naam van het besturings systeem, bijvoorbeeld Debian of Windows 10 | Vereist |
| os.version | De versie van het besturings systeem in de vorm *primair. secundair. build. Branch* | Vereist |

##### <a name="device-element"></a>Element apparaat

| Veld | Description | Gebruik |
|-|-|-|
| device.manufacturer | De hardwarefabrikant van het apparaat | Vereist |
| device.model | Het model van het apparaat | Vereist |
| device.version | De software versie van het apparaat die is geleverd door de fabrikant van het apparaat. Met deze waarde wordt een versie van het apparaat opgegeven dat door de fabrikant kan worden gevolgd. | Vereist |

### <a name="message-audio"></a>Bericht`audio`

Spraak ingeschakelde client toepassingen verzenden audio naar de speech-service door de audio stroom te converteren naar een reeks audio fragmenten. Elk audio segment bevat een segment van de gesp roken audio dat door de service wordt getranscribeerd. De maximale grootte van één audio segment is 8.192 bytes. Audio stroom berichten zijn *binaire WebSocket-berichten*.

Clients gebruiken het `audio` bericht om een audio segment naar de service te verzenden. Clients lezen audio van de microfoon in segmenten en verzenden deze segmenten naar de speech-service voor transcriptie. Het eerste `audio` bericht moet een juist opgemaakte header bevatten die aangeeft dat de audio voldoet aan een van de coderings indelingen die worden ondersteund door de service. Extra `audio` berichten bevatten alleen de binaire audio gegevensstroom gegevens die van de microfoon zijn gelezen.

Clients kunnen eventueel een `audio` bericht verzenden met een hoofd tekst met een lengte van nul. Dit bericht geeft aan de service dat de client weet dat de gebruiker is gestopt met spreken, het utterance is voltooid en de microfoon is uitgeschakeld.

De speech-service gebruikt `audio` het eerste bericht dat een unieke aanvraag-id bevat om aan te geven dat het begin van een nieuwe aanvraag/antwoord cyclus of *Turn*wordt gestart. Nadat de service een `audio` bericht met een nieuwe aanvraag-id heeft ontvangen, worden alle in de wachtrij geplaatste of niet-verzonden berichten die zijn gekoppeld aan een vorige zet, verwijderd.

| Veld | Description |
|-------------|----------------|
| WebSocket-bericht codering | Binary |
| Hoofdtekst | De binaire gegevens voor het audio segment. De maximale grootte is 8.192 bytes. |

#### <a name="required-message-headers"></a>Vereiste bericht headers

De volgende headers zijn vereist voor alle `audio` berichten.

| Header         |  Value     |
| ------------- | ---------------- |
| Path | `audio` |
| X-RequestId | UUID in indeling zonder streepje |
| X-Time Stamp | Tijds tempel van UTC-klok tijd van client in ISO 8601-indeling |
| Content-Type | Het type audio-inhoud. Het type moet *Audio/x-WAV* (PCM) of *Audio/zijde* (zijde) zijn. |

#### <a name="supported-audio-encodings"></a>Ondersteunde audio codering

In deze sectie worden de audiocodecs beschreven die worden ondersteund door de spraak service.

##### <a name="pcm"></a>PCM

De spraak service accepteert niet-gecomprimeerde Pulse Code-modulatie (PCM)-audio. Audio wordt in [WAV](https://en.wikipedia.org/wiki/WAV) -indeling naar de service verzonden, dus het eerste audio segment *moet* een geldige RIFF-header ( [Resource Interchange File Format](https://en.wikipedia.org/wiki/Resource_Interchange_File_Format) ) bevatten. Als een client een turn initieert met een audio segment dat *geen* geldige RIFF-kop bevat, weigert de service de aanvraag en beëindigt de WebSocket-verbinding.

PCM-audio *moet* worden bemonsterd met 16 kHz en 16 bits per sample en één kanaal (*RIFF-16khz-16-mono-PCM*). De speech-service biedt geen ondersteuning voor audio gegevensstromen in stereo en wijst audio stromen af die geen gebruik maken van de opgegeven bitsnelheid, sample frequentie of het aantal kanalen.

##### <a name="opus"></a>Opus

Opus is een open, Royalty gratis, uiterst veelzijdige audiocodec van audio. Speech Service ondersteunt Opus met een constante bitsnelheid van `32000` of. `16000` Alleen de `OGG` container voor Opus wordt momenteel ondersteund en wordt opgegeven door het `audio/ogg` MIME-type.

Als u Opus wilt gebruiken, wijzigt u het [Java script](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/master/samples/browser/Sample.html#L101) -voor beeld en wijzigt u de `RecognizerSetup` methode om te retour neren.

```javascript
return SDK.CreateRecognizerWithCustomAudioSource(
            recognizerConfig,
            authentication,
            new SDK.MicAudioSource(
                     new SDK.OpusRecorder(
                     {
                         mimeType: "audio/ogg",
                         bitsPerSecond: 32000
                     }
              )
          ));
```

#### <a name="detect-end-of-speech"></a>Einde van spraak detectie

Mensen geven geen expliciete Signa lering wanneer ze klaar zijn met spreken. Elke toepassing die spraak accepteert als invoer, heeft twee opties voor het afhandelen van het einde van de spraak in een audio stroom: end-of-speech-detectie en client-end-of-speech-detectie. Van deze twee keuzes biedt service-end-of-speech-detectie meestal een betere gebruikers ervaring.

##### <a name="service-end-of-speech-detection"></a>End-of-speech-detectie service

Om de ideale hand-free spraak ervaring te bouwen, kunnen de services door toepassingen detecteren wanneer de gebruiker klaar is met spreken. Clients verzenden audio van de microfoon als *Audio* fragmenten totdat de service stilte detecteert en terugkeert met `speech.endDetected` een bericht.

##### <a name="client-end-of-speech-detection"></a>Client end-of-speech-detectie

Client toepassingen die de gebruiker in staat stellen het einde van de spraak op een of andere manier te Signa leren, kunnen ook de service die signaal geeft. Een client toepassing kan bijvoorbeeld een knop ' stoppen ' of ' dempen ' hebben waarop de gebruiker kan drukken. Om einde van spraak te Signa leren, verzenden client toepassingen een bericht met een *Audio* segment met een hoofd tekst met een lengte van nul. De speech-service interpreteert dit bericht als het einde van de inkomende audio stroom.

### <a name="message-telemetry"></a>Bericht`telemetry`

Client toepassingen *moeten* het einde van elke beurt bevestigen door telemetrie over de turn to speech-service te verzenden. Met de functie voor het inschakelen van de eind bevestiging kan de spraakherkennings service ervoor zorgen dat alle berichten die nodig zijn voor het volt ooien van de aanvraag en het antwoord op de juiste wijze zijn ontvangen door de client. Met de functie voor het inschakelen van de eind bevestiging kan de speech-service ook controleren of de client toepassingen worden uitgevoerd zoals verwacht. Deze informatie is niet waardevol als u hulp nodig hebt bij het oplossen van problemen met spraak toepassingen.

Clients moeten het einde van een turn bevestigen door een `telemetry` bericht te verzenden zodra hij een `turn.end` bericht heeft ontvangen. Clients moeten zo snel mogelijk proberen `turn.end` om dit te bevestigen. Als een client toepassing het inschakelen van het einde niet kan bevestigen, kan de spraak service de verbinding met een fout beëindigen. Clients moeten slechts één `telemetry` bericht verzenden voor elke aanvraag en reactie die wordt geïdentificeerd door de *X-aanvraag-* id.

| Veld | Description |
| ------------- | ---------------- |
| WebSocket-bericht codering | Text |
| Path | `telemetry` |
| X-Time Stamp | Tijds tempel van UTC-klok tijd van client in ISO 8601-indeling |
| Content-Type | `application/json` |
| Hoofdtekst | Een JSON-structuur die client informatie over de beurt bevat |

Het schema voor de hoofd tekst van `telemetry` het bericht wordt gedefinieerd in de sectie [telemetrie-schema](#telemetry-schema) .

#### <a name="telemetry-for-interrupted-connections"></a>Telemetrie voor onderbroken verbindingen

Als de netwerk verbinding om een of andere reden niet kan worden uitgevoerd en de *client geen* `turn.end` bericht van de service ontvangt, verzendt de client een `telemetry` bericht. In dit bericht wordt de mislukte aanvraag beschreven wanneer de client de volgende keer verbinding maakt met de service. Clients hoeven niet onmiddellijk een verbinding te proberen om het `telemetry` bericht te verzenden. Het bericht kan worden gebufferd op de client en via een toekomstig door de gebruiker aangevraagde verbinding worden verzonden. Het `telemetry` bericht voor de mislukte aanvraag *moet* de *X-aanvraag-* waarde van de mislukte aanvraag gebruiken. Het kan worden verzonden naar de service zodra er een verbinding tot stand is gebracht, zonder te wachten op het verzenden of ontvangen van andere berichten.

## <a name="service-originated-messages"></a>Berichten van de service

In deze sectie worden de berichten beschreven die afkomstig zijn van de spraak service en worden verzonden naar de client. Speech Service onderhoudt een REGI ster van client mogelijkheden en genereert de berichten die zijn vereist voor elke client, zodat niet alle clients alle berichten ontvangen die hier worden beschreven. Voor het boogere wordt verwezen naar berichten met de waarde van de *pad* -header. Zo verwijzen we naar een WebSocket-tekst bericht met de *padwaarde* `speech.hypothesis` als een speech. hypo these-bericht.

### <a name="message-speechstartdetected"></a>Bericht`speech.startDetected`

Het `speech.startDetected` bericht geeft aan dat de speech-service spraak heeft gedetecteerd in de audio stroom.

| Veld | Description |
| ------------- | ---------------- |
| WebSocket-bericht codering | Text |
| Path | `speech.startDetected` |
| Content-Type | application/json; charset=utf-8 |
| Hoofdtekst | De JSON-structuur met informatie over de voor waarden wanneer het starten van de spraak is gedetecteerd. In het *Offset* veld in deze structuur wordt de offset (in 100-nano seconden eenheden) opgegeven wanneer er spraak is gedetecteerd in de audio stroom, ten opzichte van het begin van de stroom. |

#### <a name="sample-message"></a>Voorbeeldbericht

```HTML
Path: speech.startDetected
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Offset": 100000
}
```

### <a name="message-speechhypothesis"></a>Bericht`speech.hypothesis`

Tijdens spraak herkenning genereert de spraak service periodiek hypo thesen over de woorden die door de service worden herkend. Speech service stuurt deze hypo theses ongeveer elke 300 milliseconden naar de client. De `speech.hypothesis` is *alleen* geschikt voor het verbeteren van de gebruikers spraak ervaring. U mag geen afhankelijkheid nemen van de inhoud of nauw keurigheid van de tekst in deze berichten.

 Het `speech.hypothesis` bericht is van toepassing op de clients die een bepaalde functie voor het weer geven van tekst hebben en die vrijwel realtime feedback over de onderhanden herkenning kunnen geven aan de persoon die spreekt.

| Veld | Description |
| ------------- | ---------------- |
| WebSocket-bericht codering | Text |
| Path | `speech.hypothesis` |
| X-RequestId | UUID in indeling zonder streepje |
| Content-Type | application/json |
| Hoofdtekst | De JSON-structuur voor spraak hypo Thesen |

#### <a name="sample-message"></a>Voorbeeldbericht

```HTML
Path: speech.hypothesis
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Text": "this is a speech hypothesis",
  "Offset": 0,
  "Duration": 23600000
}
```

Met het *Offset* -element wordt de offset (in 100-nano seconden eenheden) aangegeven wanneer de zin is herkend, ten opzichte van het begin van de audio stroom.

Met het element *duration* wordt de duur (in 100-nano seconden eenheden) van deze spraak zin opgegeven.

Clients mogen geen veronderstellingen doen over de frequentie, de timing of de tekst in een spraak-hypo these of de consistentie van tekst in twee spraak hypo Thesen. De hypo Thesen zijn alleen moment opnamen in het transcriptie-proces in de service. Ze vertegenwoordigen geen stabiele accumulatie van transcriptie. Zo kan een eerste spraak hypo these de woorden ' fijn plezier ' bevatten en kan de tweede hypo these de woorden ' leuke Zoek ' bevatten. De spraak service voert geen naverwerking (bijvoorbeeld hoofdletter gebruik, lees tekens) uit voor de tekst in de woorden hypo these.

### <a name="message-speechphrase"></a>Bericht`speech.phrase`

Wanneer de spraak service vaststelt dat er voldoende informatie is om een herkennings resultaat te maken dat niet verandert, `speech.phrase` wordt een bericht door de service gegenereerd. De spraak service produceert deze resultaten nadat is vastgesteld dat de gebruiker een zin of woord groep heeft voltooid.

| Veld | Description |
| ------------- | ---------------- |
| WebSocket-bericht codering | Text |
| Path | `speech.phrase` |
| Content-Type | application/json |
| Hoofdtekst | De tekst van de JSON-structuur van de spraak |

Het JSON-schema van de spraak zin bevat de `RecognitionStatus`volgende `DisplayText`velden `Offset`:, `Duration`, en. Zie [transcriptie Responses](../concepts.md#transcription-responses)(Engelstalig) voor meer informatie over deze velden.

#### <a name="sample-message"></a>Voorbeeldbericht

```HTML
Path: speech.phrase
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": 0,
  "Duration": 12300000
}
```

### <a name="message-speechenddetected"></a>Bericht`speech.endDetected`

Het `speech.endDetected` bericht geeft aan dat de client toepassing het streamen van audio naar de service moet stoppen.

| Veld | Description |
| ------------- | ---------------- |
| WebSocket-bericht codering | Text |
| Path | `speech.endDetected` |
| Hoofdtekst | De JSON-structuur die de offset bevat wanneer het einde van de spraak is gedetecteerd. De offset wordt weer gegeven in 100-nano seconden eenheden van het begin van de audio die wordt gebruikt voor herkenning. |
| Content-Type | application/json; charset=utf-8 |

#### <a name="sample-message"></a>Voorbeeldbericht

```HTML
Path: speech.endDetected
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Offset": 0
}
```

Met het *Offset* -element wordt de offset (in 100-nano seconden eenheden) aangegeven wanneer de zin is herkend, ten opzichte van het begin van de audio stroom.

### <a name="message-turnstart"></a>Bericht`turn.start`

Hiermee wordt het begin van een bocht van het perspectief van de service gesignaleerd.`turn.start` Het `turn.start` bericht is altijd het eerste antwoord bericht dat u voor elke aanvraag ontvangt. Als u geen `turn.start` bericht ontvangt, moet u aannemen dat de status van de service verbinding ongeldig is.

| Veld | Description |
| ------------- | ---------------- |
| WebSocket-bericht codering | Text |
| Path | `turn.start` |
| Content-Type | application/json; charset=utf-8 |
| Hoofdtekst | JSON-structuur |

#### <a name="sample-message"></a>Voorbeeldbericht

```HTML
Path: turn.start
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "context": {
    "serviceTag": "7B33613B91714B32817815DC89633AFA"
  }
}
```

De hoofd tekst van `turn.start` het bericht is een JSON-structuur die context bevat voor het begin van de beurt. Het *context* element bevat een eigenschap *serviceTag* . Met deze eigenschap geeft u een label waarde op die aan de beurt is gekoppeld aan de service. Deze waarde kan worden gebruikt door micro soft als u hulp nodig hebt bij het oplossen van fouten in uw toepassing.

### <a name="message-turnend"></a>Bericht`turn.end`

De `turn.end` signalen het einde van een omschakeling van het perspectief van de service. Het `turn.end` bericht is altijd het laatste antwoord bericht dat u voor elke aanvraag ontvangt. Clients kunnen de ontvangst van dit bericht gebruiken als signaal voor opschoon activiteiten en overschakelen naar een niet-actieve status. Als u geen `turn.end` bericht ontvangt, moet u aannemen dat de status van de service verbinding ongeldig is. In dat geval sluit u de bestaande verbinding met de service en maakt u opnieuw verbinding.

| Veld | Description |
| ------------- | ---------------- |
| WebSocket-bericht codering | Text |
| Path | `turn.end` |
| Hoofdtekst | Geen |

#### <a name="sample-message"></a>Voorbeeldbericht

```HTML
Path: turn.end
X-RequestId: 123e4567e89b12d3a456426655440000
```

## <a name="telemetry-schema"></a>Telemetrie-schema

De hoofd tekst van het *telemetrie* -bericht is een JSON-structuur die client informatie over een bocht of een poging tot verbinding bevat. De structuur bestaat uit client tijds tempels die vastleggen wanneer client gebeurtenissen optreden. Elke tijds tempel moet de ISO 8601-indeling hebben, zoals beschreven in de sectie ' X-Time Stamp-koptekst '. *Telemetrie* -berichten waarbij niet alle vereiste velden in de JSON-structuur worden opgegeven of die niet gebruikmaken van de juiste notatie voor tijds tempels, kunnen ertoe leiden dat de service de verbinding met de client beëindigt. Clients *moeten* geldige waarden opgeven voor alle vereiste velden. Clients *moeten* indien nodig waarden voor optionele velden opgeven. De waarden die in de voor beelden in deze sectie worden weer gegeven, zijn alleen ter illustratie.

Het telemetrie-schema is onderverdeeld in de volgende onderdelen: tijds tempels en metrische gegevens van het bericht ontvangen. De notatie en het gebruik van elk deel worden opgegeven in de volgende secties.

### <a name="received-message-time-stamps"></a>Ontvangen tijds tempels van bericht

Clients moeten waarden voor tijd van ontvangst bevatten voor alle berichten die ze ontvangen nadat verbinding is gemaakt met de service. Deze waarden moeten de tijd vastleggen waarop de client elk bericht van het netwerk *heeft ontvangen* . De waarde mag geen andere tijd vastleggen. De client mag bijvoorbeeld niet de tijd vastleggen waarop het bericht is *verwerkt* . De ontvangen tijds tempels van het bericht zijn opgegeven in een matrix met de *naam:* waardeparen. De naam van het paar geeft de waarde van het *pad* van het bericht aan. De waarde van het paar geeft u de client tijd op waarop het bericht is ontvangen. Als er meer dan één bericht met de opgegeven naam is ontvangen, is de waarde van het paar een matrix met tijds tempels die aangeeft wanneer die berichten zijn ontvangen.

```JSON
  "ReceivedMessages": [
    { "speech.hypothesis": [ "2016-08-16T15:03:48.172Z", "2016-08-16T15:03:48.331Z", "2016-08-16T15:03:48.881Z" ] },
    { "speech.endDetected": "2016-08-16T15:03:49.721Z" },
    { "speech.phrase": "2016-08-16T15:03:50.001Z" },
    { "turn.end": "2016-08-16T15:03:51.021Z" }
  ]
```

Clients *moeten* de ontvangst bevestigen van alle berichten die door de service worden verzonden, door tijds tempels op te nemen voor deze berichten in de JSON-hoofd tekst. Als een client de ontvangst van een bericht niet bevestigt, kan de verbinding met de service worden verbroken.

### <a name="metrics"></a>Metrische gegevens

Clients moeten informatie over gebeurtenissen bevatten die zijn opgetreden tijdens de levens duur van een aanvraag. De volgende metrische gegevens worden ondersteund: `Connection`, `Microphone`en `ListeningTrigger`.

### <a name="metric-connection"></a>Gemeten`Connection`

De `Connection` metriek geeft details over verbindings pogingen door de client aan. De metrische gegevens moeten tijds tempels bevatten wanneer de WebSocket-verbinding is gestart en voltooid. De `Connection` metriek is *alleen vereist voor de eerste bocht van een verbinding*. Volgende schakels zijn niet vereist voor het toevoegen van deze gegevens. Als een client meerdere Verbindings pogingen doet voordat een verbinding tot stand is gebracht, moet informatie over *alle* Verbindings pogingen worden opgenomen. Zie [verbindings fout telemetrie](#connection-failure-telemetry)voor meer informatie.

| Veld | Description | Gebruik |
| ----- | ----------- | ----- |
| Name | `Connection` | Vereist |
| ID | De waarde van de verbindings-id die is gebruikt in de *X-ConnectionId* -header voor deze verbindings aanvraag | Vereist |
| Start | Het tijdstip waarop de client de verbindings aanvraag heeft verzonden | Vereist |
| einde | Het tijdstip waarop de client melding heeft ontvangen dat de verbinding tot stand is gebracht of, in fout gevallen, geweigerd, geweigerd of mislukt | Vereist |
| Fout | Een beschrijving van de fout die is opgetreden, indien van toepassing. Als de verbinding tot stand is gebracht, moeten clients dit veld weglaten. De maximale lengte van dit veld is 50 tekens. | Vereist voor fout cases, wordt anders wegge laten |

De fout beschrijving mag Maxi maal 50 tekens lang zijn en in het ideale geval moet een van de waarden in de volgende tabel worden weer gegeven. Als de fout niet overeenkomt met een van deze waarden, kunnen clients een beknopte beschrijving van de fout voorwaarde gebruiken met behulp van [CamelCasing](https://en.wikipedia.org/wiki/Camel_case) zonder spaties. De mogelijkheid om een *telemetrie* -bericht te verzenden vereist een verbinding met de service, zodat alleen tijdelijke of tijdelijke fout voorwaarden in het *telemetrie* -bericht kunnen worden gerapporteerd. Fout situaties waarbij een client *permanent* een verbinding met de service tot stand kan brengen, voor komen dat de client een bericht verzendt naar de service, inclusief *telemetrie* -berichten.

| Fout | Gebruik |
| ----- | ----- |
| DNSfailure | De client kan geen verbinding maken met de service vanwege een DNS-fout in de netwerk stack. |
| Geen netwerk | De client heeft een verbinding tot stand gebracht, maar de netwerk stack meldt dat er geen fysiek netwerk beschikbaar was. |
| Autorisatie | De client verbinding is mislukt bij een poging een autorisatie token te verkrijgen voor de verbinding. |
| Geen resources | De client heeft een of meer lokale bronnen (bijvoorbeeld geheugen) uitgevoerd tijdens het maken van een verbinding. |
| Verboden | De client kan geen verbinding maken met de service, omdat de service een http `403 Forbidden` -status code heeft geretourneerd voor de upgrade aanvraag van WebSocket. |
| Niet geautoriseerd | De client kan geen verbinding maken met de service, omdat de service een http `401 Unauthorized` -status code heeft geretourneerd voor de upgrade aanvraag van WebSocket. |
| BadRequest | De client kan geen verbinding maken met de service, omdat de service een http `400 Bad Request` -status code heeft geretourneerd voor de upgrade aanvraag van WebSocket. |
| ServerUnavailable | De client kan geen verbinding maken met de service, omdat de service een http `503 Server Unavailable` -status code heeft geretourneerd voor de upgrade aanvraag van WebSocket. |
| ServerError | De client kan geen verbinding maken met de service, omdat de service een `HTTP 500` interne fout status code voor de WebSocket-upgrade aanvraag heeft geretourneerd. |
| Time-out | Er is een time-out opgetreden voor de verbindings aanvraag van de client zonder reactie van de service. Het veld *End* bevat het tijdstip waarop de client een time-out heeft en de wacht tijd voor de verbinding is gestopt. |
| ClientError | De client heeft de verbinding verbroken vanwege een interne client fout. |

### <a name="metric-microphone"></a>Gemeten`Microphone`

De `Microphone` metriek is vereist voor alle gesp roken schakelingen. Deze metrische waarde meet de tijd op de client gedurende welke audio-invoer actief wordt gebruikt voor een spraak aanvraag.

Gebruik de volgende voor beelden als richt lijnen voor het vastleggen van start `Microphone` tijd waarden voor de metrische gegevens in uw client toepassing:

* Een client toepassing vereist dat een gebruiker op een fysieke knop moet drukken om de microfoon te starten. Nadat de knop is ingedrukt, leest de client toepassing de invoer van de microfoon en verzendt deze naar de speech-service. De *begin* waarde voor de `Microphone` metrische gegevens registreert de tijd na de knop push wanneer de microfoon is geïnitialiseerd en klaar is om invoer te leveren. De *eind* waarde voor de `Microphone` metrische gegevens registreert de tijd waarop de client toepassing het streamen van audio naar de service `speech.endDetected` heeft gestopt nadat het bericht van de service is ontvangen.

* Een client toepassing maakt gebruik van een tref woord Spotter dat ' Always ' luistert. Alleen nadat het tref woord Spotter een gesp roken trigger detecteert, verzamelt de client toepassing de invoer van de microfoon en verzendt deze naar de speech-service. De *begin* waarde voor de `Microphone` metrische gegevens registreert de tijd waarop het tref woord Spotter de client heeft gewaarschuwd om invoer van de microfoon te gebruiken. De *eind* waarde voor de `Microphone` metrische gegevens registreert de tijd waarop de client toepassing het streamen van audio naar de service `speech.endDetected` heeft gestopt nadat het bericht van de service is ontvangen.

* Een client toepassing heeft toegang tot een constante audio stroom en voert stilte/spraak detectie uit op die audio stroom in een *module voor spraak detectie*. De *begin* waarde voor de `Microphone` metrische gegevens registreert de tijd waarop de *module spraak detectie* de client heeft gewaarschuwd om invoer van de audio stroom te gebruiken. De *eind* waarde voor de `Microphone` metrische gegevens registreert de tijd waarop de client toepassing het streamen van audio naar de service `speech.endDetected` heeft gestopt nadat het bericht van de service is ontvangen.

* Een client toepassing verwerkt de tweede turn van een aanvraag voor meervoudige invoer en wordt op de hoogte gesteld door een service respons bericht om de microfoon in te scha kelen om invoer voor de tweede zet te verzamelen. De *begin* waarde voor de `Microphone` metrische gegevens registreert de tijd wanneer de client toepassing de microfoon inschakelt en begint met het gebruik van de invoer van die audio bron. De *eind* waarde voor de `Microphone` metrische gegevens registreert de tijd waarop de client toepassing het streamen van audio naar de service `speech.endDetected` heeft gestopt nadat het bericht van de service is ontvangen.

Met de waarde voor *eind* tijd `Microphone` voor de metriek wordt de tijd vastgelegd waarop de client toepassing is gestopt met het streamen van audio-invoer. In de meeste gevallen vindt deze gebeurtenis plaats kort nadat de client het `speech.endDetected` bericht van de service heeft ontvangen. Client toepassingen kunnen controleren of ze juist voldoen aan het protocol door ervoor te zorgen dat de *eind* tijd waarde voor de `Microphone` metriek later ligt dan de waarde voor de ontvangst tijd van het `speech.endDetected` bericht. En omdat er meestal een vertraging is tussen het einde van de ene beurt en het begin van een andere zet, kunnen clients het protocol conformiteit controleren door ervoor te zorgen dat de `Microphone` begin tijd van de metriek voor elke volgende keer correct wordt vastgelegd op de tijd waarop de client heeft de microfoon *gestart* met het streamen van audio-invoer naar de service.

| Veld | Description | Gebruik |
| ----- | ----------- | ----- |
| Name | Microfoon | Vereist |
| Start | Het tijdstip waarop de client audio-invoer van de microfoon of andere audio stroom heeft gestart of een trigger heeft ontvangen van het tref woord Spotter | Vereist |
| einde | Het tijdstip waarop de client stopt met het gebruik van de microfoon of audio stroom | Vereist |
| Fout | Een beschrijving van de fout die is opgetreden, indien van toepassing. Als de microfoon bewerkingen zijn geslaagd, moeten clients dit veld weglaten. De maximale lengte van dit veld is 50 tekens. | Vereist voor fout cases, wordt anders wegge laten |

### <a name="metric-listeningtrigger"></a>Gemeten`ListeningTrigger`
De `ListeningTrigger` meet waarde meet de tijd waarop de gebruiker de actie uitvoert die de spraak invoer initieert. De `ListeningTrigger` metriek is optioneel, maar clients die deze metrische gegevens kunnen opgeven, worden aanbevolen.

Gebruik de volgende voor beelden als richt lijnen voor het vastleggen van de *begin* - `ListeningTrigger` en *eind* tijd waarden voor de metrische gegevens in uw client toepassing.

* Een client toepassing vereist dat een gebruiker op een fysieke knop moet drukken om de microfoon te starten. De *begin* waarde voor deze metrische gegevens registreert de tijd van de knop push. Met de *eind* waarde wordt de tijd vastgelegd waarop het pushen van de knop is voltooid.

* Een client toepassing maakt gebruik van een tref woord Spotter dat ' Always ' luistert. Nadat het tref woord Spotter een gedicteerde trigger woordgroep detecteert, leest de client toepassing de invoer van de microfoon en verzendt deze naar de speech-service. De *begin* waarde voor deze metrische gegevens registreert de tijd waarop het tref woord Spotter audio heeft ontvangen die vervolgens is gedetecteerd als de trigger frase. Met de *eind* waarde wordt de tijd vastgelegd waarop het laatste woord van de trigger woordgroep door de gebruiker is gesp roken.

* Een client toepassing heeft toegang tot een constante audio stroom en voert stilte/spraak detectie uit op die audio stroom in een *module voor spraak detectie*. De *begin* waarde voor deze metrische gegevens registreert de tijd waarop de *module spraak detectie* audio heeft ontvangen die vervolgens als spraak is gedetecteerd. De *eind* waarde legt de tijd vast waarop de *module spraak detectie* spraak heeft gedetecteerd.

* Een client toepassing verwerkt de tweede turn van een aanvraag voor meervoudige invoer en wordt op de hoogte gesteld door een service respons bericht om de microfoon in te scha kelen om invoer voor de tweede zet te verzamelen. De client toepassing mag *geen* `ListeningTrigger` metrieke waarde voor deze turn bevatten.

| Veld | Description | Gebruik |
| ----- | ----------- | ----- |
| Name | ListeningTrigger | Optioneel |
| Start | Het tijdstip waarop de client luistert gestart | Vereist |
| einde | Het tijdstip waarop de client luistert die is voltooid | Vereist |
| Fout | Een beschrijving van de fout die is opgetreden, indien van toepassing. Als de trigger bewerking is geslaagd, moeten clients dit veld weglaten. De maximale lengte van dit veld is 50 tekens. | Vereist voor fout cases, wordt anders wegge laten |

#### <a name="sample-message"></a>Voorbeeldbericht

In het volgende voor beeld ziet u een telemetrie-bericht met zowel ReceivedMessages als metrische onderdelen:

```HTML
Path: telemetry
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000
X-Timestamp: 2016-08-16T15:03:54.183Z

{
  "ReceivedMessages": [
    { "speech.hypothesis": [ "2016-08-16T15:03:48.171Z", "2016-08-16T15:03:48.331Z", "2016-08-16T15:03:48.881Z" ] },
    { "speech.endDetected": "2016-08-16T15:03:49.721Z" },
    { "speech.phrase": "2016-08-16T15:03:50.001Z" },
    { "turn.end": "2016-08-16T15:03:51.021Z" }
  ],
  "Metrics": [
    {
      "Name": "Connection",
      "Id": "A140CAF92F71469FA41C72C7B5849253",
      "Start": "2016-08-16T15:03:47.921Z",
      "End": "2016-08-16T15:03:48.000Z",
    },
    {
      "Name": "ListeningTrigger",
      "Start": "2016-08-16T15:03:48.776Z",
      "End": "2016-08-16T15:03:48.777Z",
    },
    {
      "Name": "Microphone",
      "Start": "2016-08-16T15:03:47.921Z",
      "End": "2016-08-16T15:03:51.921Z",
    },
  ],
}
```

## <a name="error-handling"></a>Foutafhandeling

In deze sectie worden de soorten fout berichten en voor waarden beschreven die uw toepassing moet verwerken.

### <a name="http-status-codes"></a>HTTP-statuscodes

Tijdens de WebSocket-upgrade aanvraag kan de spraak service een van de standaard HTTP-status codes, zoals `400 Bad Request`, enzovoort, retour neren. Uw toepassing moet deze fout voorwaarden correct afhandelen.

#### <a name="authorization-errors"></a>Verificatiefouten

Als er tijdens de upgrade van de WebSocket geen onjuiste autorisatie wordt gegeven, wordt `403 Forbidden` door de speech-service een HTTP-status code geretourneerd. Onder de omstandigheden die deze fout code kunnen activeren, zijn:

* *Autorisatie* -header ontbreekt

* Ongeldig autorisatie token

* Verlopen autorisatie token

Het `403 Forbidden` fout bericht duidt niet op een probleem met de spraak service. Dit fout bericht geeft aan dat er een probleem is met de client toepassing.

### <a name="protocol-violation-errors"></a>Schendingen van protocol fouten

Als de speech-service eventuele schendingen van het Protocol van een client detecteert, wordt de WebSocket-verbinding door de service beëindigd nadat de *status code* en de *reden* voor de beëindiging zijn geretourneerd. Client toepassingen kunnen deze informatie gebruiken om problemen op te lossen en de schendingen op te lossen.

#### <a name="incorrect-message-format"></a>Onjuiste bericht indeling

Als een client een tekst-of binair bericht verzendt naar de service die niet is versleuteld in de juiste indeling die is opgegeven in deze specificatie, sluit de service de verbinding met een ongeldige status code voor *payload-gegevens van 1007* .

De service retourneert deze status code om verschillende redenen, zoals wordt weer gegeven in de volgende voor beelden:

* "Onjuiste bericht indeling. Het binaire bericht bevat een ongeldig voor voegsel voor de header grootte. De client heeft een binair bericht met een ongeldig voor voegsel voor de header grootte verzonden.

* "Onjuiste bericht indeling. Het binaire bericht heeft een ongeldige header-grootte. De client heeft een binair bericht verzonden dat een ongeldige header grootte heeft opgegeven.

* "Onjuiste bericht indeling. Fout bij het decoderen van binaire bericht headers in UTF-8. De client heeft een binair bericht verzonden met headers die niet juist zijn gecodeerd in UTF-8.

* "Onjuiste bericht indeling. Tekst bericht bevat geen gegevens. " De client heeft een tekst bericht verzonden dat geen hoofd gegevens bevat.

* "Onjuiste bericht indeling. Het SMS-bericht dat in UTF-8 wordt gedecodeerd, is mislukt. De client heeft een tekst bericht verzonden dat niet juist is gecodeerd in UTF-8.

* "Onjuiste bericht indeling. Het tekst bericht bevat geen scheidings teken voor de koptekst. " De client heeft een tekst bericht verzonden dat geen header scheidings teken bevat of het verkeerde scheidings teken voor kopteksten heeft gebruikt.

#### <a name="missing-or-empty-headers"></a>Ontbrekende of lege kopteksten

Als een client een bericht verzendt dat niet de vereiste headers *X-time-* out of het *pad*heeft, sluit de service de verbinding met de fout status code van een *1002-protocol* . Het bericht bevat een ontbrekende/lege koptekst. {Header naam}. "

#### <a name="requestid-values"></a>Aanvraag-waarde

Als een client een bericht verzendt waarin een *X-aanvraag-* header met een onjuiste indeling wordt opgegeven, wordt de verbinding door de service afgesloten en wordt een fout status van het *1002-protocol* geretourneerd. Het bericht is een ongeldige aanvraag. De waarde van de X-aanvraag-header is niet opgegeven in de No-Dash-UUID-indeling.

#### <a name="audio-encoding-errors"></a>Audio coderings fouten

Als een client een audio segment verzendt dat een bocht initieert en de audio-indeling of-code ring niet voldoet aan de vereiste specificatie, wordt de verbinding door de service afgesloten en wordt een ongeldige status code van een *nettolading van 1007* geretourneerd. Het bericht geeft de fout bron voor indelings codering aan.

#### <a name="requestid-reuse"></a>Aanvraag-opnieuw gebruiken

Als een client een bericht verzendt dat de aanvraag-id opnieuw gebruikt, wordt de verbinding door de service afgesloten en wordt er een fout status code van het *1002-protocol* weer gegeven. Het bericht is een ongeldige aanvraag. Het opnieuw gebruiken van aanvraag-id's is niet toegestaan. "

## <a name="connection-failure-telemetry"></a>Telemetrie verbindings fout

Om ervoor te zorgen dat de beste gebruikers ervaring is, moeten clients spraak service van de tijds tempels op de hoogte stellen van belang rijke controle punten binnen een verbinding door gebruik te maken van het *telemetrie* -bericht. Het is net zo belang rijk dat clients de service van verbindingen die zijn geprobeerd, op de hoogte brengen, maar mislukt.

Voor elke verbindings poging die is mislukt, maken clients een *telemetrie* -bericht met een unieke header waarde *X-id-aanvraag* . Omdat de client geen verbinding kan maken, kan het veld *ReceivedMessages* in de JSON-hoofd tekst worden wegge laten. Alleen de `Connection` invoer in het veld *metrieken* is opgenomen. Dit item bevat de begin-en eind tijd tempels en de fout voorwaarde die is aangetroffen.

### <a name="connection-retries-in-telemetry"></a>Verbindings pogingen in telemetrie

Clients moeten *nieuwe pogingen* onderscheiden van *meerdere Verbindings pogingen* door de gebeurtenis die de verbindings poging activeert. Verbindings pogingen die programmatisch worden uitgevoerd zonder tussen komst van de gebruiker, worden opnieuw geprobeerd. Meerdere Verbindings pogingen die worden uitgevoerd als reactie op gebruikers invoer zijn meerdere Verbindings pogingen. Clients geven elke door de gebruiker geactiveerde verbindings poging een unieke *X-aanvraag-* en *telemetrie* -bericht. Clients hergebruiken de *X-client-aanvraag* opnieuw voor programmatische pogingen. Als er meerdere nieuwe pogingen zijn gedaan voor een enkele verbindings poging, wordt elke nieuwe poging als `Connection` vermelding opgenomen in het *telemetrie* -bericht.

Stel bijvoorbeeld dat een gebruiker de trefwoord trigger uitspreekt om een verbinding te starten en de eerste verbindings poging mislukt vanwege DNS-fouten. Een tweede poging die via een programma wordt uitgevoerd door de client, slaagt echter. Omdat de client de verbinding opnieuw heeft uitgevoerd zonder dat er extra invoer van de gebruiker is vereist, gebruikt de client een enkel *telemetrie* -bericht met meerdere `Connection` vermeldingen om de verbinding te beschrijven.

Een voor beeld: een gebruiker spreekt de trefwoord trigger uit om een verbinding te starten en deze verbindings poging mislukt na drie nieuwe pogingen. De client geeft vervolgens een poging om verbinding te maken met de service en de gebruiker te informeren dat er iets is misgegaan. De gebruiker spreekt vervolgens de trefwoord trigger opnieuw. Stel dat de client verbinding maakt met de service. Nadat verbinding is gemaakt, verzendt de client onmiddellijk een *telemetrie* -bericht naar de `Connection` service die drie vermeldingen bevat die de verbindings fouten beschrijven. Nadat het `turn.end` bericht is ontvangen, verzendt de client een ander *telemetrie* -bericht met een beschrijving van de geslaagde verbinding.

## <a name="error-message-reference"></a>Naslag informatie over fout berichten

### <a name="http-status-codes"></a>HTTP-statuscodes

| HTTP-statuscode | Description | Problemen oplossen |
| - | - | - |
| 400 ongeldige aanvraag | De client heeft een onjuiste WebSocket-verbindings aanvraag verzonden. | Controleer of u alle vereiste para meters en HTTP-headers hebt opgegeven en of de waarden juist zijn. |
| 401 niet gemachtigd | De client bevat niet de vereiste autorisatie gegevens. | Controleer of u de *autorisatie* -header in de WebSocket-verbinding verzendt. |
| 403 verboden | De client heeft autorisatie gegevens verzonden, maar deze is ongeldig. | Controleer of u geen verlopen of ongeldige waarde in de *autorisatie* -header verzendt. |
| 404 Niet gevonden | De client heeft geprobeerd toegang te krijgen tot een URL-pad dat niet wordt ondersteund. | Controleer of u de juiste URL gebruikt voor de WebSocket-verbinding. |
| 500-server fout | De service heeft een interne fout aangetroffen en kan de aanvraag niet volt ooien. | In de meeste gevallen is deze fout tijdelijk. Voer de aanvraag opnieuw uit. |
| 503 Service niet beschikbaar | De service is niet beschikbaar om de aanvraag af te handelen. | In de meeste gevallen is deze fout tijdelijk. Voer de aanvraag opnieuw uit. |

### <a name="websocket-error-codes"></a>WebSocket-fout codes

| WebSocketsStatus-code | Description | Problemen oplossen |
| - | - | - |
| 1000 normale sluiting | De service heeft de WebSocket-verbinding zonder een fout gesloten. | Als het sluiten van de WebSocket onverwacht was, lees dan de documentatie opnieuw om te zien hoe en wanneer de service de WebSocket-verbinding kan beëindigen. |
| 1002-protocol fout | De client kan niet voldoen aan de protocol vereisten. | Zorg ervoor dat u de protocol documentatie begrijpt en duidelijk weet wat de vereisten zijn. Lees de vorige documentatie voor de oorzaken van de fout om te zien of u de protocol vereisten hebt geschonden. |
| 1007 ongeldige payload-gegevens | De client heeft een ongeldige Payload verzonden in een protocol bericht. | Controleer het laatste bericht dat u naar de service hebt verzonden voor fouten. Lees de vorige documentatie over Payload-fouten. |
| 1011-server fout | De service heeft een interne fout aangetroffen en kan de aanvraag niet volt ooien. | In de meeste gevallen is deze fout tijdelijk. Voer de aanvraag opnieuw uit. |

## <a name="related-topics"></a>Verwante onderwerpen

Bekijk een [Java script-SDK](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript) die een implementatie is van het Speech Service-Protocol op basis van websockets.
