---
title: Release Notes - Spraakservice
titleSuffix: Azure Cognitive Services
description: Een lopend logboek van Speech Service-functie releases, verbeteringen, bug fixes, en bekende problemen.
services: cognitive-services
author: brianem
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: brianem
ms.custom: seodec18
ms.openlocfilehash: cbe9c7cbd0f402e38d1163050d77b055f89948ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219641"
---
# <a name="release-notes"></a>Releaseopmerkingen
## <a name="speech-sdk-1100-2020-february-release"></a>Speech SDK 1.10.0: release 2020-februari

**Nieuwe functies**
 - Toegevoegd Python pakketten ter ondersteuning van de nieuwe 3.8 release van Python.
 - Ondersteuning voor Red Hat Enterprise Linux (RHEL)/CentOS 8 x64 (C++, C#, Java, Python).
   > [!NOTE] 
   > Klanten moeten OpenSSL configureren volgens [deze instructies.](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-openssl-linux)
 - Linux ARM32 ondersteuning voor Debian en Ubuntu.
 - DialogServiceConnector ondersteunt nu een optionele parameter 'bot-ID' op BotFrameworkConfig. Met deze parameter u meerdere Direct Line Speech-bots gebruiken met één Azure-spraakbron. Zonder de opgegeven parameter wordt de standaardbot (zoals bepaald door de pagina direct line spraakkanaal) gebruikt.
 - DialogServiceConnector heeft nu een eigenschap SpeechActivityTemplate. De inhoud van deze JSON-tekenreeks wordt gebruikt door Direct Line Speech om een breed scala aan ondersteunde velden vooraf in te vullen in alle activiteiten die een Direct Line Speech-bot bereiken, inclusief activiteiten die automatisch worden gegenereerd als reactie op gebeurtenissen zoals spraakherkenning.
 - TTS gebruikt nu abonnementssleutel voor authenticatie, waardoor de eerste bytelatentie van het eerste syntheseresultaat na het maken van een synthesizer wordt verminderd.
 - Bijgewerkte spraakherkenningsmodellen voor 19 locaties voor een gemiddelde vermindering van het woordfoutenpercentage van 18,6% (es-ES, es-MX, fr-CA, fr-FR, it-IT, ja-JP, ko-KR, pt-BR, zh-CN, zh-HK, nb-NO, fi-FL, ru-RU, pl-PL, ca-ES, zh-TW, th-TH, pt-PT, tr-TR). De nieuwe modellen brengen aanzienlijke verbeteringen op meerdere domeinen, waaronder dicteren, Call-Center Transcriptie en Video Indexering scenario's.

**Opgeloste fouten**
 - Fixed bug where Conversation Transcriber did not await properly in JAVA API's Fixed bug where Conversation Transcriber did not await properly in JAVA API's Fixed bug where Conversation Transcriber did not await properly in JAVA API' 
 - Android x86 emulator fix voor Xamarin [GitHub probleem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/363)
 - Ontbreken toevoegen (Get| Eigenschapsmethoden instellen voor AudioConfig
 - Een TTS-bug oplossen waarbij de audioDataStream niet kan worden gestopt wanneer de verbinding mislukt
 - Het gebruik van een eindpunt zonder regio zou leiden tot USP-fouten voor gespreksvertaler
 - ID-generatie in Universal Windows Applications maakt nu gebruik van een passend uniek GUID-algoritme; het eerder en onbedoeld in gebreke gebleven tot een stubbed implementatie die vaak geproduceerd botsingen over grote sets van interacties.
 
 **Monsters**
 - Unity-voorbeeld voor het gebruik van Speech SDK met [Unity-microfoon en pushmodus streaming](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/from-unitymicrophone)

**Andere wijzigingen**
 - [OpenSSL-configuratiedocumentatie bijgewerkt voor Linux](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-openssl-linux)

## <a name="speech-sdk-190-2020-january-release"></a>Speech SDK 1.9.0: release 2020-januari

**Nieuwe functies**

- Gesprek op meerdere apparaten: verbind meerdere apparaten met hetzelfde spraak- of tekstgesprek en vertaal optioneel berichten die tussen beide apparaten worden verzonden. Meer informatie in [dit artikel](multi-device-conversation.md). 
- Ondersteuning voor trefwoordherkenning toegevoegd voor Android .aar-pakket en ondersteuning toegevoegd voor x86- en x64-smaken. 
- Doelstelling-C: `SendMessage` `SetMessageProperty` en methoden `Connection` die aan object zijn toegevoegd. Zie documentatie [hier](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxconnection).
- TTS C++ api `std::wstring` ondersteunt nu als synthesetekstinvoer, waardoor de noodzaak om een tekenreeks om te zetten naar tekenreeks wordt verwijderd voordat deze wordt doorgestroomd naar de SDK. Zie [details hier](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer#speaktextasync). 
- C#: [Taal-ID](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-automatic-language-detection?pivots=programming-language-csharp) en [brontaalconfig](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-specify-source-language?pivots=programming-language-csharp) zijn nu beschikbaar.
- JavaScript: Een functie `Connection` toegevoegd om bezwaar te maken om aangepaste `receivedServiceMessage`berichten van de spraakservice door te geven als terugroepen.
- JavaScript: Ondersteuning `FromHost API` toegevoegd voor het gebruik met on-prem-containers en soevereine clouds. Zie documentatie [hier](speech-container-howto.md).
- JavaScript: We `NODE_TLS_REJECT_UNAUTHORIZED` eer nu dankzij een bijdrage van [orgads](https://github.com/orgads). Zie [details hier](https://github.com/microsoft/cognitive-services-speech-sdk-js/pull/75).


**Wijzigingen doorbreken**

- `OpenSSL`is bijgewerkt naar versie 1.1.1b en is statisch gekoppeld aan de Speech SDK core library voor Linux. Dit kan een onderbreking veroorzaken `OpenSSL` als uw postvak `/usr/lib/ssl` IN niet is geïnstalleerd op de map in het systeem. Controleer [onze documentatie](how-to-configure-openssl-linux.md) onder Speech SDK-documenten om het probleem te omzeilen.
- We hebben het gegevenstype dat `WordLevelTimingResult.Offset` `int` voor `long` C# is `WordLevelTimingResults` geretourneerd, gewijzigd om toegang te krijgen tot wanneer spraakgegevens langer zijn dan 2 minuten.
- `PushAudioInputStream`en `PullAudioInputStream` stuur nu wav-header-informatie `AudioStreamFormat`naar de spraakservice op basis van , optioneel opgegeven toen ze zijn gemaakt. Klanten moeten nu de [ondersteunde audio-invoerindeling](how-to-use-audio-input-streams.md)gebruiken. Alle andere indelingen krijgen suboptimale herkenningsresultaten of kunnen andere problemen veroorzaken. 


**Opgeloste fouten**

- Zie `OpenSSL` de update onder Bovenstaande Wijzigingen verbreken. We hebben zowel een intermitterende crash als een prestatieprobleem (lock contention under high load) in Linux en Java opgelost. 
- Java: Verbeteringen aangebracht in objectsluiting in hoge gelijktijdigheidsscenario's.
- Geherstructureerd ons NuGet pakket. We verwijderden de `Microsoft.CognitiveServices.Speech.core.dll` `Microsoft.CognitiveServices.Speech.extension.kws.dll` drie kopieën van en onder lib mappen, waardoor het NuGet-pakket kleiner en sneller te downloaden was, en we voegden headers toe die nodig waren om een aantal C++ native apps samen te stellen.
- Fixed quickstart samples [here](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp). Deze werden verlaten zonder het weergeven van "microfoon niet gevonden" uitzondering op Linux, MacOS, Windows.
- Fixed SDK crash with long speech recognition results on certain code paths like [this sample.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/csharp/uwp/speechtotext-uwp)
- Fixed SDK deployment error in Azure Web App environment to address [this customer issue.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/396)
- Fixed a TTS error `<voice>` while `<audio>` using multi tag or tag to address [this customer issue.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/433) 
- Fixed a TTS 401 error when the SDK is recovered from suspended.
- JavaScript: Fixed a circular import of audio data thanks to a contribution from [euirim.](https://github.com/euirim) 
- JavaScript: added support for setting service properties, as added in 1.7.
- JavaScript: een probleem opgelost waarbij een verbindingsfout kan leiden tot continue, mislukte pogingen om opnieuw verbinding te maken.


**Monsters**

- Toegevoegd trefwoord herkenning monster voor Android [hier](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/android/sdkdemo).
- TTS-voorbeeld toegevoegd voor het serverscenario [hier](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs).
- Added Multi-device conversation quickstarts for C# and C++ [here](quickstarts/multi-device-conversation.md).


**Andere wijzigingen**

- Geoptimaliseerde SDK-kernbibliotheekgrootte op Android.
- SDK in 1.9.0 en `int` `string` verder ondersteunt beide en typen in het veld voice signature-versie voor Conversation Transcriber.

## <a name="speech-sdk-180-2019-november-release"></a>Speech SDK 1.8.0: release 2019-november

**Nieuwe functies**

- Een `FromHost()` API toegevoegd, om het gebruik te vergemakkelijken met on-prem containers en soevereine wolken.
- Automatische brontaaldetectie toegevoegd voor spraakherkenning (in Java en C++)
- Object `SourceLanguageConfig` toegevoegd voor spraakherkenning, dat wordt gebruikt om verwachte brontalen op te geven (in Java en C++)
- Ondersteuning `KeywordRecognizer` toegevoegd op Windows (UWP), Android en iOS via de NuGet- en Unity-pakketten
- Remote Conversation Java API toegevoegd om Conversation Transcription in asynchrone batches te doen.

**Wijzigingen doorbreken**

- Conversation Transcriber-functionaliteiten verplaatst `Microsoft.CognitiveServices.Speech.Transcription`onder naamruimte .
- Een deel van de methoden Conversation `Conversation` Transcriber wordt verplaatst naar een nieuwe klasse.
- Ondersteuning voor 32-bits (ARMv7 en x86) iOS

**Opgeloste fouten**

- Oplossing voor crash `KeywordRecognizer` als lokaal wordt gebruikt zonder geldige abonnementssleutel voor spraakservice

**Monsters**

- Xamarin monster voor`KeywordRecognizer`
- Eenheidsmonster voor`KeywordRecognizer`
- C++ en Java-voorbeelden voor automatische brontaaldetectie.

## <a name="speech-sdk-170-2019-september-release"></a>Speech SDK 1.7.0: release 2019-september

**Nieuwe functies**

- Bèta-ondersteuning voor Xamarin toegevoegd op het Universal Windows Platform (UWP), Android en iOS
- IOS-ondersteuning toegevoegd voor Unity
- Ondersteuning `Compressed` voor input toegevoegd voor ALaw, Mulaw, FLAC op Android, iOS en Linux
- Toegevoegd `SendMessageAsync` `Connection` in de klas voor het verzenden van een bericht naar de service
- Toegevoegd `SetMessageProperty` `Connection` in de klasse voor het instellen van de eigenschap van een bericht
- TTS heeft bindingen toegevoegd voor Java (Jre en Android), Python, Swift en Objective-C
- TTS heeft ondersteuning voor afspelen toegevoegd voor macOS, iOS en Android.
- Toegevoegd "woord grens" informatie voor TTS.

**Opgeloste fouten**

- Fixed IL2CPP build issue on Unity 2019 for Android
- Probleem opgelost met verkeerd gevormde kopteksten in wav-bestandsinvoer die onjuist worden verwerkt
- Probleem opgelost met uu-id's die niet uniek zijn in sommige verbindingseigenschappen
- Fixed a few warnings about nullability specifiers in the Swift bindings (might require small code changes) Fixed a few warnings about nullability specifiers in the Swift bindings (might require small code changes) Fixed a few warnings about nullability specifiers in the Swift bindings (might require small code changes) Fixed a
- Een bug verholpen waardoor websocketverbindingen onsierlijk werden gesloten onder netwerkbelasting
- Fixed an issue on Android that sometimes results in duplicate impression IDs used by`DialogServiceConnector`
- Verbeteringen in de stabiliteit van verbindingen in multi-turn interacties `Canceled` en het melden van storingen (via gebeurtenissen) wanneer ze optreden met`DialogServiceConnector`
- `DialogServiceConnector`start van de sessie biedt nu `ListenOnceAsync()` goed evenementen, ook wanneer u belt tijdens een actieve`StartKeywordRecognitionAsync()`
- Een crash verholpen `DialogServiceConnector` die verband houdt met de ontvangen activiteiten

**Monsters**

- Snel start voor Xamarin
- Bijgewerkt CPP Quickstart met Linux ARM64 informatie
- Quickstart van Unity bijgewerkt met iOS-informatie

## <a name="speech-sdk-160-2019-june-release"></a>Speech SDK 1.6.0: release 2019-juni

**Monsters**

- Quickstart samples voor Text To Speech op UWP en Unity
- Quickstart-voorbeeld voor Swift op iOS
- Unity samples voor Speech & Intent Recognition and Translation
- Updated quickstart samples for Updated quickstart samples for Updated quickstart samples for Updated quick`DialogServiceConnector`

**Verbeteringen / Veranderingen**

- Naamruimte in dialoogvenster:
  - De naam van `SpeechBotConnector` is gewijzigd in `DialogServiceConnector`
  - De naam van `BotConfig` is gewijzigd in `DialogServiceConfig`
  - `BotConfig::FromChannelSecret()`is remapped naar`DialogServiceConfig::FromBotSecret()`
  - Alle bestaande Direct Line Speech clients worden nog steeds ondersteund na de naamswijziging
- TTS REST-adapter bijwerken om proxy, permanente verbinding te ondersteunen
- Foutbericht verbeteren wanneer een ongeldig gebied wordt doorgegeven
- Swift/Objective-C:
  - Verbeterde foutrapportage: methoden die tot een fout kunnen leiden, zijn nu `NSError` aanwezig in twee versies: een object dat een object blootstelt voor foutafhandeling en een die een uitzondering oproept. De eerste zijn blootgesteld aan Swift. Deze wijziging vereist aanpassingen aan de bestaande Swift-code.
  - Verbeterde afhandeling van gebeurtenissen

**Opgeloste fouten**

- Oplossing voor TTS: waar `SpeakTextAsync` de toekomst terugkeerde zonder te wachten tot audio is voltooid rendering
- Oplossing voor het marshalen van tekenreeksen in C# om volledige taalondersteuning in te schakelen
- Oplossing voor het probleem van de .NET-kernapp om kernbibliotheek te laden met net461-doelkader in voorbeelden
- Problemen oplossen voor incidentele problemen om native bibliotheken te implementeren in de uitvoermap in voorbeelden
- Oplossing voor het op betrouwbare wijze sluiten van de websocket
- Fix voor mogelijke crash tijdens het openen van een verbinding onder zeer zware belasting op Linux
- Oplossing voor ontbrekende metadata in de frameworkbundel voor macOS
- Oplossing voor `pip install --user` problemen met op Windows

## <a name="speech-sdk-151"></a>Spraak SDK 1.5.1

Dit is een bug fix release en alleen van invloed op de native / managed SDK. Het is niet van invloed op de JavaScript-versie van de SDK.

**Opgeloste fouten**

- Fix FromSubscription bij gebruik met Conversation Transcription.
- Fix bug in keyword spotting for voice assistants.

## <a name="speech-sdk-150-2019-may-release"></a>Speech SDK 1.5.0: release 2019-mei

**Nieuwe functies**

- Keyword spotting (KWS) is nu beschikbaar voor Windows en Linux. KWS-functionaliteit kan werken met elk microfoontype, officiële KWS-ondersteuning is momenteel echter beperkt tot de microfoonarrays in de Azure Kinect DK-hardware of de Speech Devices SDK.
- Phrase hint functionaliteit is beschikbaar via de SDK. Voor meer informatie, zie [hier](how-to-phrase-lists.md).
- Gesprekstranscriptiefunctionaliteit is beschikbaar via de SDK. Zie [hier](conversation-transcription-service.md).
- Ondersteuning voor spraakassistenten toevoegen met behulp van het kanaal Direct Line Spraak.

**Monsters**

- Voorbeelden toegevoegd voor nieuwe functies of nieuwe services die worden ondersteund door de SDK.

**Verbeteringen / Veranderingen**

- Verschillende eigenschappen van de herkenning toegevoegd om servicegedrag of serviceresultaten aan te passen (zoals maskeren en anderen).
- U de herkenning nu configureren via de standaardconfiguratie-eigenschappen, zelfs als u de herkennings. `FromEndpoint`
- Doelstelling-C: `OutputFormat` eigendom is `SPXSpeechConfiguration`toegevoegd aan .
- De SDK ondersteunt nu Debian 9 als Linux-distributie.

**Opgeloste fouten**

- Een probleem opgelost waarbij de luidsprekerbron te vroeg werd vernietigd in tekst-naar-spraak.

## <a name="speech-sdk-142"></a>Spraak SDK 1.4.2

Dit is een bug fix release en alleen van invloed op de native / managed SDK. Het is niet van invloed op de JavaScript-versie van de SDK.

## <a name="speech-sdk-141"></a>Spraak SDK 1.4.1

Dit is een JavaScript-only release. Er zijn geen functies toegevoegd. De volgende correcties werden gemaakt:

- Voorkomen dat het webpack https-proxy-agent laadt.

## <a name="speech-sdk-140-2019-april-release"></a>Speech SDK 1.4.0: release 2019-april

**Nieuwe functies**

- De SDK ondersteunt nu de tekst-naar-spraakservice als bètaversie. Het wordt ondersteund op Windows en Linux Desktop van C++ en C#. Voor meer informatie raadpleegt u het [overzicht van tekst naar spraak](text-to-speech.md#get-started).
- De SDK ondersteunt nu MP3- en Opus/OGG-audiobestanden als streaminvoerbestanden. Deze functie is alleen beschikbaar op Linux van C ++ en C # en is momenteel in bèta (meer details [hier](how-to-use-codec-compressed-audio-input-streams.md)).
- De Speech SDK voor Java, .NET core, C++ en Objective-C hebben macOS-ondersteuning gekregen. De Objective-C-ondersteuning voor macOS is momenteel in bèta.
- iOS: De Speech SDK voor iOS (Objective-C) wordt nu ook gepubliceerd als CocoaPod.
- JavaScript: Ondersteuning voor niet-standaard microfoon als invoerapparaat.
- JavaScript: Proxy-ondersteuning voor Node.js.

**Monsters**

- Er zijn voorbeelden toegevoegd voor het gebruik van de Speech SDK met C++ en met Objective-C op macOS.
- Voorbeelden die het gebruik van de tekst-naar-spraakservice aantonen, zijn toegevoegd.

**Verbeteringen / Veranderingen**

- Python: Extra eigenschappen van herkenningsresultaten `properties` worden nu via de eigenschap weergegeven.
- Voor extra ondersteuning voor ontwikkeling en foutopsporing u informatie over SDK-logboekregistratie en diagnostische gegevens omleiden naar een logbestand (meer details [hier).](how-to-use-logging.md)
- JavaScript: Verbeter de prestaties van audioverwerking.

**Opgeloste fouten**

- Mac/iOS: Een bug die leidde tot een lange wachttijd toen een verbinding met de Spraakservice niet kon worden vastgesteld, werd opgelost.
- Python: verbeter foutafhandeling voor argumenten in Python-callbacks.
- JavaScript: Fixed wrong state reporting for speech ended on RequestSession.

## <a name="speech-sdk-131-2019-february-refresh"></a>Speech SDK 1.3.1: refresh 2019-februari

Dit is een bug fix release en alleen van invloed op de native / managed SDK. Het is niet van invloed op de JavaScript-versie van de SDK.

**Bug fix**

- Fixed a memory leak when using microphone input. Stream-gebaseerde of bestandsinvoer wordt niet beïnvloed.

## <a name="speech-sdk-130-2019-february-release"></a>Speech SDK 1.3.0: release 2019-februari

**Nieuwe functies**

- De Speech SDK ondersteunt de selectie `AudioConfig` van de invoermicrofoon via de klasse. Hiermee u audiogegevens streamen naar de spraakservice vanuit een niet-standaardmicrofoon. Zie de documentatie waarin de selectie van [audioinvoerapparaten](how-to-select-audio-input-devices.md)wordt beschreven voor meer informatie. Deze functie is nog niet beschikbaar via JavaScript.
- De Speech SDK ondersteunt Unity nu in een bètaversie. Geef feedback via de uitgiftesectie in de [GitHub-voorbeeldopslagplaats.](https://aka.ms/csspeech/samples) Deze release ondersteunt Unity op Windows x86 en x64 (desktop of Universal Windows Platform-toepassingen) en Android (ARM32/64, x86). Meer informatie is beschikbaar in onze [Unity quickstart.](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=unity)
- Het `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` bestand (verzonden in eerdere releases) is niet meer nodig. De functionaliteit is nu geïntegreerd in de core SDK.

**Monsters**

De volgende nieuwe content is beschikbaar in onze [sample repository:](https://aka.ms/csspeech/samples)

- Aanvullende monsters `AudioConfig.FromMicrophoneInput`voor .
- Aanvullende Python-voorbeelden voor intentieherkenning en -vertaling.
- Aanvullende voorbeelden voor `Connection` het gebruik van het object in iOS.
- Extra Java-samples voor vertaling met audio-uitvoer.
- Nieuw voorbeeld voor gebruik van de [Batch Transcription REST API](batch-transcription.md).

**Verbeteringen / Veranderingen**

- Python
  - Verbeterde parameterverificatie en foutmeldingen in `SpeechConfig`.
  - Ondersteuning voor `Connection` het object toevoegen.
  - Ondersteuning voor 32-bits Python (x86) op Windows.
  - De SpraakSDK voor Python is uit bèta.
- iOS
  - De SDK is nu gebouwd tegen de iOS SDK versie 12.1.
  - De SDK ondersteunt nu iOS-versies 9.2 en hoger.
  - Verbeter de referentiedocumentatie en los verschillende eigenschapsnamen op.
- JavaScript
  - Ondersteuning voor `Connection` het object toevoegen.
  - Tekstdefinitiebestanden toevoegen voor gebundelde JavaScript
  - Eerste ondersteuning en implementatie voor zinszin.
  - Ophalen van eigenschappen retourneren met service JSON voor herkenning
- Windows DLL's bevatten nu wel een versiebron.
- Als u een `FromEndpoint` herkenningspunt maakt, u parameters rechtstreeks toevoegen aan de URL van het eindpunt. Met `FromEndpoint` behulp van u u de herkenning niet configureren via de standaardconfiguratie-eigenschappen.

**Opgeloste fouten**

- Lege proxy gebruikersnaam en proxy wachtwoord werden niet correct behandeld. Met deze release, als u proxy gebruikersnaam en proxy wachtwoord ingesteld op een lege string, zullen ze niet worden ingediend bij het aansluiten op de proxy.
- SessionId's gemaakt door de SDK waren niet&nbsp;altijd echt willekeurig voor sommige talen / omgevingen. Toegevoegd willekeurige generator initialisatie om dit probleem op te lossen.
- Verbetering van de afhandeling van autorisatietoken. Als u een autorisatietoken wilt gebruiken, geeft u in de `SpeechConfig` optie op en laat u de abonnementssleutel leeg. Maak vervolgens de herkennings.
- In sommige `Connection` gevallen is het object niet correct vrijgegeven. Dit probleem is opgelost.
- Het JavaScript-voorbeeld werd ook op Safari vastgesteld om audio-uitvoer voor vertaalsynthese te ondersteunen.

## <a name="speech-sdk-121"></a>Spraak SDK 1.2.1

Dit is een JavaScript-only release. Er zijn geen functies toegevoegd. De volgende correcties werden gemaakt:

- Vuur einde van de stroom op turn.end, niet bij speech.end.
- Fix bug in audio pomp die niet van plan volgende te sturen als de huidige verzenden mislukt.
- Fix continuous recognition with auth token.
- Bug fix voor verschillende herkenningspunten/ eindpunten.
- Documentatieverbeteringen.

## <a name="speech-sdk-120-2018-december-release"></a>Speech SDK 1.2.0: release 2018-december

**Nieuwe functies**

- Python
  - De bètaversie van Python-ondersteuning (3.5 en hoger) is beschikbaar met deze release. Zie hier voor meer informatie(quickstart-python.md).
- JavaScript
  - De SpraakSDK voor JavaScript is open-source. De broncode is beschikbaar op [GitHub.](https://github.com/Microsoft/cognitive-services-speech-sdk-js)
  - We ondersteunen nu Node.js, meer info is [hier](quickstart-js-node.md)te vinden.
  - De lengtebeperking voor audiosessies is verwijderd, de verbinding gebeurt automatisch onder de hoes.
- `Connection`Object
  - Vanaf `Recognizer`de , kunt `Connection` u toegang tot een object. Met dit object u de serviceverbinding expliciet starten en u abonneren om gebeurtenissen te verbinden en los te koppelen.
    (Deze functie is nog niet beschikbaar via JavaScript en Python.)
- Ondersteuning voor Ubuntu 18.04.
- Android
  - ProGuard-ondersteuning tijdens de APK-generatie.

**Verbeteringen**

- Verbeteringen in het interne draadgebruik, waardoor het aantal threads, sloten, mutexes wordt verminderd.
- Verbeterde foutrapportage / informatie. In verschillende gevallen zijn foutmeldingen niet helemaal naar buiten gepropageerd.
- Bijgewerkte ontwikkelingsafhankelijkheden in JavaScript om up-to-date modules te gebruiken.

**Opgeloste fouten**

- Vaste geheugenlekken als gevolg `RecognizeAsync`van een type mismatch in .
- In sommige gevallen werden uitzonderingen gelekt.
- Geheugenlek herstellen in argumenten voor vertaalgebeurtenissen.
- Fixed a locking issue on reconnect in long running sessies.
- Fixed an issue that could lead to missing final result for failed translations.
- C#: Als `async` een bewerking niet werd verwacht in de hoofdthread, was het mogelijk dat de herkenningkon worden verwijderd voordat de async-taak was voltooid.
- Java: Een probleem opgelost wat resulteert in een crash van de Java VM.
- Doelstelling-C: Vaste enummapping; RecognizedIntent werd geretourneerd `RecognizingIntent`in plaats van .
- JavaScript: stel de standaarduitvoerindeling `SpeechConfig`in op 'eenvoudig' in .
- JavaScript: inconsistentie tussen eigenschappen op het config-object in JavaScript en andere talen verwijderen.

**Monsters**

- Bijgewerkt en vaste verschillende monsters (bijvoorbeeld output stemmen voor vertaling, enz.).
- Node.js-voorbeelden toegevoegd in de [monsteropslagplaats](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-110"></a>Spraak SDK 1.1.0

**Nieuwe functies**

- Ondersteuning voor Android x86/x64.
- Proxy-ondersteuning: `SpeechConfig` In het object u nu een functie aanroepen om de proxy-informatie in te stellen (hostnaam, poort, gebruikersnaam en wachtwoord). Deze functie is nog niet beschikbaar op iOS.
- Verbeterde foutcode en berichten. Als een melding een fout heeft `Reason` geretourneerd, is deze `CancellationDetails` al ingesteld (in geannuleerde gebeurtenis) of (in het resultaat van de erkenning) ingesteld op `Error`. Het geannuleerde evenement bevat nu `ErrorCode` `ErrorDetails`twee extra leden en . Als de server extra foutgegevens met de gerapporteerde fout heeft geretourneerd, is deze nu beschikbaar in de nieuwe leden.

**Verbeteringen**

- Extra verificatie toegevoegd in de configuratie van de herkenningsfunctie en extra foutbericht toegevoegd.
- Verbeterde afhandeling van langdurige stilte in het midden van een audiobestand.
- NuGet-pakket: voor .NET Framework-projecten voorkomt het bouwen met AnyCPU-configuratie.

**Opgeloste fouten**

- Fixed several exceptions found in recognizers. Bovendien worden uitzonderingen gevangen en `Canceled` omgezet in gebeurtenis.
- Repareer een geheugenlek in property management.
- Fixed bug in which an audio input file could crash the recognizeer.
- Fixed a bug where events could be received after a session stop event.
- Fixed some race conditions in threading.
- Fixed an iOS compatibility issue that could result in a crash.
- Stabiliteitsverbeteringen voor Android-microfoonondersteuning.
- Fixed a bug where a recognizeer in JavaScript would ignore the recognition language.
- Fixed a bug preventing setting the `EndpointId` (in some cases) in JavaScript.
- Gewijzigde parametervolgorde in AddIntent in JavaScript en ontbrekende `AddIntent` JavaScript-handtekening toegevoegd.

**Monsters**

- C++ en C#-samples toegevoegd voor pull- en pushstreamgebruik in de [sample repository.](https://aka.ms/csspeech/samples)

## <a name="speech-sdk-101"></a>Spraak SDK 1.0.1

Verbeteringen in betrouwbaarheid en bugfixes:

- Vaste potentiële fatale fout als gevolg van ras conditie in het verwijderen van recognizer
- Fixed potential fatal error in issue of unset properties.
- Extra fout- en parametercontrole toegevoegd.
- Doelstelling-C: Mogelijke fatale fout opgelost veroorzaakt door naamoverschrijding in NSString.
- Doelstelling-C: Aangepaste zichtbaarheid van API
- JavaScript: Opgelost met betrekking tot gebeurtenissen en hun payloads.
- Documentatieverbeteringen.

In onze [voorbeeldrepository](https://aka.ms/csspeech/samples)is een nieuw voorbeeld voor JavaScript toegevoegd.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Cognitive Services Speech SDK 1.0.0: release 2018-september

**Nieuwe functies**

- Ondersteuning voor Objective-C op iOS. Bekijk onze [Objective-C quickstart voor iOS.](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone-langs/objectivec-ios.md)
- Ondersteuning voor JavaScript in de browser. Bekijk onze [JavaScript quickstart](quickstart-js-browser.md).

**Wijzigingen doorbreken**

- Met deze release worden een aantal baanbrekende wijzigingen geïntroduceerd.
  Kijk op [deze pagina](https://aka.ms/csspeech/breakingchanges_1_0_0) voor meer informatie.

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Cognitive Services Speech SDK 0.6.0: release 2018-augustus

**Nieuwe functies**

- UWP-apps die met de Speech SDK zijn gebouwd, kunnen nu de Windows Kit voor app-certificering (WACK) passeren.
  Bekijk de [UWP quickstart.](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp&tabs=uwp)
- Ondersteuning voor .NET Standard 2.0 op Linux (Ubuntu 16.04 x64).
- Experimenteel: Ondersteuning java 8 op Windows (64-bits) en Linux (Ubuntu 16.04 x64).
  Bekijk de [Java Runtime Environment snelaan.](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=jre)

**Functionele wijziging**

- Aanvullende foutdetailgegevens over verbindingsfouten blootleggen.

**Wijzigingen doorbreken**

- Op Java (Android) vereist de `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` functie geen padparameter meer. Nu wordt het pad automatisch gedetecteerd op alle ondersteunde platforms.
- Het get-accessoire van `EndpointUrl` het pand in Java en C# is verwijderd.

**Opgeloste fouten**

- In Java wordt het audiosyntheseresultaat op de vertaalherkenninger nu geïmplementeerd.
- Fixed a bug that could cause inactive threads and a increased number of open and unused sockets.
- Een probleem opgelost, waarbij een langdurige herkenning in het midden van de transmissie kon worden beëindigd.
- Fixed a race condition in recognizeer shutdown.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Cognitive Services Speech SDK 0.5.0: release 2018-juli

**Nieuwe functies**

- Ondersteuning Android-platform (API 23: Android 6.0 Marshmallow of hoger). Bekijk de [Android quickstart](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=android).
- Ondersteuning voor .NET Standard 2.0 op Windows. Bekijk de [.NET Core quickstart](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore).
- Experimenteel: Ondersteuning voor UWP op Windows (versie 1709 of hoger).
  - Bekijk de [UWP quickstart.](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)
  - Opmerking: UWP-apps die met de Speech SDK zijn gebouwd, slagen nog niet voor de Windows Kit voor app-certificering (WACK).
- Ondersteuning voor langdurige herkenning met automatische heraansluiting.

**Functionele wijzigingen**

- `StartContinuousRecognitionAsync()`ondersteunt langdurige herkenning.
- Het herkenningsresultaat bevat meer velden. Ze worden gecompenseerd met het begin en de duur van de audio (beide in teken) `InitialSilenceTimeout` van `InitialBabbleTimeout`de herkende tekst en extra waarden die de herkenningsstatus vertegenwoordigen, bijvoorbeeld, en .
- Ondersteuning AuthorizationToken voor het maken van fabrieksexemplaren.

**Wijzigingen doorbreken**

- Herkenningsgebeurtenissen: `NoMatch` gebeurtenistype is `Error` samengevoegd in de gebeurtenis.
- SpeechOutputFormat in C# is `OutputFormat` omgedoopt tot om uitgelijnd te blijven met C++.
- Het retourtype van sommige `AudioInputStream` methoden van de interface is enigszins gewijzigd:
  - In Java `read` wordt de `long` methode `int`nu geretourneerd in plaats van .
  - In C#, `Read` de `uint` methode `int`wordt nu geretourneerd in plaats van .
  - In C++ `Read` keren `GetFormat` de en `size_t` methoden `int`nu terug in plaats van .
- C++: Exemplaren van audio-invoerstreams kunnen `shared_ptr`nu alleen worden doorgegeven als een .

**Opgeloste fouten**

- Fixed incorrect return values `RecognizeAsync()` in the result when times out.
- De afhankelijkheid van mediafoundation-bibliotheken op Windows is verwijderd. De SDK maakt nu gebruik van Core Audio API's.
- Documentatieoplossing: een [regiopagina](regions.md) toegevoegd om de ondersteunde regio's te beschrijven.

**Bekend probleem**

- De SpraakSDK voor Android rapporteert geen spraaksyntheseresultaten voor vertaling. Dit probleem zal worden opgelost in de volgende release.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Cognitive Services Speech SDK 0.4.0: release 2018-juni

**Functionele wijzigingen**

- AudioInputStream

  Een herkenningsspeler kan nu een stream als audiobron gebruiken. Zie voor meer informatie de bijbehorende [handleiding.](how-to-use-audio-input-streams.md)

- Gedetailleerde uitvoernotatie

  Wanneer u `SpeechRecognizer`een , `Detailed` kunt `Simple` u aanvragen of uitvoer formaat. Het `DetailedSpeechRecognitionResult` bevat een betrouwbaarheidsscore, erkende tekst, ruwe lexicale vorm, genormaliseerde vorm, en genormaliseerde vorm met gemaskerde godslastering.

**Verandering doorbreken**

- Gewijzigd `SpeechRecognitionResult.Text` in `SpeechRecognitionResult.RecognizedText` van in C#.

**Opgeloste fouten**

- Fixed a possible callback issue in the USP layer during shutdown.
- Als een herkenningsspeler een audio-invoerbestand heeft verbruikt, houdt het de bestandsgreep langer vast dan nodig is.
- Verwijderd verschillende impasses tussen het bericht pomp en de recognizer.
- Brand `NoMatch` een resultaat wanneer de reactie van de dienst is time-out.
- De media foundation bibliotheken op Windows zijn vertraging geladen. Deze bibliotheek is alleen vereist voor microfooninvoer.
- De uploadsnelheid voor audiogegevens is beperkt tot ongeveer twee keer de oorspronkelijke audiosnelheid.
- Op Windows, C # .NET assemblages zijn nu sterk genoemd.
- Documentatieoplossing: `Region` is vereist informatie om een herkenningspunt te maken.

Meer monsters zijn toegevoegd en worden voortdurend bijgewerkt. Zie de [GitHub-bewaarplaats voor](https://aka.ms/csspeech/samples)de laatste reeks voorbeelden.

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Cognitive Services Speech SDK 0.2.12733: 2018-mei release

Deze release is de eerste publieke preview release van de Cognitive Services Speech SDK.
