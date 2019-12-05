---
title: Release opmerkingen-spraak service
titleSuffix: Azure Cognitive Services
description: Zie een actief logboek van functie releases, verbeteringen, fout oplossingen en bekende problemen voor de spraak service.
services: cognitive-services
author: BrianMouncer
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: brianem
ms.custom: seodec18
ms.openlocfilehash: 74b34a1d65f6beb097a649658c508da6ff48f1ac
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816603"
---
# <a name="release-notes"></a>Releaseopmerkingen

## <a name="speech-sdk-180-2019-november-release"></a>Speech SDK 1.8.0:2019-november release

**Nieuwe functies**

- Er is een FromHost ()-API toegevoegd om gebruik te vereenvoudigen met on-premises containers en soevereine Clouds.
- Automatische bron Taaldetectie toegevoegd voor spraak herkenning (in Java en C++)
- Er is een SourceLanguageConfig-object toegevoegd voor spraak herkenning, dat wordt gebruikt om de verwachte C++bron talen op te geven (in Java en)
- KeywordRecognizer ondersteuning voor Windows (UWP), Android en iOS is toegevoegd via de Nuget-en Unity-pakketten
- De Java-API voor externe conversaties is toegevoegd aan de conversatie transcriptie in asynchrone batches.

**Belangrijke wijzigingen**

- De functionaliteit van de conversatie-transcriber is verplaatst onder de naam ruimte micro soft. CognitiveServices. speech. transcriptie.
- Een deel van de audio transcriber-methoden worden verplaatst naar de nieuwe conversatie klasse.
- Ondersteuning voor verloren voor 32-bits (ARMv7 en x86) iOS

**Oplossingen voor problemen**

- Corrigeren voor vastlopen als lokale KeywordRecognizer wordt gebruikt zonder een geldige abonnements sleutel voor spraak Services

**Voorbeelden**

- Xamarin-voor beeld voor KeywordRecognizer
- Unit-voor beeld voor KeywordRecognizer
- C++en Java-voor beelden voor automatische bron Taaldetectie.

## <a name="speech-sdk-170-2019-september-release"></a>Speech SDK 1.7.0:2019-release van september

**Nieuwe functies**

- Bèta-ondersteuning toegevoegd voor Xamarin op Universeel Windows-platform (UWP), Android en iOS
- Ondersteuning voor iOS toegevoegd voor Unity
- Ondersteuning voor gecomprimeerde invoer toegevoegd voor ALaw, mulaw, FLAC op Android, iOS en Linux
- SendMessageAsync toegevoegd in de verbindings klasse voor het verzenden van een bericht naar de service
- SetMessageProperty toegevoegd in de verbindings klasse voor het instellen van de eigenschap van een bericht
- Aan TTS toegevoegde bindingen voor Java (jre en Android), Python, SWIFT en objectief-C
- TTS heeft ondersteuning voor afspelen toegevoegd voor macOS, iOS en Android.
- Informatie over woord grens toegevoegd voor TTS.

**Oplossingen voor problemen**

- Probleem met vast IL2CPP-Build op unit 2019 voor Android
- Probleem opgelost met onjuiste headers in WAV-bestand invoer wordt onjuist verwerkt
- Er is een probleem opgelost met UUIDs die niet uniek zijn in sommige verbindings eigenschappen
- Enkele waarschuwingen over de specificaties van nulwaarden in de SWIFT-bindingen zijn opgelost (hiervoor zijn mogelijk kleine code wijzigingen vereist)
- Er is een fout opgelost waardoor WebSocket-verbindingen zonder problemen kunnen worden gesloten onder netwerk belasting
- Er is een probleem opgelost in Android dat soms resulteert in dubbele indruk-Id's die worden gebruikt door DialogServiceConnector
- Verbeteringen in de stabiliteit van verbindingen tussen multi-turn-interacties en de rapportage van fouten (via geannuleerde gebeurtenissen) wanneer deze zich voordoen met DialogServiceConnector
- DialogServiceConnector-sessie start nu op juiste wijze gebeurtenissen, zoals bij het aanroepen van ListenOnceAsync () tijdens een actieve StartKeywordRecognitionAsync ()
- Een crash gericht op het ontvangen van DialogServiceConnector-activiteiten

