---
title: Aan de slag met de micro soft Speech Recognition API C# met behulp van de service bibliotheek | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Gebruik de Bing Speech Recognition service-bibliotheek om gesp roken taal te converteren naar tekst.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 71c3e471a8844eb6c6b70921e40c94338a084a8b
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965846"
---
# <a name="quickstart-use-the-bing-speech-recognition-service-library-in-c35-for-net-windows"></a>Quickstart: De bibliotheek voor de Bing Speech herkennings service&#35; gebruiken in C voor .net Windows

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

De service bibliotheek is voor ontwikkel aars die een eigen Cloud service hebben en die de spraak service vanuit hun service willen aanroepen. Als u de service voor spraak herkenning van apparaat-gebonden Apps wilt aanroepen, moet u deze SDK niet gebruiken. (Gebruik andere client bibliotheken of REST-Api's hiervoor.)

Als u de C# service bibliotheek wilt gebruiken, installeert u het [NuGet-pakket micro soft. Bing. speech](https://www.nuget.org/packages/Microsoft.Bing.Speech/). Zie de [micro soft speech C# service-bibliotheek](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html)voor naslag informatie over de bibliotheek-API.

In de volgende secties wordt beschreven hoe u de C# voorbeeld toepassing installeert, bouwt en uitvoert met behulp van de C# service bibliotheek.

## <a name="prerequisites"></a>Vereisten

### <a name="platform-requirements"></a>Platformvereisten

Het volgende voor beeld is ontwikkeld voor Windows 8 + en .NET 4.5 + Framework met behulp van [Visual Studio 2015, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs).

### <a name="get-the-sample-application"></a>De voorbeeld toepassing ophalen

Kloon het voor beeld in de voor beeld-opslag plaats van de [ C# Speech-Service bibliotheek](https://github.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary) .

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Abonneer u op de spraakherkennings-API en ontvang een gratis proef abonnement sleutel

De Speech-API maakt deel uit van Cognitive Services (voorheen project Oxford). U kunt abonnements sleutels voor een gratis proef versie downloaden van de pagina [Cognitive Services abonnement](https://azure.microsoft.com/try/cognitive-services/) . Nadat u de spraak-API hebt geselecteerd, selecteert u **API-sleutel ophalen** om de sleutel op te halen. Er wordt een primaire en secundaire sleutel geretourneerd. Beide sleutels zijn gekoppeld aan hetzelfde quotum, dus u kunt beide sleutels gebruiken.

> [!IMPORTANT]
> * Een abonnements sleutel ophalen. Voordat u de speech client-bibliotheken kunt gebruiken, moet u een [abonnements sleutel](https://azure.microsoft.com/try/cognitive-services/)hebben.
>
> * Gebruik uw abonnements sleutel. Met de opgegeven C# voorbeeld toepassing voor de service bibliotheek moet u uw abonnements sleutel opgeven als een van de opdracht regel parameters. Zie [de voorbeeld toepassing uitvoeren](#step-3-run-the-sample-application)voor meer informatie.

## <a name="step-1-install-the-sample-application"></a>Stap 1: De voorbeeld toepassing installeren

1. Start Visual Studio 2015 en selecteer **bestand** > **openen** > **project/oplossing**.

2. Dubbel klik om het bestand met de Visual Studio 2015-oplossing (. SLN) te openen met de naam SpeechClient. SLN. De oplossing wordt geopend in Visual Studio.

## <a name="step-2-build-the-sample-application"></a>Stap 2: De voorbeeld toepassing bouwen

Druk op CTRL + SHIFT + B of selecteer **samen stellen** in het lint menu. Selecteer vervolgens **Build-oplossing**.

## <a name="step-3-run-the-sample-application"></a>Stap 3: De voorbeeldtoepassing uitvoeren

1. Wanneer de build is voltooid, drukt u op F5 of selecteert u **Start** in het lint menu om het voor beeld uit te voeren.

2. Open de uitvoermap voor het voor beeld, bijvoorbeeld SpeechClientSample\bin\Debug. Druk op Shift + Klik met de rechter muisknop en selecteer **hier opdracht venster openen**.

3. Voer `SpeechClientSample.exe` uit met de volgende argumenten:

   * Arg[0]: Geef een WAV-bestand voor invoer op.
   * Arg[1]: Geef de land instellingen voor de audio op.
   * ARG [2]: De herkennings modi opgeven: *Kort* voor de `ShortPhrase` modus en *lang* voor de `LongDictation` modus.
   * Arg[3]: Geef de abonnements sleutel op voor toegang tot de service voor spraak herkenning.

## <a name="samples-explained"></a>Voor beelden beschreven

### <a name="recognition-modes"></a>Herkennings modi

* `ShortPhrase`penmodus Een utterance van Maxi maal 15 seconden lang. Wanneer gegevens naar de server worden verzonden, ontvangt de client meerdere gedeeltelijke resultaten en een uiteindelijk beste resultaat.
* `LongDictation`penmodus Een utterance van Maxi maal tien minuten lang. Naarmate er gegevens naar de server worden verzonden, ontvangt de client meerdere gedeeltelijke resultaten en meerdere eind resultaten, op basis van waar de server een zin pauzeert.

### <a name="supported-audio-formats"></a>Ondersteunde audio-indelingen

De spraak-API ondersteunt audio/WAV met behulp van de volgende codecs:

* PCM-één kanaal
* Siren
* SirenSR

### <a name="preferences"></a>Voorkeuren

Als u een SpeechClient wilt maken, moet u eerst een voor keuren-object maken. Het object Preferences is een set para meters die het gedrag van de spraak service configureren. Deze bestaat uit de volgende velden:

* `SpeechLanguage`: De land instelling van de audio die wordt verzonden naar de spraak service.
* `ServiceUri`: Het eind punt dat wordt gebruikt om de spraak service aan te roepen.
* `AuthorizationProvider`: Een IAuthorizationProvider-implementatie die wordt gebruikt voor het ophalen van tokens om toegang te krijgen tot de speech-service. Hoewel het voor beeld een Cognitive Services autorisatie provider biedt, wordt u ten zeerste aangeraden uw eigen implementatie te maken voor het afhandelen van token-caching.
* `EnableAudioBuffering`: Een geavanceerde optie. Zie [verbindings beheer](#connection-management).

### <a name="speech-input"></a>Spraak invoer

Het SpeechInput-object bestaat uit twee velden:

* **Audio**: Een stroom implementatie van uw keuze van waaruit de SDK audio haalt. Dit kan elke [stroom](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx) zijn die lees bewerkingen ondersteunt.
   > [!NOTE]
   > De SDK detecteert het einde van de stroom wanneer de stroom **0** in lezen retourneert.

* **RequestMetadata**: Meta gegevens over de spraak aanvraag. Zie de [verwijzing](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html)voor meer informatie.

### <a name="speech-request"></a>Spraak aanvraag

Nadat u een SpeechClient-en SpeechInput-object hebt gemaakt, gebruikt u RecognizeAsync om een aanvraag naar de speech-service te maken.

```cs
    var task = speechClient.RecognizeAsync(speechInput);
```

Nadat de aanvraag is voltooid, wordt de taak die is geretourneerd door RecognizeAsync voltooid. De laatste RecognitionResult is het einde van de herkenning. De taak kan mislukken als de service of de SDK onverwacht mislukt.

### <a name="speech-recognition-events"></a>Spraakherkennings gebeurtenissen

#### <a name="partial-results-event"></a>Gedeeltelijke resultaten gebeurtenis

Deze gebeurtenis wordt elke keer dat de spraak service wordt opgeroepen, voor speld wat u kunt zeggen, zelfs voordat u klaar bent met `MicrophoneRecognitionClient`het spreken (als u gebruikt) of het `DataRecognitionClient`verzenden van gegevens (als u gebruikt). U kunt zich abonneren op de gebeurtenis met `SpeechClient.SubscribeToPartialResult()`behulp van. U kunt ook de abonnements methode `SpeechClient.SubscribeTo<RecognitionPartialResult>()`generic Events gebruiken.

**Retour indeling** | Description |
------|------
**LexicalForm** | Dit formulier is optimaal voor gebruik door toepassingen die onbewerkte, niet-verwerkte spraak herkennings resultaten nodig hebben.
**DisplayText** | De herkende woord groep met inverse tekst normalisatie, hoofdletter gebruik, interpunctie en grove maskering toegepast. Scheld woorden worden gemaskeerd met sterretjes na het eerste teken, bijvoorbeeld "d * * *". " Dit formulier is optimaal voor gebruik door toepassingen waarin de resultaten van de spraak herkenning voor een gebruiker worden weer gegeven.
**Vallen** | Het vertrouwens niveau dat de herkende woord groep vertegenwoordigt voor de gekoppelde audio zoals gedefinieerd door de server voor spraak herkenning.
**MediaTime** | De huidige tijd ten opzichte van het begin van de audio stroom (in 100 nano seconden eenheden van tijd).
**MediaDuration** | De duur van de huidige zin/lengte ten opzichte van het audio segment (in 100-nano seconden eenheden van tijd).

#### <a name="result-event"></a>Resultaat gebeurtenis
Wanneer u klaar bent met spreken `ShortPhrase` (in modus), wordt deze gebeurtenis aangeroepen. U hebt een n-beste keuze voor het resultaat. In `LongDictation` de modus kan de gebeurtenis meerdere keren worden aangeroepen, op basis van waar de server een zin pauzeert. U kunt zich abonneren op de gebeurtenis met `SpeechClient.SubscribeToRecognitionResult()`behulp van. U kunt ook de abonnements methode `SpeechClient.SubscribeTo<RecognitionResult>()`generic Events gebruiken.

**Retour indeling** | Description |
------|------|
**RecognitionStatus** | De status van de manier waarop de herkenning is geproduceerd. Het is bijvoorbeeld geproduceerd als gevolg van een geslaagde herkenning of als gevolg van het annuleren van de verbinding, enzovoort.
**Zinnen** | De set n-best herkende woord groepen met het vertrouwens niveau van de herkenning.

Zie [uitvoer indeling](../Concepts.md#output-format)voor meer informatie over herkennings resultaten.

### <a name="speech-recognition-response"></a>Antwoord op spraak herkenning

Voor beeld van een spraak antwoord:
```
--- Partial result received by OnPartialResult  
---what  
--- Partial result received by OnPartialResult  
--what's  
--- Partial result received by OnPartialResult  
---what's the web  
--- Partial result received by OnPartialResult   
---what's the weather like  
---***** Phrase Recognition Status = [Success]   
***What's the weather like? (Confidence:High)  
What's the weather like? (Confidence:High)
```

## <a name="connection-management"></a>Verbindings beheer

De API maakt gebruik van één WebSocket-verbinding per aanvraag. Voor een optimale gebruikers ervaring probeert de SDK opnieuw verbinding te maken met de spraak service en wordt de herkenning gestart vanaf de laatste RecognitionResult die deze heeft ontvangen. Als de audio aanvraag bijvoorbeeld twee minuten lang is, heeft de SDK een RecognitionEvent bij het 1-minuten-merk ontvangen en een netwerk fout is opgetreden na vijf seconden, start de SDK een nieuwe verbinding die begint met de 1-minuten.

>[!NOTE]
>De SDK zoekt niet terug naar de markering van één minuut, omdat de stroom mogelijk geen ondersteuning biedt voor zoeken. In plaats daarvan behoudt de SDK een interne buffer die wordt gebruikt voor het bufferen van de audio en wordt de buffer gewist terwijl er RecognitionResult-gebeurtenissen worden ontvangen.

## <a name="buffering-behavior"></a>Buffer gedrag

Standaard buffert de SDK audio, zodat deze kan worden hersteld wanneer een netwerk onderbreking optreedt. In een scenario waarin het de voor keur heeft om de audio die verloren is gegaan tijdens het netwerk te verwijderen en de verbinding opnieuw te starten, is het het beste `EnableAudioBuffering` om audio buffering uit `false`te scha kelen door in het object voor keuren in te stellen op.

## <a name="related-topics"></a>Verwante onderwerpen

[Naslag informatie C# voor de micro soft Speech-Service bibliotheek](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html)
