---
title: Aan de slag met de Bing Speech Recognition API met C# behulp van de bureau blad-bibliotheek | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Ontwikkel basis Windows-toepassingen die gebruikmaken van de Bing Speech Recognition API om gesp roken audio naar tekst te converteren.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 3160ccc1c8741d87fcee94a6face48551a79052d
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966901"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-c35-for-net-on-windows"></a>Quickstart: De Bing Speech Recognition API in C&#35; voor .net in Windows gebruiken

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Op deze pagina ziet u hoe u een eenvoudige Windows-toepassing kunt ontwikkelen die gebruikmaakt van de API voor spraak herkenning om gesp roken audio naar tekst te converteren. Door gebruik te maken van de client bibliotheek is realtime-streaming mogelijk, wat betekent dat wanneer uw client toepassing audio naar de service verzendt, gelijktijdig en asynchroon gedeeltelijke herkennings resultaten worden ontvangen.

Ontwikkel aars die spraak service willen gebruiken van apps die op een apparaat worden uitgevoerd, kunnen C# de bureau blad-bibliotheek gebruiken. Als u de bibliotheek wilt gebruiken, installeert u het [NuGet-pakket micro soft. ProjectOxford. SpeechRecognition-x86](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x86/) voor een 32-bits platform en het [NuGet-pakket micro soft. ProjectOxford. SpeechRecognition-x64](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x64/) voor een 64-bits platform. Zie [micro soft speech C# Desktop Library](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html)(ENGELSTALIG) voor de API-referentie van de client bibliotheek.

In de volgende secties wordt beschreven hoe u de C# voorbeeld toepassing installeert, bouwt en uitvoert met behulp van de C# bureau blad-bibliotheek.

## <a name="prerequisites"></a>Vereisten

### <a name="platform-requirements"></a>Platformvereisten

Het volgende voor beeld is ontwikkeld voor Windows 8 + en .NET Framework 4.5 + met behulp van [Visual Studio 2015, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs).

### <a name="get-the-sample-application"></a>De voorbeeld toepassing ophalen