**Voorbeelden**

- Quick start voor Xamarin
- Update CPP Snelstartgids met Linux ARM64 Information
- Quick start voor Unity update met iOS-gegevens

## <a name="speech-sdk-160-2019-june-release"></a>Speech SDK 1.6.0:2019-juni release

**Voorbeelden**

- Quick start-voor beelden voor tekst-naar-spraak op UWP en eenheid
- Voor beeld van Quick start voor Swift op iOS
- Unit-voor beelden voor spraak & Intentieherkenning en omzetting
- Bijgewerkte Quick start-voor beelden voor DialogServiceConnector

**Verbeteringen/wijzigingen**

- Naam ruimte dialoog venster:
  - De naam van de SpeechBotConnector is gewijzigd in DialogServiceConnector
  - De naam van de BotConfig is gewijzigd in DialogServiceConfig
  - BotConfig::FromChannelSecret() has been remapped to DialogServiceConfig::FromBotSecret()
  - Alle bestaande clients met directe spraak blijven worden ondersteund na de naamswijziging
- TTS REST-Adapter bijwerken ter ondersteuning van proxy, permanente verbinding
- Fout bericht verbeteren wanneer een ongeldige regio is door gegeven
- Swift/Objective-C:
  - Verbeterde fout rapportage: methoden die kunnen resulteren in een fout, zijn nu beschikbaar in twee versies: een waarmee een `NSError`-object wordt weer gegeven voor het afhandelen van fouten en één waarmee een uitzonde ring wordt gegenereerd. De voormalige worden weer gegeven aan SWIFT. Deze wijziging vereist aanpassingen in bestaande SWIFT-code.
  - Verbeterde verwerking van gebeurtenissen

**Oplossingen voor problemen**

- Fix voor TTS: waar SpeakTextAsync toekomst wordt geretourneerd zonder te wachten totdat de rendering van audio is voltooid
- Oplossing voor het Mars hallen C# van teken reeksen in om ondersteuning voor volledige taal in te scha kelen
- Oplossing voor een probleem met de .NET core-app voor het laden van de kern bibliotheek met net461 Target Framework in samples
- Problemen oplossen om systeem eigen bibliotheken in voor beelden te implementeren in de uitvoermap
- Oplossing voor het sluiten van de WebSocket is betrouwbaar
- Oplossing voor mogelijke crash tijdens het openen van een verbinding onder zeer zware belasting op Linux
- Oplossing voor ontbrekende meta gegevens in de Framework bundel voor macOS
- Problemen oplossen met `pip install --user` op Windows

## <a name="speech-sdk-151"></a>Speech SDK 1.5.1

Dit is een release van de oplossing voor fouten en alleen van invloed op de systeem eigen/beheerde SDK. Dit heeft geen invloed op de Java script-versie van de SDK.

**Oplossingen voor problemen**

- Herstel FromSubscription wanneer het wordt gebruikt met de conversatie-transcriptie.
- Los de fout op in trefwoord herkennen voor spraak assistenten.

## <a name="speech-sdk-150-2019-may-release"></a>Speech SDK 1.5.0:2019-mei release

**Nieuwe functies**

- Trefwoord herkennen (KWS) is nu beschikbaar voor Windows en Linux. KWS-functionaliteit kan worden gebruikt met elk type microfoon, maar de officiële KWS-ondersteuning is momenteel beperkt tot de microfoon matrices die zijn gevonden in de Azure Kinect DK-hardware of de speech-apparaten SDK.
- De functionaliteit van de woordgroepen Hint is beschikbaar via de SDK. Zie [hier](how-to-phrase-lists.md)voor meer informatie.
- De functionaliteit van de conversatie transcriptie is beschikbaar via de SDK. [Hier](conversation-transcription-service.md)weer geven.
- Voeg ondersteuning toe voor spraak assistenten met behulp van het directe lijn spraak kanaal.

**Voorbeelden**

- Er zijn voor beelden toegevoegd voor nieuwe functies of nieuwe services die door de SDK worden ondersteund.

**Verbeteringen/wijzigingen**

