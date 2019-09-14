---
title: Translator Speech-API referentie
titleSuffix: Azure Cognitive Services
description: Referentie documentatie voor de Translator Speech-API.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: reference
ms.date: 05/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 9d2f78d05de6b966dd872e0b57a90d1c8e890975
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965375"
---
# <a name="translator-speech-api"></a>Translator Speech-API

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Deze service biedt een streaming-API voor het transcriberen van gesp roken spraak van de ene taal in de tekst van een andere taal. De API integreert ook mogelijkheden voor tekst naar spraak om de vertaalde tekst terug te spreken. De Translator Speech-API biedt scenario's als realtime-vertaling van gesp rekken zoals weer gegeven in Skype Translator.

Met de Translator Speech-API streamen client toepassingen spraak audio naar de service en ontvangen ze een stroom van op tekst gebaseerde resultaten, waaronder de herkende tekst in de bron taal en de vertaling in de doel taal. Tekstresultaten worden geproduceerd door automatische spraakherkenning (Automatic Speech Recognition, ASR) toe te passen op de binnenkomende audiostream, mogelijk gemaakt door deep neural networks. De onbewerkte ASR-uitvoer wordt verder verbeterd door een nieuwe methode met de naam True, zodat de gebruikers intentie nauw keuriger kan worden weer gegeven. True verwijdert bijvoorbeeld disfluencies (de hmms en Coughs) en herstelt de juiste lees-en hoofd letters. Ook is de mogelijkheid om schuttingtaal te maskeren of uit te sluiten in de techniek opgenomen. De herkennings- en vertalingsengines zijn speciaal getraind om gesproken taal te verwerken. De service voor spraak omzetting gebruikt stilte detectie om het einde van een utterance te bepalen. Na een onderbreking in de stemactiviteit, streamt de service een definitief resultaat terug voor de voltooide taaluiting. De service kan ook gedeeltelijke resultaten terugsturen, zodat tussenliggende herkenningen en vertalingen zichtbaar zijn voor een lopende taaluiting. Voor de uiteindelijke resultaten biedt de service de mogelijkheid om spraak (tekst-naar-spraak) te syntheseen vanuit de gesp roken tekst in de doel talen. Tekst-naar-spraak-audio wordt gemaakt in de indeling die is opgegeven door de client. De indelingen WAV en MP3 zijn beschikbaar.

Translator Speech-API maakt gebruik van het WebSocket-protocol om een full-duplex communicatie kanaal tussen de client en de server op te geven. Voor een toepassing moeten de volgende stappen worden uitgevoerd om de service te gebruiken:

## <a name="1-getting-started"></a>1. Aan de slag
Voor toegang tot de Translator Text-API moet u [zich registreren voor Microsoft Azure](translator-speech-how-to-signup.md).

## <a name="2-authentication"></a>2. Authentication

De abonnements sleutel gebruiken om te verifiëren. De Translator Speech-API ondersteunt twee modi voor verificatie:

* **Een toegangs token gebruiken:** Verschaf in uw toepassing een toegangs token van de token service. Gebruik uw Translator Speech-API-abonnements sleutel om een toegangs token van de Azure Cognitive Services Authentication-service te verkrijgen. Het toegangs token is 10 minuten geldig. Vraag elke 10 minuten een nieuw toegangs token op en blijf hetzelfde toegangs token gebruiken voor herhaalde aanvragen binnen deze tien minuten.

* **Rechtstreeks een abonnements sleutel gebruiken:** Geef in uw toepassing uw abonnements sleutel door als een waarde in `Ocp-Apim-Subscription-Key` de koptekst.

Behandel uw abonnements sleutel en het toegangs token als geheimen die moeten worden verborgen in de weer gave.

## <a name="3-query-languages"></a>3. Query talen
**Een query uitvoeren op de resource taal voor de huidige set ondersteunde talen.** In de [talen resource](languages-reference.md) wordt de set talen en stemmen beschikbaar gesteld voor spraak herkenning, voor tekst omzetting en voor tekst naar spraak. Elke taal of stem krijgt een id die door de Translator Speech-API wordt gebruikt om dezelfde taal of spraak te identificeren.

## <a name="4-stream-audio"></a>4. Audio streamen
**Open een verbinding en begin met het streamen van audio naar de service.** De service-URL `wss://dev.microsofttranslator.com/speech/translate`is. De para meters en audio-indelingen die door de service worden verwacht `/speech/translate` , worden hieronder beschreven, in de bewerking. Een van de para meters wordt gebruikt om het toegangs token uit stap 2 hierboven door te geven.