Kloon het voor beeld vanuit [de C# bibliotheek voor beeld van de speech-Desktop Library](https://github.com/microsoft/cognitive-speech-stt-windows) .

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Abonneer u op de spraakherkennings-API en ontvang een gratis proef abonnement sleutel

De Speech-API maakt deel uit van Cognitive Services (voorheen project Oxford). U kunt abonnements sleutels voor een gratis proef versie downloaden van de pagina [Cognitive Services abonnement](https://azure.microsoft.com/try/cognitive-services/) . Nadat u de spraak-API hebt geselecteerd, selecteert u **API-sleutel ophalen** om de sleutel op te halen. Er wordt een primaire en secundaire sleutel geretourneerd. Beide sleutels zijn gekoppeld aan hetzelfde quotum, dus u kunt beide sleutels gebruiken.

> [!IMPORTANT]
> * Een abonnements sleutel ophalen. Voordat u de speech client-bibliotheken gebruikt, moet u een [abonnements sleutel](https://azure.microsoft.com/try/cognitive-services/)hebben.
>
> * Gebruik uw abonnements sleutel. Plak met de C# meegeleverde voorbeeld toepassing voor het bureau blad uw abonnements sleutel in het tekstvak wanneer u het voor beeld uitvoert. Zie [de voorbeeld toepassing uitvoeren](#step-3-run-the-sample-application)voor meer informatie.

## <a name="step-1-install-the-sample-application"></a>Stap 1: De voorbeeld toepassing installeren

1. Start Visual Studio 2015 en selecteer **bestand** > **openen** > **project/oplossing**.

2. Blader naar de map waar u de gedownloade spraakherkennings-API-bestanden hebt opgeslagen. Selecteer **Speech** > **Windows**en selecteer vervolgens de map sample-wp.

3. Dubbel klik om het bestand met de Visual Studio 2015-oplossing (. SLN) te openen met de naam SpeechToText-WPF-Samples. SLN. De oplossing wordt geopend in Visual Studio.

## <a name="step-2-build-the-sample-application"></a>Stap 2: De voorbeeld toepassing bouwen

1. Als u *herkenning met opzet*wilt gebruiken, moet u zich eerst registreren voor de [Language Understanding intelligent service (Luis)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/). Gebruik vervolgens de eind punt-URL van de Luis-app om de waarde van `LuisEndpointUrl` de sleutel in het bestand app. config in de map Samples/SpeechRecognitionServiceExample in te stellen. Zie [uw app publiceren](../../luis/luis-get-started-create-app.md#publish-your-app)voor meer informatie over de eind punt-URL van de Luis-app.

   > [!TIP]
   > Vervang het `&` teken in de `&amp;` URL van het Luis-eind punt door om ervoor te zorgen dat de URL correct wordt geïnterpreteerd door de XML-parser.

2. Druk op CTRL + SHIFT + B of selecteer **samen stellen** in het lint menu. Selecteer vervolgens **Build-oplossing**.

## <a name="step-3-run-the-sample-application"></a>Stap 3: De voorbeeldtoepassing uitvoeren

1. Wanneer de build is voltooid, drukt u op F5 of selecteert u **Start** in het lint menu om het voor beeld uit te voeren.

2. Ga naar het **voorbeeld venster Project Oxford speech to text** . Plak uw abonnements sleutel in het tekstvak **uw abonnements sleutel plakken** , zoals wordt weer gegeven. Als u uw abonnements sleutel op uw PC of laptop wilt behouden, selecteert u **sleutel opslaan**. Als u de abonnements sleutel van het systeem wilt verwijderen, selecteert u **sleutel verwijderen** om deze te verwijderen van uw PC of laptop.

   ![Toets voor het plakken van spraak herkenning](../Images/SpeechRecog_paste_key.PNG)

3. Kies onder **bron spraak herkenning**een van de zes spraak bronnen die in twee belang rijke invoer categorieën vallen:

   * Gebruik de microfoon van uw computer of een aangesloten microfoon om spraak vast te leggen.
   * Een audio bestand afspelen.

   Elke categorie heeft drie herkennings modi:

    * **ShortPhrase-modus**: Een utterance van Maxi maal 15 seconden lang. Wanneer gegevens naar de server worden verzonden, ontvangt de client meerdere gedeeltelijke resultaten en één eind resultaat met meerdere n-beste keuzes.
    * **LongDictation-modus**: Een utterance van Maxi maal twee minuten lang. Naarmate er gegevens naar de server worden verzonden, ontvangt de client meerdere gedeeltelijke resultaten en meerdere eind resultaten, op basis van waar de server een zin pauzeert.
    * **Detectie**van de intentie: De server retourneert aanvullende gestructureerde informatie over de spraak invoer. Als u de detectie van de intentie wilt gebruiken, moet u eerst een model trainen met behulp van [Luis](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

Gebruik voorbeeld audio bestanden met deze voorbeeld toepassing. Zoek de bestanden in de opslag plaats die u hebt gedownload met dit voor beeld in de map Samples/SpeechRecognitionServiceExample. Deze audio bestanden worden automatisch uitgevoerd als er geen andere bestanden worden gekozen wanneer u **WAV-bestand gebruiken voor de Shortphrase-modus** selecteert of als u een **WAV-bestand gebruikt voor de Longdictation-modus** als uw gesp roken invoer. Op dit moment wordt alleen WAV-audio-indeling ondersteund.

![Spraak naar tekst interface](../Images/HelloJones.PNG)

## <a name="samples-explained"></a>Voor beelden beschreven

### <a name="recognition-events"></a>Herkennings gebeurtenissen

* **Gedeeltelijke resultaten gebeurtenissen**: Deze gebeurtenis wordt elke keer dat de spraak service wordt opgeroepen, voor speld wat u kunt zeggen, zelfs voordat u klaar bent met `MicrophoneRecognitionClient`het spreken (als u gebruikt) of het `DataRecognitionClient`verzenden van gegevens (als u gebruikt).
* **Fout gebeurtenissen**: Wordt aangeroepen wanneer er een fout wordt gedetecteerd door de service.
* **Intentie gebeurtenissen**: Wordt aangeroepen op ' WithIntent-clients (alleen in de ShortPhrase-modus) nadat het uiteindelijke herkennings resultaat is geparseerd in een Structured JSON-doel.
* **Resultaat gebeurtenissen**:
  * In `ShortPhrase` de modus wordt deze gebeurtenis aangeroepen en retourneert de n-beste resultaten nadat u het spreken hebt voltooid.
  * In `LongDictation` de modus wordt de gebeurtenis-handler meerdere keren aangeroepen, op basis van waar de service zin-pauzes aanduidt.
  * **Voor elk van de n-beste keuzes**wordt een betrouw bare waarde en enkele andere vormen van de herkende tekst geretourneerd. Zie [output format](../Concepts.md#output-format)(Engelstalig) voor meer informatie.

Er zijn al gebeurtenis-handlers opgenomen in de code in de vorm van code opmerkingen.

## <a name="related-topics"></a>Verwante onderwerpen

* [Naslag informatie over micro soft speech Desktop Library](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html)
* [Aan de slag met de micro soft Speech Recognition API in Java op Android](GetStartedJavaAndroid.md)
* [Aan de slag met de micro soft Speech Recognition API in doelstelling-C op iOS](Get-Started-ObjectiveC-iOS.md)
* [Aan de slag met de micro soft Speech Recognition API in Java script](GetStartedJSWebsockets.md)
* [Aan de slag met de micro soft Speech Recognition API via REST](GetStartedREST.md)