- Er zijn verschillende kenmerken van de Recognizer toegevoegd om het service gedrag of de service resultaten aan te passen (zoals het maskeren van scheld woorden en andere).
- U kunt de herkenner nu configureren via de standaard configuratie-eigenschappen, zelfs als u de `FromEndpoint`Recognizer hebt gemaakt.
- Doel-C: `OutputFormat` eigenschap is toegevoegd aan SPXSpeechConfiguration.
- De SDK ondersteunt nu Debian 9 als een Linux-distributie.

**Oplossingen voor problemen**

- Er is een probleem opgelost waarbij de resource van de spreker te vroeg is afgezet in tekst-naar-spraak.

## <a name="speech-sdk-142"></a>Speech SDK 1.4.2

Dit is een release van de oplossing voor fouten en alleen van invloed op de systeem eigen/beheerde SDK. Dit heeft geen invloed op de Java script-versie van de SDK.

## <a name="speech-sdk-141"></a>Speech SDK 1.4.1

Dit is een alleen-Java script-versie. Er zijn geen functies toegevoegd. De volgende oplossingen zijn uitgevoerd:

- Voor komen dat het webpakket de HTTPS-proxy-agent laadt.

## <a name="speech-sdk-140-2019-april-release"></a>Speech SDK 1.4.0:2019-april release

**Nieuwe functies**

- De SDK ondersteunt nu de tekst-naar-spraak-service als een bèta versie. Het wordt ondersteund op het bureau blad van Windows C++ en C#Linux van en. Controleer het [tekst-naar-spraak-overzicht](text-to-speech.md#get-started-with-text-to-speech)voor meer informatie.
- De SDK ondersteunt nu MP3-en opus/OGG-audio bestanden als invoer bestanden voor streams. Deze functie is alleen beschikbaar op Linux van C++ en C# is momenteel in bèta (meer informatie hierover [).](how-to-use-codec-compressed-audio-input-streams.md)
- De Speech-SDK voor Java, .NET core C++ en de doel-C hebben macOS-ondersteuning verkregen. De doel-C-ondersteuning voor macOS bevindt zich momenteel in een bèta versie.
- iOS: de Speech SDK voor iOS (objectief-C) wordt nu ook gepubliceerd als een CocoaPod.
- Java script: ondersteuning voor niet-standaard microfoon als invoer apparaat.
- Java script: proxy-ondersteuning voor node. js.

**Voorbeelden**

- Er zijn voor beelden toegevoegd voor het C++ gebruik van de Speech SDK met en met de doel-C op macOS.
- Er zijn voor beelden toegevoegd waarmee het gebruik van de tekst-naar-spraak-service wordt gedemonstreerd.

**Verbeteringen/wijzigingen**

- Python: extra eigenschappen van herkennings resultaten worden nu weer gegeven via de eigenschap `properties`.
- Voor aanvullende ontwikkel-en probleemoplossings ondersteuning kunt u informatie over de SDK-logboek registratie en diagnostische gegevens omleiden naar een logboek bestand (meer informatie [hierover).](how-to-use-logging.md)
- Java script: Verbeter de prestaties van de audio verwerking.

**Oplossingen voor problemen**

- Mac/iOS: een fout die heeft geleid tot een lange wacht tijd wanneer een verbinding met de spraak service niet tot stand kan worden gebracht, is opgelost.
- Python: de fout afhandeling voor argumenten in python-retour aanroepen wordt verbeterd.
- Java script: onjuiste status rapportage voor spraak gestopt op RequestSession.

## <a name="speech-sdk-131-2019-february-refresh"></a>Speech SDK 1.3.1:2019-februari vernieuwen

Dit is een release van de oplossing voor fouten en alleen van invloed op de systeem eigen/beheerde SDK. Dit heeft geen invloed op de Java script-versie van de SDK.

**Bug oplossen**

- Er is een geheugenlek opgelost bij het gebruik van een microfoon invoer. Streamen of bestands invoer worden niet beïnvloed.

## <a name="speech-sdk-130-2019-february-release"></a>Speech SDK 1.3.0:2019-februari release

**Nieuwe functies**