## <a name="5-process-the-results"></a>5. De resultaten verwerken
**De resultaten die vanuit de service zijn gestreamd verwerken.** De indeling van gedeeltelijke resultaten, eind resultaten en tekst naar spraak-audio segmenten worden beschreven in de documentatie van de `/speech/translate` onderstaande bewerking.

Code voorbeelden van het gebruik van de Translator Speech-API zijn beschikbaar via de [micro soft Translator github-site](https://github.com/MicrosoftTranslator).

## <a name="implementation-notes"></a>Opmerkingen bij de implementatie

/Speech/Translate ophalen een sessie voor spraak omzetting tot stand brengen

### <a name="connecting"></a>Verbinding maken
Voordat u verbinding maakt met de service, bekijkt u de lijst met para meters die verderop in deze sectie worden gegeven. Een voorbeeld aanvraag is:

`GET wss://dev.microsofttranslator.com/speech/translate?from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa&api-version=1.0`
`Ocp-Apim-Subscription-Key: {subscription key}`
`X-ClientTraceId: {GUID}`

De aanvraag geeft aan dat gesp roken Engels wordt gestreamd naar de service en wordt vertaald in het Italiaans. Elk laatste herkennings resultaat genereert een tekst-naar-spraak-audio-antwoord met de vrouwelijke stem met de naam Elsa. U ziet dat de aanvraag referenties bevat in `Ocp-Apim-Subscription-Key header`de. De aanvraag volgt ook een best practice door een Globally Unique Identifier in de header `X-ClientTraceId`in te stellen. Een client toepassing moet de traceer-ID registreren, zodat deze kan worden gebruikt om problemen op te lossen wanneer deze zich voordoen.

### <a name="sending-audio"></a>Audio verzenden
Zodra de verbinding tot stand is gebracht, begint de client met het streamen van audio naar de service. De client verzendt audio in segmenten. Elk segment wordt verzonden met behulp van een WebSocket-bericht van het type binary.

Audio-invoer bevindt zich in de indeling van het Waveform-audio bestand (Golf, of meer bekend als WAV vanwege de bestandsnaam extensie). De client toepassing moet één kanaal streamen, ondertekend 16 PCM-audio samples bij 16 kHz. De eerste set bytes die door de client wordt gestreamd, bevat de WAV-header. Een 44-byte-header voor een Single Channel, met een 16-bits PCM-stroom van 16 kHz:

|Offset|Value|
|:---|:---|
|0 - 3|RIFF|
|4 - 7|0|
|8 - 11|WAVE|
|12 - 15|fmt|
|16 - 19|16|
|20 - 21|1|
|22 - 23|1|
|24 - 27|16000|
|28 - 31|32000|
|32 - 33|2|
|34 - 35|16|
|36 - 39|gegevens|
|40-43|0|

U ziet dat de totale bestands grootte (bytes 4-7) en de grootte van de gegevens (bytes 40-43) zijn ingesteld op nul. Dit is een goede oplossing voor het streaming-scenario waarbij de totale grootte niet noodzakelijkerwijs bekend is.

Na verzen ding van de WAV-header (RIFF) verzendt de client segmenten van de audio gegevens. De client zal doorgaans vaste segmenten streamen die een vaste duur vertegenwoordigen (bijvoorbeeld stream 100 MS van audio per keer).

### <a name="signal-the-end-of-the-utterance"></a>Het einde van de utterance signalering geven
De Translator Speech-API retourneert de transcriptie en de vertaling van de audio stroom wanneer u de audio verzendt. De laatste transcriptie, de laatste vertaling en de vertaalde audio worden alleen naar u teruggestuurd na het einde van de utterance. In sommige gevallen wilt u mogelijk het einde van de utterance forceren. Verzend 2,5 seconden stilte om het einde van de utterance af te dwingen.

### <a name="final-result"></a>Eind resultaat
Er wordt een laatste resultaat van een spraak herkenning gegenereerd aan het einde van een utterance. Er wordt een resultaat van de service naar de client verzonden met behulp van een WebSocket-bericht van het type tekst. De inhoud van het bericht is de JSON-serialisatie van een object met de volgende eigenschappen:

* `type`: Teken reeks constante voor het identificeren van het type resultaat. De waarde is definitief voor de uiteindelijke resultaten.
* `id`: Teken reeks-id die is toegewezen aan het herkennings resultaat.
* `recognition`: Herkende tekst in de bron taal. De tekst kan een lege teken reeks zijn in het geval van een onjuiste herkenning.
* `translation`: Herkende tekst vertaald in de doel taal.
* `audioTimeOffset`: De tijd verschuiving van het begin van de herkenning in Ticks (1 Tick = 100 nano seconden). De offset is relatief ten opzichte van het begin van streaming.
* `audioTimeSize`: De duur in Ticks (100 nano seconden) van de herkenning.
* `audioStreamPosition`: De byte offset van het begin van de herkenning. De offset is relatief ten opzichte van het begin van de stroom.
* `audioSizeBytes`: Grootte in bytes van de herkenning.

Houd er rekening mee dat het plaatsen van de herkenning in de audio stroom niet standaard wordt opgenomen in de resultaten. De `TimingInfo` functie moet worden geselecteerd door de client (Zie `features` para meter).

Een voor beeld van een eind resultaat is als volgt:

```
{
  type: "final"
  id: "23",
  recognition: "what was said",
  translation: "translation of what was said",
  audioStreamPosition: 319680,
  audioSizeBytes: 35840,
  audioTimeOffset: 2731600000,
  audioTimeSize: 21900000
}
```

### <a name="partial-result"></a>Gedeeltelijk resultaat
Gedeeltelijke of tussenliggende spraak herkennings resultaten worden standaard niet naar de client gestreamd. De client kan de para meter van de onderdelen query gebruiken om ze aan te vragen.

Er wordt een gedeeltelijk resultaat van de service naar de client verzonden met behulp van een WebSocket-bericht van het type tekst. De inhoud van het bericht is de JSON-serialisatie van een object met de volgende eigenschappen:

* `type`: Teken reeks constante voor het identificeren van het type resultaat. De waarde is gedeeltelijk voor gedeeltelijke resultaten.
* `id`: Teken reeks-id die is toegewezen aan het herkennings resultaat.
* `recognition`: Herkende tekst in de bron taal.
* `translation`: Herkende tekst vertaald in de doel taal.
* `audioTimeOffset`: De tijd verschuiving van het begin van de herkenning in Ticks (1 Tick = 100 nano seconden). De offset is relatief ten opzichte van het begin van streaming.
* `audioTimeSize`: De duur in Ticks (100 nano seconden) van de herkenning.
* `audioStreamPosition`: De byte offset van het begin van de herkenning. De offset is relatief ten opzichte van het begin van de stroom.
* `audioSizeBytes`: Grootte in bytes van de herkenning.

Houd er rekening mee dat het plaatsen van de herkenning in de audio stroom niet standaard wordt opgenomen in de resultaten. De functie TimingInfo moet worden geselecteerd door de client (Zie de para meter onderdelen).

Een voor beeld van een eind resultaat is als volgt:

```
{
  type: "partial"
  id: "23.2",
  recognition: "what was",
  translation: "translation of what was",
  audioStreamPosition: 319680,
  audioSizeBytes: 25840,
  audioTimeOffset: 2731600000,
  audioTimeSize: 11900000
}
```

### <a name="text-to-speech"></a>Tekst naar spraak
Wanneer de functie voor tekst naar spraak is ingeschakeld (Zie `features` de para meter hieronder), wordt het eind resultaat gevolgd door de audio van de gesp roken vertaling. Audio gegevens worden gesegmenteerd en verzonden van de service naar de client als een reeks WebSocket-berichten van het type binary. Een client kan het einde van de stroom detecteren door de FIN-bit van elk bericht te controleren. Voor het laatste binaire bericht wordt de FIN-bit ingesteld op één om het einde van de stroom aan te geven. De indeling van de stroom is afhankelijk van de waarde van de `format` para meter.

### <a name="closing-the-connection"></a>De verbinding sluiten
Wanneer een client toepassing klaar is met het streamen van audio en het laatste eind resultaat heeft ontvangen, moet de verbinding worden gesloten door de handshake voor het sluiten van websockets te initiëren. Er zijn voor waarden waardoor de server de verbinding kan beëindigen. De volgende WebSocket-gesloten codes kunnen door de client worden ontvangen:

* `1003 - Invalid Message Type`: De verbinding met de server wordt beëindigd omdat het ontvangen gegevens type niet kan worden geaccepteerd. Dit gebeurt meestal wanneer inkomende audio niet met een juiste header begint.
* `1000 - Normal closure`: De verbinding is gesloten nadat de aanvraag is voltooid. De server sluit de verbinding: wanneer er gedurende lange tijd geen audio van de client wordt ontvangen. Wanneer stilte gedurende lange tijd wordt gestreamd; Wanneer een sessie de toegestane maximum duur bereikt (ongeveer 90 minuten).
* `1001 - Endpoint Unavailable`: Geeft aan dat de server niet meer beschikbaar is. Client toepassing kan proberen opnieuw verbinding te maken met een limiet voor het aantal nieuwe pogingen.
* `1011 - Internal Server Error`: De verbinding wordt gesloten door de server vanwege een fout op de server.

### <a name="parameters"></a>Parameters

|Parameter|Value|Description|Parametertype|Gegevenstype|
|:---|:---|:---|:---|:---|
|api-version|1.0|De versie van de API die door de client is aangevraagd. Toegestane waarden zijn: `1.0`.|query   |string|
|from|gelaten   |Hiermee geeft u de taal van de binnenkomende spraak op. De waarde is een van de taal-id's van het `speech` bereik in het antwoord van de talen-API.|query|string|
|to|gelaten|Hiermee geeft u de taal voor de vertaling van de omgezette tekst in. De waarde is een van de taal-id's van het `text` bereik in het antwoord van de talen-API.|query|string|
|database|gelaten   |Door komma's gescheiden set functies die door de client zijn geselecteerd. Beschik bare functies zijn:<ul><li>`TextToSpeech`: Hiermee geeft u op dat de service de vertaalde audio van de laatste omgezette zin moet retour neren.</li><li>`Partial`: Hiermee geeft u op dat de service tussenliggende herkennings resultaten moet retour neren terwijl de audio streamt naar de service.</li><li>`TimingInfo`: Hiermee geeft u op dat de service timing gegevens moet retour neren die zijn gekoppeld aan elke herkenning.</li></ul>Zo zou een client opgeven `features=partial,texttospeech` dat er gedeeltelijke resultaten en tekst naar spraak moeten worden ontvangen, maar geen timing gegevens. Houd er rekening mee dat de uiteindelijke resultaten altijd naar de client worden gestreamd.|query|string|
|Roken|gelaten|Hiermee wordt aangegeven welke stem moet worden gebruikt voor het weer geven van tekst naar spraak van de vertaalde tekst. De waarde is een van de spraak-id's uit het TTS-bereik in het antwoord van de talen-API. Als er geen spraak is opgegeven, kiest het systeem automatisch een als de functie tekst-naar-spraak is ingeschakeld.|query|string|
|format|gelaten|Hiermee geeft u de indeling op van de tekst-naar-spraak-audio stroom die door de service wordt geretourneerd. De volgende opties zijn beschikbaar:<ul><li>`audio/wav`: Waveform-audio stroom. De client moet de WAV-header gebruiken om de audio-indeling goed te kunnen interpreteren. WAV-audio voor tekst naar spraak is 16-bits, Single Channel PCM met een sampling frequentie van 24kHz of 16kHz.</li><li>`audio/mp3`: MP3-Audio stroom.</li></ul>De standaardwaarde is `audio/wav`.|query|string|
|ProfanityAction    |gelaten    |Hiermee geeft u op hoe de service woorden die in de spraak worden herkend, moeten afhandelen. Geldige acties zijn:<ul><li>`NoAction`: Ongewijzigde voor vallen blijven bestaan.</li><li>`Marked`: Grove woorden worden vervangen door een markering. Zie `ProfanityMarker` para meter.</li><li>`Deleted`: Scheld woorden worden verwijderd. Als het woord `"jackass"` bijvoorbeeld als grove taal wordt beschouwd, wordt de zin `"He is a jackass."``"He is a .".`</li></ul>De standaard waarde is gemarkeerd.|query|string|
|ProfanityMarker|gelaten    |Hiermee geeft u op hoe gedetecteerde woorden `ProfanityAction` worden verwerkt wanneer `Marked`deze is ingesteld op. Geldige opties zijn:<ul><li>`Asterisk`: Grove woorden worden vervangen door de teken `***`reeks. Als het woord `"jackass"` bijvoorbeeld als grove taal wordt beschouwd, wordt de zin `"He is a jackass."``"He is a ***.".`</li><li>`Tag`: Scheld woorden worden omgeven door een XML-label van woorden. Als het woord `"jackass"` bijvoorbeeld als grove taal wordt beschouwd, wordt `"He is a <profanity>jackass</profanity>."`de woord groep `"He is a jackass."` .</li></ul>De standaardwaarde is `Asterisk`.|query|string|
|Authorization|gelaten  |Hiermee geeft u de waarde op van het Bearer-token van de client. Gebruik het voor `Bearer` voegsel gevolgd door de waarde van `access_token` de waarde die door de verificatie token service wordt geretourneerd.|koptekst   |string|
|OCP-Apim-Subscription-Key|gelaten|Vereist als de `Authorization` koptekst niet is opgegeven.|koptekst|string|
|access_token|gelaten   |Alternatieve manier om een geldig OAuth-toegangs token door te geven. Het Bearer-token wordt meestal gegeven met `Authorization`een header. In sommige WebSocket-bibliotheken is client code niet toegestaan om headers in te stellen. In dat geval kan de client de `access_token` query parameter gebruiken om een geldig token door te geven. Als er een toegangs token wordt gebruikt om te `Authorization` verifiëren, `access_token` moet er een worden ingesteld als er geen header is ingesteld. Als zowel de header-als de query parameter zijn ingesteld, wordt de query parameter genegeerd. Clients moeten slechts één methode gebruiken om het token door te geven.|query|string|
|abonnement-sleutel|gelaten   |Alternatieve manier om de abonnements sleutel door te geven. In sommige WebSocket-bibliotheken is client code niet toegestaan om headers in te stellen. In dat geval kan de client de `subscription-key` query parameter gebruiken om een geldige abonnements sleutel door te geven. Wanneer u een abonnements sleutel gebruikt voor verificatie, `Ocp-Apim-Subscription-Key` moet u een abonnements sleutel instellen als er geen header is ingesteld. Als zowel de header-als de query parameter zijn ingesteld, wordt de query parameter genegeerd. Clients moeten slechts één methode gebruiken om de `subscription key`te door geven.|query|string|
|X-ClientTraceId    |gelaten    |Een door de client gegenereerde GUID die wordt gebruikt om een aanvraag te traceren. Voor een correcte probleem oplossing van problemen moeten clients een nieuwe waarde opgeven bij elke aanvraag en deze registreren.<br/>In plaats van een header te gebruiken, kan deze waarde worden door gegeven `X-ClientTraceId`met query parameter. Als zowel de header-als de query parameter zijn ingesteld, wordt de query parameter genegeerd.|koptekst|string|
|X-CorrelationId|gelaten    |Een door de client gegenereerde id die wordt gebruikt voor het correleren van meerdere kanalen in een gesprek. Er kunnen meerdere spraak Vertaal sessies worden gemaakt om gesp rekken tussen gebruikers mogelijk te maken. In dit scenario gebruiken alle spraak Vertaal sessies dezelfde correlatie-ID om de kanalen samen te koppelen. Dit vereenvoudigt tracering en diagnostische gegevens. De id moet voldoen aan:`^[a-zA-Z0-9-_.]{1,64}$`<br/>In plaats van een header te gebruiken, kan deze waarde worden door gegeven `X-CorrelationId`met query parameter. Als zowel de header-als de query parameter zijn ingesteld, wordt de query parameter genegeerd.|koptekst|string|
|X-ClientVersion|gelaten    |Hiermee wordt de versie van de client toepassing aangegeven. Voorbeeld: "2.1.0.123".<br/>In plaats van een header te gebruiken, kan deze waarde worden door gegeven `X-ClientVersion`met query parameter. Als zowel de header-als de query parameter zijn ingesteld, wordt de query parameter genegeerd.|koptekst|string|
|X-OsPlatform|gelaten   |Hiermee worden de naam en versie van het besturings systeem aangegeven waarop de client toepassing wordt uitgevoerd. Voorbeelden: "Android 5,0", "iOs 8.1.3", "Windows 8,1".<br/>In plaats van een header te gebruiken, kan deze waarde worden door gegeven `X-OsPlatform`met query parameter. Als zowel de header-als de query parameter zijn ingesteld, wordt de query parameter genegeerd.|koptekst|string|

### <a name="response-messages"></a>Antwoord berichten

|HTTP-status code|Reason|Antwoord model|Headers|
|:--|:--|:--|:--|
|101    |WebSocket-upgrade.|Voorbeeld waarde model <br/> Object{}|X-RequestId<br/>Een waarde voor het identificeren van de aanvraag voor het oplossen van problemen.<br/>string|
|400    |Ongeldige aanvraag. Controleer de invoer parameters om er zeker van te zijn dat ze geldig zijn. Het object Response bevat een gedetailleerde beschrijving van de fout.|||
|401    |Gasten. Zorg ervoor dat de referenties zijn ingesteld, dat ze geldig zijn en dat uw Azure data Market-abonnement in goede staat is met een beschik bare balans.|||
|500    |Er is een fout opgetreden. Als de fout zich blijft voordoen, meldt u deze met de client Trace Identifier (X-ClientTraceId) of de aanvraag-id (X-id-aanvraag).|||
|503    |De server is tijdelijk niet beschikbaar. Voer de aanvraag opnieuw uit. Als de fout zich blijft voordoen, meldt u deze met de client Trace Identifier (X-ClientTraceId) of de aanvraag-id (X-id-aanvraag).|||
