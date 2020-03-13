---
title: Release opmerkingen-spraak service
titleSuffix: Azure Cognitive Services
description: Een actief logboek van de functie versies, verbeteringen, fout oplossingen en bekende problemen met betrekking tot de spraak service.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79219641"
---
# <a name="release-notes"></a>Releaseopmerkingen
## <a name="speech-sdk-1100-2020-february-release"></a>Speech SDK 1.10.0:2020-februari release

**Nieuwe functies**
 - Python-pakketten zijn toegevoegd ter ondersteuning van de nieuwe 3,8-versie van python.
 - Red Hat Enterprise Linux (RHEL)/CentOS 8 x64-ondersteuningC++( C#,, Java, python).
   > [!NOTE] 
   > Klanten moeten OpenSSL configureren volgens [deze instructies](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-openssl-linux).
 - Linux ARM32-ondersteuning voor Debian en Ubuntu.
 - DialogServiceConnector ondersteunt nu een optionele para meter ' bot ID ' op BotFrameworkConfig. Met deze para meter kunt u meerdere directe-regel spraak botsen met één Azure-spraak bron. Als de para meter niet is opgegeven, wordt de standaard-bot gebruikt (zoals wordt bepaald door de configuratie pagina voor direct-spraak kanaal).
 - DialogServiceConnector heeft nu een SpeechActivityTemplate-eigenschap. De inhoud van deze JSON-teken reeks wordt gebruikt door directe regel spraak om vooraf een breed scala aan ondersteunde velden te vullen in alle activiteiten die een bot met directe lijnen bereiken, inclusief activiteiten die automatisch zijn gegenereerd als reactie op gebeurtenissen als spraak herkenning.
 - TTS maakt nu gebruik van abonnements sleutel voor verificatie en vermindert de eerste byte latentie van het eerste synthese resultaat nadat een synthesizer is gemaakt.
 - Bijgewerkte modellen voor spraak herkenning voor 19 land instellingen voor een gemiddelde reductie van een woord fout van 18,6% (es-ES, es-MX, FR-CA, fr-FR, it-IT, ja-JP, ko-KR, punt-k, zh-CN, zh-HK, nb-NO, fi-FL, ru-RU, pl-PL, CA-ES, zh-TW, th-TH, pt-PT, tr-TR). De nieuwe modellen bieden aanzienlijke verbeteringen in meerdere domeinen, waaronder dicteer functies, Call-Center transcriptie en video indexerings scenario's.

**Oplossingen voor oplossingen**
 - Er is een probleem opgelost waarbij conversatie transcriber niet correct heeft gewacht in JAVA-Api's 
 - Android x86-emulator reparatie voor Xamarin [github-probleem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/363)
 - Ontbrekende toevoegen (ophalen | Set) eigenschaps methoden naar AudioConfig
 - Een TTS-fout oplossen waarbij de audioDataStream niet kan worden gestopt wanneer de verbinding is mislukt
 - Het gebruik van een eind punt zonder regio zou USP storingen voor conversatie-vertaler veroorzaken
 - Voor het genereren van een ID in universele Windows-toepassingen wordt nu een adequaat uniek GUID-algoritme gebruikt. het was voorheen en onbedoeld standaard ingesteld op een stubbed-implementatie die vaak conflicten veroorzaakt in grote sets met interacties.
 
 **Voorbeelden**
 - Unit-voor beeld voor het gebruik van Speech SDK met [Unity-microfoon en push-modus streaming](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/from-unitymicrophone)

**Andere wijzigingen**
 - [Documentatie voor OpenSSL-configuratie is bijgewerkt voor Linux](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-openssl-linux)

## <a name="speech-sdk-190-2020-january-release"></a>Speech SDK 1.9.0:2020-januari release

**Nieuwe functies**

- Conversatie met meerdere apparaten: u kunt meerdere apparaten verbinden met dezelfde spraak of op tekst gebaseerd gesprek en optioneel berichten vertalen die ertussen worden verzonden. Meer informatie vindt u in [dit artikel](multi-device-conversation.md). 
- Ondersteuning voor trefwoord herkenning is toegevoegd voor Android. aar-pakket en er is ondersteuning toegevoegd voor x86-en x64-versies. 
- Doel-C: `SendMessage`-en `SetMessageProperty`-methoden zijn toegevoegd aan het `Connection`-object. Raadpleeg [hier](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxconnection)de documentatie.
- De C++ TTS API ondersteunt nu `std::wstring` als synthese-tekst invoer, waarbij een wstring moet worden geconverteerd naar een teken reeks voordat deze wordt door gegeven aan de SDK. Bekijk [hier](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer#speaktextasync)meer informatie. 
- C#: [Taal-id](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-automatic-language-detection?pivots=programming-language-csharp) en [bron taal configuratie](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-specify-source-language?pivots=programming-language-csharp) zijn nu beschikbaar.
- Java script: een functie toegevoegd aan `Connection`-object om aangepaste berichten door te geven van de speech-service als call back `receivedServiceMessage`.
- Java script: er is ondersteuning toegevoegd voor `FromHost API` om het gebruik te vereenvoudigen met on-premises containers en soevereine Clouds. Raadpleeg [hier](speech-container-howto.md)de documentatie.
- Java script: we gaan nu `NODE_TLS_REJECT_UNAUTHORIZED` dankzij een bijdrage van [orgads](https://github.com/orgads). Bekijk [hier](https://github.com/microsoft/cognitive-services-speech-sdk-js/pull/75)meer informatie.


**Wijzigingen afbreken**

- `OpenSSL` is bijgewerkt naar versie 1.1.1 b en is statisch gekoppeld aan de Speech SDK core-bibliotheek voor Linux. Dit kan een storing veroorzaken als uw postvak in-`OpenSSL` niet is geïnstalleerd in de `/usr/lib/ssl` Directory in het systeem. Raadpleeg [onze documentatie](how-to-configure-openssl-linux.md) onder documenten voor spraak-SDK om het probleem te omzeilen.
- Het gegevens type dat is geretourneerd voor C# `WordLevelTimingResult.Offset`, is gewijzigd van `int` naar `long` om toegang te krijgen tot `WordLevelTimingResults` wanneer de spraak gegevens langer dan twee minuten zijn.
- `PushAudioInputStream` en `PullAudioInputStream` nu informatie over de WAV-header verzenden naar de spraak service op basis van `AudioStreamFormat`, eventueel opgegeven wanneer ze zijn gemaakt. Klanten moeten nu de [ondersteunde audio-invoer indeling](how-to-use-audio-input-streams.md)gebruiken. Andere indelingen krijgen de meest optimale herkennings resultaten of kunnen andere problemen veroorzaken. 


**Oplossingen voor oplossingen**

- Zie de `OpenSSL` update onder belang rijke wijzigingen hierboven. We hebben een crash en een prestatie probleem opgelost (conflicten met hoge belasting vergren delen) in Linux en Java. 
- Java: verbeteringen aangebracht in het sluiten van objecten in gelijktijdige scenario's.
- Het NuGet-pakket is herstructureeerd. We hebben de drie kopieën van `Microsoft.CognitiveServices.Speech.core.dll` en `Microsoft.CognitiveServices.Speech.extension.kws.dll` onder lib folders verwijderd, waardoor het NuGet-pakket kleiner en sneller kan worden gedownload en er zijn headers toegevoegd die C++ nodig zijn om bepaalde systeem eigen apps te compileren.
- [Hier vindt](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp)u voor beelden van vaste Quick Start. Deze zijn afgesloten zonder weer gave van de uitzonde ring ' microfoon niet gevonden ' op Linux, MacOS, Windows.
- Vaste SDK-crash met lange spraak herkennings resultaten voor bepaalde code paden zoals [dit voor beeld](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/csharp/uwp/speechtotext-uwp).
- Er is een vaste SDK-implementatie fout opgetreden in de Azure web app-omgeving om [deze klant probleem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/396)op te lossen
- Er is een fout met TTS opgelost tijdens het gebruik van de code voor meerdere `<voice>` of `<audio>` om [Dit probleem van de klant](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/433)op te lossen. 
- Er is een fout met TTS 401 opgelost tijdens het herstellen van de SDK vanuit Suspended.
- Java script: een circulaire invoer van audio gegevens is opgelost dankzij een bijdrage van [euirim](https://github.com/euirim). 
- Java script: ondersteuning toegevoegd voor het instellen van service-eigenschappen, zoals toegevoegd in 1,7.
- Java script: er is een probleem opgelost waarbij een verbindings fout kan leiden tot doorlopende, niet-geslaagde WebSocket-pogingen om verbinding te maken.


**Voorbeelden**

- Voor beeld van trefwoord herkenning voor [Android is](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/android/sdkdemo)toegevoegd.
- Er is [hier](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs)een voor beeld van TTS toegevoegd voor het Server scenario.
- De Snelstartgids voor multi-device- C# conversaties C++ [voor en zijn](quickstarts/multi-device-conversation.md)toegevoegd.


**Andere wijzigingen**

- Geoptimaliseerde grootte van de SDK-kern bibliotheek op Android.
- De SDK in 1.9.0 en vervolgens ondersteunt zowel `int` als `string` typen in het veld voor de spraak handtekening versie voor de conversatie-transcriber.

## <a name="speech-sdk-180-2019-november-release"></a>Speech SDK 1.8.0:2019-november release

**Nieuwe functies**

- Er is een `FromHost()`-API toegevoegd om gebruik te vereenvoudigen met on-premises containers en soevereine Clouds.
- Automatische bron Taaldetectie toegevoegd voor spraak herkenning (in Java en C++)
- `SourceLanguageConfig`-object toegevoegd voor spraak herkenning, gebruikt om de verwachte bron talen op te geven C++(in Java en)
- `KeywordRecognizer` ondersteuning voor Windows (UWP), Android en iOS is toegevoegd via de NuGet-en Unity-pakketten
- De Java-API voor externe conversaties is toegevoegd aan de conversatie transcriptie in asynchrone batches.

**Wijzigingen afbreken**

- De functies van de conversatie-transcriber zijn verplaatst onder naam ruimte `Microsoft.CognitiveServices.Speech.Transcription`.
- Een deel van de audio transcriber-methoden worden verplaatst naar de nieuwe `Conversation`-klasse.
- Ondersteuning voor verloren voor 32-bits (ARMv7 en x86) iOS

**Oplossingen voor oplossingen**

- Herstel voor crash als lokale `KeywordRecognizer` wordt gebruikt zonder een geldige abonnements sleutel voor spraak Services

**Voorbeelden**

- Xamarin-voor beeld voor `KeywordRecognizer`
- Unit-voor beeld voor `KeywordRecognizer`
- C++en Java-voor beelden voor automatische bron Taaldetectie.

## <a name="speech-sdk-170-2019-september-release"></a>Speech SDK 1.7.0:2019-release van september

**Nieuwe functies**

- Bèta-ondersteuning toegevoegd voor Xamarin op Universeel Windows-platform (UWP), Android en iOS
- Ondersteuning voor iOS toegevoegd voor Unity
- Er is `Compressed` invoer ondersteuning toegevoegd voor ALaw, mulaw, FLAC op Android, iOS en Linux
- `SendMessageAsync` toegevoegd in `Connection` klasse voor het verzenden van een bericht naar de service
- `SetMessageProperty` in `Connection` klasse toegevoegd voor het instellen van de eigenschap van een bericht
- Aan TTS toegevoegde bindingen voor Java (jre en Android), Python, SWIFT en objectief-C
- TTS heeft ondersteuning voor afspelen toegevoegd voor macOS, iOS en Android.
- Informatie over woord grens toegevoegd voor TTS.

**Oplossingen voor oplossingen**

- Probleem met vast IL2CPP-Build op unit 2019 voor Android
- Probleem opgelost met onjuiste headers in WAV-bestand invoer wordt onjuist verwerkt
- Er is een probleem opgelost met UUIDs die niet uniek zijn in sommige verbindings eigenschappen
- Enkele waarschuwingen over de specificaties van nulwaarden in de SWIFT-bindingen zijn opgelost (hiervoor zijn mogelijk kleine code wijzigingen vereist)
- Er is een fout opgelost waardoor WebSocket-verbindingen zonder problemen kunnen worden gesloten onder netwerk belasting
- Er is een probleem opgelost in Android dat soms resulteert in dubbele indruk-Id's die worden gebruikt door `DialogServiceConnector`
- Verbeteringen in de stabiliteit van verbindingen tussen multi-turn-interacties en de rapportage van fouten (via `Canceled` gebeurtenissen) wanneer deze zich voordoen met `DialogServiceConnector`
- `DialogServiceConnector` sessie wordt nu op de juiste manier voorzien van gebeurtenissen, zoals wanneer het aanroepen van `ListenOnceAsync()` tijdens een actieve `StartKeywordRecognitionAsync()`
- Een crash gericht op het ontvangen van `DialogServiceConnector` activiteiten

**Voorbeelden**

- Quick start voor Xamarin
- Update CPP Snelstartgids met Linux ARM64 Information
- Quick start voor Unity update met iOS-gegevens

## <a name="speech-sdk-160-2019-june-release"></a>Speech SDK 1.6.0:2019-juni release

**Voorbeelden**

- Quick start-voor beelden voor tekst-naar-spraak op UWP en eenheid
- Voor beeld van Quick start voor Swift op iOS
- Unit-voor beelden voor spraak & Intentieherkenning en omzetting
- Bijgewerkte Quick start-voor beelden voor `DialogServiceConnector`

**Verbeteringen/wijzigingen**

- Naam ruimte dialoog venster:
  - De naam van `SpeechBotConnector` is gewijzigd in `DialogServiceConnector`
  - De naam van `BotConfig` is gewijzigd in `DialogServiceConfig`
  - `BotConfig::FromChannelSecret()` is opnieuw toegewezen aan `DialogServiceConfig::FromBotSecret()`
  - Alle bestaande clients met directe spraak blijven worden ondersteund na de naamswijziging
- TTS REST-Adapter bijwerken ter ondersteuning van proxy, permanente verbinding
- Fout bericht verbeteren wanneer een ongeldige regio is door gegeven
- Swift/Objective-C:
  - Verbeterde fout rapportage: methoden die kunnen resulteren in een fout, zijn nu beschikbaar in twee versies: een waarmee een `NSError`-object wordt weer gegeven voor het afhandelen van fouten en één waarmee een uitzonde ring wordt gegenereerd. De voormalige worden weer gegeven aan SWIFT. Deze wijziging vereist aanpassingen in bestaande SWIFT-code.
  - Verbeterde verwerking van gebeurtenissen

**Oplossingen voor oplossingen**

- Fix voor TTS: waar `SpeakTextAsync` toekomstig wordt geretourneerd zonder te wachten totdat de rendering van audio is voltooid
- Oplossing voor het Mars hallen C# van teken reeksen in om ondersteuning voor volledige taal in te scha kelen
- Oplossing voor een probleem met de .NET core-app voor het laden van de kern bibliotheek met net461 Target Framework in samples
- Problemen oplossen om systeem eigen bibliotheken in voor beelden te implementeren in de uitvoermap
- Oplossing voor het sluiten van de WebSocket is betrouwbaar
- Oplossing voor mogelijke crash tijdens het openen van een verbinding onder zeer zware belasting op Linux
- Oplossing voor ontbrekende meta gegevens in de Framework bundel voor macOS
- Problemen oplossen met `pip install --user` op Windows

## <a name="speech-sdk-151"></a>Speech SDK 1.5.1

Dit is een release van de oplossing voor fouten en alleen van invloed op de systeem eigen/beheerde SDK. Dit heeft geen invloed op de Java script-versie van de SDK.

**Oplossingen voor oplossingen**

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
- Doel-C: `OutputFormat` eigenschap is toegevoegd aan `SPXSpeechConfiguration`.
- De SDK ondersteunt nu Debian 9 als een Linux-distributie.

**Oplossingen voor oplossingen**

- Er is een probleem opgelost waarbij de resource van de spreker te vroeg is afgezet in tekst-naar-spraak.

## <a name="speech-sdk-142"></a>Speech SDK 1.4.2

Dit is een release van de oplossing voor fouten en alleen van invloed op de systeem eigen/beheerde SDK. Dit heeft geen invloed op de Java script-versie van de SDK.

## <a name="speech-sdk-141"></a>Speech SDK 1.4.1

Dit is een alleen-Java script-versie. Er zijn geen functies toegevoegd. De volgende oplossingen zijn uitgevoerd:

- Voor komen dat het webpakket de HTTPS-proxy-agent laadt.

## <a name="speech-sdk-140-2019-april-release"></a>Speech SDK 1.4.0:2019-april release

**Nieuwe functies**

- De SDK ondersteunt nu de tekst-naar-spraak-service als een bèta versie. Het wordt ondersteund op het bureau blad van Windows C++ en C#Linux van en. Controleer het [tekst-naar-spraak-overzicht](text-to-speech.md#get-started)voor meer informatie.
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

**Oplossingen voor oplossingen**

- Mac/iOS: een fout die heeft geleid tot een lange wacht tijd wanneer een verbinding met de spraak service niet tot stand kan worden gebracht, is opgelost.
- Python: de fout afhandeling voor argumenten in python-retour aanroepen wordt verbeterd.
- Java script: onjuiste status rapportage voor spraak gestopt op RequestSession.

## <a name="speech-sdk-131-2019-february-refresh"></a>Speech SDK 1.3.1:2019-februari vernieuwen

Dit is een release van de oplossing voor fouten en alleen van invloed op de systeem eigen/beheerde SDK. Dit heeft geen invloed op de Java script-versie van de SDK.

**Bug oplossen**

- Er is een geheugenlek opgelost bij het gebruik van een microfoon invoer. Streamen of bestands invoer worden niet beïnvloed.

## <a name="speech-sdk-130-2019-february-release"></a>Speech SDK 1.3.0:2019-februari release

**Nieuwe functies**

- De Speech SDK ondersteunt het selecteren van de invoer microfoon via de `AudioConfig` klasse. Zo kunt u audio gegevens streamen naar de spraak service vanuit een niet-standaard microfoon. Zie de documentatie over het [selecteren van audio-invoer apparaten](how-to-select-audio-input-devices.md)voor meer informatie. Deze functie is nog niet beschikbaar via Java script.
- De Speech SDK ondersteunt nu eenheid in een bèta versie. Feedback geven via de sectie probleem in de [github-voorbeeld opslagplaats](https://aka.ms/csspeech/samples). Deze versie ondersteunt eenheid op Windows x86-en x64-computers (desktop-of Universeel Windows-platform-toepassingen) en Android (ARM32/64, x86). Meer informatie is beschikbaar in onze [Quick](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=unity)start van onze unit.
- De bestands `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (geleverd in eerdere releases) is niet meer nodig. De functionaliteit is nu geïntegreerd in de core-SDK.

**Voorbeelden**

De volgende nieuwe inhoud is beschikbaar in onze [voorbeeld opslagplaats](https://aka.ms/csspeech/samples):

- Aanvullende voor beelden voor `AudioConfig.FromMicrophoneInput`.
- Aanvullende python-voor beelden voor intentie herkenning en-omzetting.
- Aanvullende voor beelden voor het gebruik van het `Connection`-object in iOS.
- Aanvullende Java-voor beelden voor vertaling met audio-uitvoer.
- Nieuw voor beeld voor het gebruik van de [batch-transcriptie rest API](batch-transcription.md).

**Verbeteringen/wijzigingen**

- Python
  - Verbeterde verificatie van de para meter en fout berichten in `SpeechConfig`.
  - Voeg ondersteuning toe voor het object `Connection`.
  - Ondersteuning voor 32-bits python (x86) in Windows.
  - De spraak-SDK voor python valt buiten de bèta versie.
- iOS
  - De SDK is nu gebouwd op basis van de iOS SDK-versie 12,1.
  - De SDK ondersteunt nu iOS-versies 9,2 en hoger.
  - Verbeter de referentie documentatie en los diverse eigenschapnamen op.
- Javascript
  - Voeg ondersteuning toe voor het object `Connection`.
  - Type definitie bestanden voor gebundelde java script toevoegen
  - Eerste ondersteuning en implementatie voor woordgroepen hints.
  - Verzameling eigenschappen retour neren met Service-JSON voor herkenning
- Windows-Dll's bevatten nu een versie bron.
- Als u een herkennings `FromEndpoint` maakt, kunt u de para meters rechtstreeks aan de eind punt-URL toevoegen. Met `FromEndpoint` kunt u de herkenner niet configureren via de standaard configuratie-eigenschappen.

**Oplossingen voor oplossingen**

- De lege proxy-gebruikers naam en het proxy wachtwoord zijn niet goed afgehandeld. Als u met deze versie proxy gebruikersnaam en proxy wachtwoord instelt op een lege teken reeks, worden ze niet verzonden wanneer er verbinding wordt gemaakt met de proxy.
- SessionId die door de SDK is gemaakt, is niet altijd echt wille keurig voor sommige talen&nbsp;/omgevingen. De initialisatie van de wille keurige Generator is toegevoegd om dit probleem op te lossen.
- De verwerking van het autorisatie token verbeteren. Als u een autorisatie token wilt gebruiken, geeft u in de `SpeechConfig` op en laat u de sleutel abonnement leeg. Maak vervolgens zoals gebruikelijk de herkenner.
- In sommige gevallen is het `Connection`-object niet correct vrijgegeven. Dit probleem is opgelost.
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
- `Connection`-object
  - Vanuit het `Recognizer`kunt u toegang krijgen tot een `Connection`-object. Met dit object kunt u de service verbinding expliciet initiëren en zich abonneren op verbinding maken en verbreken van gebeurtenissen.
    (Deze functie is nog niet beschikbaar vanuit Java script en python.)
- Ondersteuning voor Ubuntu 18,04.
- Android
  - Er is ondersteuning geboden voor proguard tijdens het genereren van APK.

**Rijke**

- Verbeteringen in het gebruik van de interne thread, waardoor het aantal threads, vergren delingen en mutexen wordt verkleind.
- Verbeterde fout rapportage/-informatie. In verschillende gevallen zijn fout berichten niet helemaal door gegeven.
- Bijgewerkte ontwikkelings afhankelijkheden in Java script voor het gebruik van up-to-date-modules.

**Oplossingen voor oplossingen**

- Er zijn problemen met het geheugen opgelost vanwege een niet-overeenkomend type in `RecognizeAsync`.
- In sommige gevallen werden uitzonde ringen gelekt.
- Oplossen van geheugen lekkage in Vertaal gebeurtenis argumenten.
- Er is een vergrendelings probleem opgelost bij het opnieuw verbinden van langlopende sessies.
- Er is een probleem opgelost dat kan leiden tot een ontbrekend eind resultaat voor mislukte vertalingen.
- C#: Als een `async` bewerking niet in de hoofd thread is gewacht, zou de herkenner mogelijk kunnen worden verwijderd voordat de asynchrone taak werd voltooid.
- Java: er is een probleem opgelost dat resulteert in het vastlopen van de Java-VM.
- Doel-C: vaste Enum-toewijzing; RecognizedIntent is geretourneerd in plaats van `RecognizingIntent`.
- Java script: Stel de standaard uitvoer indeling in op ' Simple ' in `SpeechConfig`.
- Java script: inconsistentie verwijderen tussen eigenschappen van het configuratie object in Java script en andere talen.

**Voorbeelden**

- Er zijn verschillende voor beelden bijgewerkt en opgelost (bijvoorbeeld uitvoer stemmen voor vertaling, enz.).
- Er zijn voor beelden van node. js toegevoegd aan de voor [beeld-opslag plaats](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-110"></a>Speech SDK 1.1.0

**Nieuwe functies**

- Ondersteuning voor Android x86/x64.
- Proxy ondersteuning: in het `SpeechConfig`-object kunt u nu een functie aanroepen om de proxy gegevens in te stellen (hostnaam, poort, gebruikers naam en wacht woord). Deze functie is nog niet beschikbaar op iOS.
- Verbeterde foutcode en berichten. Als een herkenning een fout retourneert, is dit al ingesteld `Reason` (in geannuleerde gebeurtenis) of `CancellationDetails` (in het erkennings resultaat) om `Error`. De geannuleerde gebeurtenis bevat nu twee extra leden: `ErrorCode` en `ErrorDetails`. Als de server heeft aanvullende foutinformatie met de gemelde fout geretourneerd, is het nu zijn beschikbaar in de nieuwe leden.

**Rijke**

- Aanvullende verificatie toegevoegd in de configuratie voor de herkenning en toegevoegde extra foutbericht.
- Verbeterde verwerking van ervaren stilte in het midden van een geluidsbestand.
- NuGet-pakket: .NET Framework-projecten, voorkomt u dat bouwen met configuratie/platform.

**Oplossingen voor oplossingen**

- Verschillende uitzonderingen gevonden in de kenmerken die zijn opgelost. Daarnaast worden uitzonde ringen onderschept en omgezet in `Canceled` gebeurtenis.
- Een geheugenlek in de eigenschap management oplossen.
- Probleem opgelost waarbij een audio-invoerbestand de herkenning kan vastlopen.
- Een bug opgelost waar de gebeurtenissen na een sessie stop-gebeurtenis kunnen worden ontvangen.
- Sommige racevoorwaarden in threading opgelost.
- Een iOS compatibiliteitsprobleem dat leiden een crash tot kan opgelost.
- Stabiliteitsverbeteringen voor ondersteuning van Android microfoon.
- Een bug opgelost waarbij een kenmerk in JavaScript de opname-taal zou negeren.
- Er is een fout opgelost waardoor de `EndpointId` (in sommige gevallen) in Java script niet kan worden ingesteld.
- De parameter volgorde is gewijzigd in AddIntent in Java script en de ontbrekende `AddIntent` java script-hand tekening is toegevoegd.

**Voorbeelden**

- Toegevoegde C++ en C# voor beelden voor het verzamelen en het gebruik van push streams in de voor [beeld-opslag plaats](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-101"></a>Speech SDK 1.0.1

Verbeteringen van de betrouwbaarheid en oplossingen voor problemen:

- Vaste mogelijke fatale fout vanwege een racevoorwaarde in herkenning wordt verwijderd
- Vaste mogelijke fatale fout in het geval van niet-ingestelde eigenschappen.
- Toegevoegde extra fout en controleren van de parameter.
- Objective-c vaste mogelijk fatale fout veroorzaakt door de naam in NSString overschrijven.
- Objective-c aangepast zichtbaarheid van de API
- JavaScript: Vaste met betrekking tot de gebeurtenissen en de nuttige informatie.
- Verbeteringen in de documentatie bij.

In onze [voorbeeld opslagplaats](https://aka.ms/csspeech/samples)is een nieuw voor beeld voor Java script toegevoegd.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Cognitive Services Speech SDK 1.0.0: release 2018-September

**Nieuwe functies**

- Ondersteuning voor Objective-C in iOS. Bekijk onze [doel stelling-C Quick start voor IOS](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone-langs/objectivec-ios.md).
- Ondersteuning voor JavaScript in browser. Bekijk onze [Snelstartgids voor Java script](quickstart-js-browser.md).

**Wijzigingen afbreken**

- In deze versie wordt een aantal belang rijke wijzigingen geïntroduceerd.
  Raadpleeg [Deze pagina](https://aka.ms/csspeech/breakingchanges_1_0_0) voor meer informatie.

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Cognitive Services Speech SDK 0.6.0: augustus 2018-release

**Nieuwe functies**

- UWP-apps die zijn gemaakt met de spraak-SDK nu kunnen de Windows App Certification Kit (WACK) doorgeven.
  Bekijk de Quick Start van [UWP](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp&tabs=uwp).
- Ondersteuning voor .NET Standard 2.0 op Linux (Ubuntu 16.04 x 64).
- Experimentele: Ondersteuning voor Java 8 in Windows (64-bits) en Linux (Ubuntu 16.04 x 64).
  Bekijk de [Java Runtime Environment Snelstartgids](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=jre).

**Functionele wijziging**

- Aanvullende foutgegevens details op verbindingsfouten worden blootgesteld.

**Wijzigingen afbreken**

- Op Java (Android) vereist de functie `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` geen para meter Path meer. Het pad wordt nu automatisch gedetecteerd op alle ondersteunde platforms.
- De Get-accessor van de eigenschap `EndpointUrl` in Java en C# is verwijderd.

**Oplossingen voor oplossingen**

- In Java, is het resultaat audio synthese op de vertaling herkenning nu geïmplementeerd.
- Een opgelost dat leiden niet-actieve threads en een toenemend aantal open en niet-gebruikte sockets tot kan.
- Een probleem opgelost waar een langlopende opname in het midden van de overdracht kan beëindigd.
- Herkenning afsluiten, een zeldzame situatie vast.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Cognitive Services Speech SDK 0.5.0: juli 2018-release

**Nieuwe functies**

- Ondersteuning voor Android-platform (API 23: Android 6.0 Marshmallow of hoger). Bekijk de [Android-Quick](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=android)start.
- Ondersteuning voor .NET Standard 2.0 op Windows. Bekijk de [Snelstartgids voor .net core](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore).
- Experimentele: Ondersteuning voor UWP op Windows (versie 1709 of hoger).
  - Bekijk de Quick Start van [UWP](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp).
  - Opmerking: UWP-apps die zijn gebouwd met de spraak-SDK niet nog door de Windows App Certification Kit (WACK).
- Ondersteuning voor langlopende met automatisch opnieuw verbinden.

**Functionele wijzigingen**

- `StartContinuousRecognitionAsync()` ondersteunt langlopende herkenning.
- Het herkenningsresultaat bevat meer velden. Ze worden gecompenseerd vanaf het begin en de duur van de audio (zowel in Ticks) van de herkende tekst als aanvullende waarden die de herkennings status vertegenwoordigen, bijvoorbeeld `InitialSilenceTimeout` en `InitialBabbleTimeout`.
- Ondersteuning voor AuthorizationToken voor het maken van factory-exemplaren.

**Wijzigingen afbreken**

- Herkennings gebeurtenissen: `NoMatch` gebeurtenis type is samengevoegd in de `Error` gebeurtenis.
- De naam C# van de SpeechOutputFormat in is gewijzigd in `OutputFormat` om te C++blijven afstemt met.
- Het retour type van sommige methoden van de `AudioInputStream`-interface is enigszins gewijzigd:
  - In Java retourneert de methode `read` nu `long` in plaats van `int`.
  - In C#retourneert de methode `Read` nu `uint` in plaats van `int`.
  - In C++worden in de methoden `Read` en `GetFormat` nu `size_t` geretourneerd in plaats van `int`.
- C++: Exemplaren van audio-invoer stromen kunnen nu alleen worden door gegeven als een `shared_ptr`.

**Oplossingen voor oplossingen**

- Er zijn onjuiste retour waarden in het resultaat vastgelegd wanneer `RecognizeAsync()` een time-out optreedt.
- De afhankelijkheid van media foundation-bibliotheken op Windows is verwijderd. De SDK gebruikt nu Core Audio-API's.
- Documentatie oplossing: er is een pagina met [regio's](regions.md) toegevoegd om de ondersteunde regio's te beschrijven.

**Bekend probleem**

- De spraak-SDK voor Android rapporteren niet spraak synthese resultaten voor vertaling. Dit probleem wordt opgelost in de volgende release.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Cognitive Services Speech SDK 0.4.0: release 2018-juni

**Functionele wijzigingen**

- AudioInputStream

  Een kenmerk kan nu een stroom gebruiken als de audiobron. Zie de verwante [hand leiding](how-to-use-audio-input-streams.md)voor meer informatie.

- De indeling van gedetailleerde uitvoer

  Wanneer u een `SpeechRecognizer`maakt, kunt u `Detailed` of `Simple` uitvoer indeling aanvragen. Het `DetailedSpeechRecognitionResult` bevat een betrouw bare Score, herkende tekst, ruwe lexicale vorm, genormaliseerde vorm en genormaliseerde vorm met gemaskeerde scheld woorden.

**Laatste wijziging**

- Gewijzigd in `SpeechRecognitionResult.Text` van `SpeechRecognitionResult.RecognizedText` in C#.

**Oplossingen voor oplossingen**

- Een mogelijke retouraanroep-probleem opgelost in de laag USP tijdens het afsluiten.
- Als een kenmerk een geluidsbestand invoer gebruikt, is het bedrijf op naar de bestandsingang die langer dan nodig.
- Verschillende impassen tussen de pomp bericht en de herkenning verwijderd.
- Als er een time-out optreedt voor het antwoord van de service, treedt een `NoMatch` resultaat op.
- De media foundation-bibliotheken op Windows zijn vertraging geladen. Deze bibliotheek is vereist voor de microfoon alleen.
- De uploadsnelheid van audiogegevens is beperkt tot over twee keer de oorspronkelijke audio snelheid.
- Op Windows, zijn C# .NET-assembly's nu sterke naam.
- Documentatie oplossing: `Region` is een vereiste informatie om een herkenner te maken.

Meer voorbeelden zijn toegevoegd en worden voortdurend bijgewerkt. Zie voor de meest recente set voor beelden de [github-opslag plaats Speech SDK samples](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Cognitive Services Speech SDK 0.2.12733: release 2018-mei

Deze release is de eerste openbare preview-versie van de Cognitive Services Speech SDK.