- De Speech SDK ondersteunt het selecteren van de invoer microfoon via de AudioConfig-klasse. Zo kunt u audio gegevens streamen naar de spraak service vanuit een niet-standaard microfoon. Zie de documentatie over het [selecteren van audio-invoer apparaten](how-to-select-audio-input-devices.md)voor meer informatie. Deze functie is nog niet beschikbaar via Java script.
- De Speech SDK ondersteunt nu eenheid in een bèta versie. Feedback geven via de sectie probleem in de [github-voorbeeld opslagplaats](https://aka.ms/csspeech/samples). Deze versie ondersteunt eenheid op Windows x86-en x64-computers (desktop-of Universeel Windows-platform-toepassingen) en Android (ARM32/64, x86). Meer informatie is beschikbaar in onze [Quick](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=unity)start van onze unit.
- De bestands `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (geleverd in eerdere releases) is niet meer nodig. De functionaliteit is nu geïntegreerd in de core-SDK.

**Voorbeelden**

De volgende nieuwe inhoud is beschikbaar in onze [voorbeeld opslagplaats](https://aka.ms/csspeech/samples):

- Aanvullende voor beelden voor AudioConfig. FromMicrophoneInput.
- Aanvullende python-voor beelden voor intentie herkenning en-omzetting.
- Aanvullende voor beelden voor het gebruik van het verbindings object in iOS.
- Aanvullende Java-voor beelden voor vertaling met audio-uitvoer.
- Nieuw voor beeld voor het gebruik van de [batch-transcriptie rest API](batch-transcription.md).

**Verbeteringen/wijzigingen**

- Python
  - Verbeterde verificatie van de para meter en fout berichten in SpeechConfig.
  - Voeg ondersteuning toe voor het verbindings object.
  - Ondersteuning voor 32-bits python (x86) in Windows.
  - De spraak-SDK voor python valt buiten de bèta versie.
- iOS
  - De SDK is nu gebouwd op basis van de iOS SDK-versie 12,1.
  - De SDK ondersteunt nu iOS-versies 9,2 en hoger.
  - Verbeter de referentie documentatie en los diverse eigenschapnamen op.
- Javascript
  - Voeg ondersteuning toe voor het verbindings object.
  - Type definitie bestanden voor gebundelde java script toevoegen
  - Eerste ondersteuning en implementatie voor woordgroepen hints.
  - Verzameling eigenschappen retour neren met Service-JSON voor herkenning
- Windows-Dll's bevatten nu een versie bron.
- Als u een herkennings `FromEndpoint` maakt, kunt u de para meters rechtstreeks aan de eind punt-URL toevoegen. Met `FromEndpoint` kunt u de herkenner niet configureren via de standaard configuratie-eigenschappen.

**Oplossingen voor problemen**

- De lege proxy-gebruikers naam en het proxy wachtwoord zijn niet goed afgehandeld. Als u met deze versie proxy gebruikersnaam en proxy wachtwoord instelt op een lege teken reeks, worden ze niet verzonden wanneer er verbinding wordt gemaakt met de proxy.
- SessionId die door de SDK is gemaakt, is niet altijd echt wille keurig voor sommige talen&nbsp;/omgevingen. De initialisatie van de wille keurige Generator is toegevoegd om dit probleem op te lossen.
- De verwerking van het autorisatie token verbeteren. Als u een autorisatie token wilt gebruiken, geeft u in het SpeechConfig op en laat u de sleutel abonnement leeg. Maak vervolgens zoals gebruikelijk de herkenner.
- In sommige gevallen is het verbindings object niet correct vrijgegeven. Dit probleem is opgelost.
- Het Java script-voor beeld is opgelost voor de ondersteuning van audio-uitvoer voor vertaal synthese ook in Safari.

## <a name="speech-sdk-121"></a>Speech SDK 1.2.1

Dit is een alleen-Java script-versie. Er zijn geen functies toegevoegd. De volgende oplossingen zijn uitgevoerd:

- Fire end van de stroom op turn. End, niet op Speech. End.
- Los de fout op in een audio pomp die de volgende verzen ding niet heeft gepland als de huidige verzen ding is mislukt.
- Herstel doorlopende herkenning met verificatie token.
- Fout oplossing voor andere Recognizer/eind punten.
- Verbeteringen in de documentatie bij.

## <a name="speech-sdk-120-2018-december-release"></a>Speech SDK 1.2.0:2018-december release

**Nieuwe functies**

- Python
  - De bèta versie van python-ondersteuning (3,5 en hoger) is beschikbaar in deze release. Zie hier] (Quick Start-python.md) voor meer informatie.
- Javascript
  - De Speech SDK voor Java script is open source. De bron code is beschikbaar op [github](https://github.com/Microsoft/cognitive-services-speech-sdk-js).
  - Node. js wordt nu ondersteund. meer informatie vindt u [hier](quickstart-js-node.md).
  - De lengte beperking voor audio sessies is verwijderd. de verbinding wordt automatisch hersteld onder de dekking.
- Verbindings object
  - Vanuit de herkenner kunt u toegang krijgen tot een verbindings object. Met dit object kunt u de service verbinding expliciet initiëren en zich abonneren op verbinding maken en verbreken van gebeurtenissen.
    (Deze functie is nog niet beschikbaar vanuit Java script en python.)
- Ondersteuning voor Ubuntu 18,04.
- Android
  - Er is ondersteuning geboden voor proguard tijdens het genereren van APK.

**Verbeteringen**

- Verbeteringen in het gebruik van de interne thread, waardoor het aantal threads, vergren delingen en mutexen wordt verkleind.
- Verbeterde fout rapportage/-informatie. In verschillende gevallen zijn fout berichten niet helemaal door gegeven.
- Bijgewerkte ontwikkelings afhankelijkheden in Java script voor het gebruik van up-to-date-modules.

**Oplossingen voor problemen**

- Er zijn problemen met het geheugen opgelost vanwege een niet-overeenkomend type in RecognizeAsync.
- In sommige gevallen werden uitzonde ringen gelekt.
- Oplossen van geheugen lekkage in Vertaal gebeurtenis argumenten.
- Er is een vergrendelings probleem opgelost bij het opnieuw verbinden van langlopende sessies.
- Er is een probleem opgelost dat kan leiden tot een ontbrekend eind resultaat voor mislukte vertalingen.
- C#: Als er geen asynchrone bewerking in de hoofd thread is, was het mogelijk dat de herkenner werd verwijderd voordat de asynchrone taak werd voltooid.
- Java: er is een probleem opgelost dat resulteert in het vastlopen van de Java-VM.
- Doel-C: vaste Enum-toewijzing; RecognizedIntent is geretourneerd in plaats van RecognizingIntent.
- Java script: Stel de standaard uitvoer indeling in op ' Simple ' in SpeechConfig.
- Java script: inconsistentie verwijderen tussen eigenschappen van het configuratie object in Java script en andere talen.

**Voorbeelden**

- Er zijn verschillende voor beelden bijgewerkt en opgelost (bijvoorbeeld uitvoer stemmen voor vertaling, enz.).
- Er zijn voor beelden van node. js toegevoegd aan de voor [beeld-opslag plaats](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-110"></a>Speech SDK 1.1.0

**Nieuwe functies**

- Ondersteuning voor Android x86/x64.
- Proxy ondersteuning: in het SpeechConfig-object kunt u nu een functie aanroepen om de proxy gegevens in te stellen (hostnaam, poort, gebruikers naam en wacht woord). Deze functie is nog niet beschikbaar op iOS.
- Verbeterde foutcode en berichten. Als een erkenning heeft een fout geretourneerd, deze al ingesteld `Reason` (in geannuleerde gebeurtenis) of `CancellationDetails` (in herkenningsresultaat) naar `Error`. De geannuleerde gebeurtenis bevat nu twee aanvullende leden, `ErrorCode` en `ErrorDetails`. Als de server heeft aanvullende foutinformatie met de gemelde fout geretourneerd, is het nu zijn beschikbaar in de nieuwe leden.

**Verbeteringen**

- Aanvullende verificatie toegevoegd in de configuratie voor de herkenning en toegevoegde extra foutbericht.
- Verbeterde verwerking van ervaren stilte in het midden van een geluidsbestand.
- NuGet-pakket: .NET Framework-projecten, voorkomt u dat bouwen met configuratie/platform.

**Oplossingen voor problemen**

- Verschillende uitzonderingen gevonden in de kenmerken die zijn opgelost. Daarnaast worden uitzonde ringen onderschept en omgezet in geannuleerde gebeurtenis.
- Een geheugenlek in de eigenschap management oplossen.
- Probleem opgelost waarbij een audio-invoerbestand de herkenning kan vastlopen.
- Een bug opgelost waar de gebeurtenissen na een sessie stop-gebeurtenis kunnen worden ontvangen.
- Sommige racevoorwaarden in threading opgelost.
- Een iOS compatibiliteitsprobleem dat leiden een crash tot kan opgelost.
- Stabiliteitsverbeteringen voor ondersteuning van Android microfoon.
- Een bug opgelost waarbij een kenmerk in JavaScript de opname-taal zou negeren.
- Er is een bug te voorkomen dat de EndpointId instellen (in sommige gevallen) opgelost in JavaScript.
- Gewijzigde parametervolgorde in AddIntent in JavaScript en toegevoegde ontbrekende AddIntent JavaScript-code.

**Voorbeelden**

- C++ toegevoegd en C# samplea voor gebruik in de pull-abonnementen en push de stroom in de [voorbeeldopslagplaats](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-101"></a>Speech SDK 1.0.1

Verbeteringen van de betrouwbaarheid en oplossingen voor problemen:

- Vaste mogelijke fatale fout vanwege een racevoorwaarde in herkenning wordt verwijderd
- Vaste mogelijke fatale fout in het geval van niet-ingestelde eigenschappen.
- Toegevoegde extra fout en controleren van de parameter.
- Objective-c vaste mogelijk fatale fout veroorzaakt door de naam in NSString overschrijven.
- Objective-c aangepast zichtbaarheid van de API
- JavaScript: Vaste met betrekking tot de gebeurtenissen en de nuttige informatie.
- Verbeteringen in de documentatie bij.

In onze [voorbeeldopslagplaats](https://aka.ms/csspeech/samples), een nieuwe steekproef voor JavaScript is toegevoegd.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Cognitive Services Speech SDK 1.0.0: release 2018-September

**Nieuwe functies**

- Ondersteuning voor Objective-C in iOS. Bekijk onze [Objective-C-Snelstartgids voor iOS](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone-langs/objectivec-ios.md).
- Ondersteuning voor JavaScript in browser. Bekijk onze [JavaScript-snelstartgids](quickstart-js-browser.md).

**Belangrijke wijzigingen**

- In deze versie wordt een aantal belang rijke wijzigingen geïntroduceerd.
  Raadpleeg [Deze pagina](https://aka.ms/csspeech/breakingchanges_1_0_0) voor meer informatie.

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Cognitive Services Speech SDK 0.6.0: augustus 2018-release

**Nieuwe functies**

- UWP-apps die zijn gemaakt met de spraak-SDK nu kunnen de Windows App Certification Kit (WACK) doorgeven.
  Bekijk de [UWP-snelstartgids](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp&tabs=uwp).
- Ondersteuning voor .NET Standard 2.0 op Linux (Ubuntu 16.04 x 64).
- Experimentele: Ondersteuning voor Java 8 in Windows (64-bits) en Linux (Ubuntu 16.04 x 64).
  Bekijk de [Java Runtime Environment snelstartgids](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=jre).

**Functionele wijzigen**

- Aanvullende foutgegevens details op verbindingsfouten worden blootgesteld.

**Belangrijke wijzigingen**

- Java (Android), de `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` functie vereist niet langer een padparameter. Het pad wordt nu automatisch gedetecteerd op alle ondersteunde platforms.
- De get-accessor van de eigenschap `EndpointUrl` in Java en C# is verwijderd.

**Oplossingen voor problemen**

- In Java, is het resultaat audio synthese op de vertaling herkenning nu geïmplementeerd.
- Een opgelost dat leiden niet-actieve threads en een toenemend aantal open en niet-gebruikte sockets tot kan.
- Een probleem opgelost waar een langlopende opname in het midden van de overdracht kan beëindigd.
- Herkenning afsluiten, een zeldzame situatie vast.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Cognitive Services Speech SDK 0.5.0: juli 2018-release

**Nieuwe functies**

- Ondersteuning voor Android-platform (API 23: Android 6.0 Marshmallow of hoger). Bekijk de [Android snelstartgids](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=android).
- Ondersteuning voor .NET Standard 2.0 op Windows. Bekijk de [.NET Core-snelstartgids](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore).
- Experimentele: Ondersteuning voor UWP op Windows (versie 1709 of hoger).
  - Bekijk de [UWP-snelstartgids](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp).
  - Opmerking: UWP-apps die zijn gebouwd met de spraak-SDK niet nog door de Windows App Certification Kit (WACK).
- Ondersteuning voor langlopende met automatisch opnieuw verbinden.

**Functionele wijzigingen**

- `StartContinuousRecognitionAsync()` biedt ondersteuning voor langlopende herkenning.
- Het herkenningsresultaat bevat meer velden. Ze worden verschoven ten opzichte van de audio begin en de duur (zowel in tikken) van de herkende tekst en aanvullende waarden die staan voor herkenning van status, bijvoorbeeld `InitialSilenceTimeout` en `InitialBabbleTimeout`.
- Ondersteuning voor AuthorizationToken voor het maken van factory-exemplaren.

**Belangrijke wijzigingen**

- Herkenning van gebeurtenissen: NoMatch gebeurtenistype is samengevoegd met de fout-gebeurtenis.
- Uitvoerindeling om te blijven met C++ uitgelijnde is SpeechOutputFormat in C# gewijzigd.
- Het retourtype van sommige methoden van de `AudioInputStream` interface enigszins gewijzigd:
  - In Java, de `read` methode nu retourneert `long` in plaats van `int`.
  - In C#, de `Read` methode nu retourneert `uint` in plaats van `int`.
  - In C++ kunt de `Read` en `GetFormat` methoden nu terug `size_t` in plaats van `int`.
- C++: Exemplaren van invoer audiostreams nu kunnen worden doorgegeven als een `shared_ptr`.

**Oplossingen voor problemen**

- Onjuiste retourwaarden, het resultaat vast wanneer `RecognizeAsync()` een time-out optreedt.
- De afhankelijkheid van media foundation-bibliotheken op Windows is verwijderd. De SDK gebruikt nu Core Audio-API's.
- Documentatie fix: toegevoegd een [regio's](regions.md) pagina voor het beschrijven van de ondersteunde regio's.

**Bekend probleem**

- De spraak-SDK voor Android rapporteren niet spraak synthese resultaten voor vertaling. Dit probleem wordt opgelost in de volgende release.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Cognitive Services Speech SDK 0.4.0: release 2018-juni

**Functionele wijzigingen**

- AudioInputStream

  Een kenmerk kan nu een stroom gebruiken als de audiobron. Zie voor meer informatie het gerelateerde [gebruiksaanwijzing](how-to-use-audio-input-streams.md).

- De indeling van gedetailleerde uitvoer

  Bij het maken van een `SpeechRecognizer`, kunt u aanvragen `Detailed` of `Simple` uitvoerindeling. De `DetailedSpeechRecognitionResult` bevat een betrouwbaarheidsscore, herkende tekst, onbewerkte lexicale vorm, genormaliseerde formulier en genormaliseerde formulier met gemaskeerd grof taalgebruik.

**Belangrijke wijziging**

- Gewijzigd in `SpeechRecognitionResult.Text` van `SpeechRecognitionResult.RecognizedText` in C#.

**Oplossingen voor problemen**

- Een mogelijke retouraanroep-probleem opgelost in de laag USP tijdens het afsluiten.

- Als een kenmerk een geluidsbestand invoer gebruikt, is het bedrijf op naar de bestandsingang die langer dan nodig.

- Verschillende impassen tussen de pomp bericht en de herkenning verwijderd.

- Fire een `NoMatch` leiden tot wanneer het antwoord van service is een time-out.

- De media foundation-bibliotheken op Windows zijn vertraging geladen. Deze bibliotheek is vereist voor de microfoon alleen.

- De uploadsnelheid van audiogegevens is beperkt tot over twee keer de oorspronkelijke audio snelheid.

- Op Windows, zijn C# .NET-assembly's nu sterke naam.

- Documentatie fix: `Region` is vereiste informatie om te maken van een kenmerk.

Meer voorbeelden zijn toegevoegd en worden voortdurend bijgewerkt. Zie voor de meest recente set voorbeelden, de [spraak SDK voorbeelden van GitHub-opslagplaats](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Cognitive Services Speech SDK 0.2.12733: release 2018-mei

Deze release is de eerste openbare preview-versie van de Cognitive Services Speech SDK.
