---
title: Wat zijn de Speech Services?
titleSuffix: Azure Cognitive Services
description: De spraak Services zijn de combineert van spraak naar tekst, tekst naar spraak en spraak omzetting in één Azure-abonnement. Het is eenvoudig om spraak op uw toepassingen, hulpprogram ma's en apparaten toe te voegen aan de Speech SDK, de speech-apparaten SDK of REST Api's. Voeg spraak functionaliteit toe aan een bestaande chat-bot, Converteer tekst naar spraak in een Vertaal toepassing of verstuur grote hoeveel heden Call Center-gegevens.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 0aa4286d8cb630f221613bebd13f7ea722224ac6
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70068850"
---
# <a name="what-are-the-speech-services"></a>Wat zijn de Speech Services?

De spraak Services zijn de combineert van spraak naar tekst, tekst naar spraak en spraak omzetting in één Azure-abonnement. Het is eenvoudig om uw toepassingen, hulpprogram ma's en apparaten met de spraak- [SDK](speech-sdk-reference.md), [Speech-apparaten SDK](https://aka.ms/sdsdk-quickstart)of [rest api's](rest-apis.md)in te scha kelen.

> [!IMPORTANT]
> Spraak Services zijn vervangen Bing Speech-API, Translator Speech en Custom Speech. Raadpleeg de *hand leidingen > migratie* voor migratie-instructies.

Deze functies vormen de spraak services van Azure. Gebruik de koppelingen in deze tabel voor meer informatie over veelvoorkomende use cases voor elke functie of blader door de API-verwijzing.

| Service | Functie | Description | SDK | REST |
|---------|---------|-------------|-----|------|
| [Spraak naar tekst](speech-to-text.md) | Spraak-naar-tekst | Met spraak naar tekst worden audio stromen naar tekst getranscribeerd in realtime die uw toepassingen, hulpprogram ma's of apparaten kunnen gebruiken of weer geven. Gebruik spraak-naar-tekst met [Language Understanding (Luis)](https://docs.microsoft.com/azure/cognitive-services/luis/) om gebruikers intentie af te leiden van transcribed speech en Act on Voice Commands. | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Batch-transcriptie](batch-transcription.md) | Batch transcriptie maakt asynchrone spraak-naar-tekst-transcriptie met grote hoeveel heden gegevens mogelijk. Dit is een REST-gebaseerde service, die hetzelfde eind punt gebruikt als aanpassing en model beheer. | Nee | [Ja](https://westus.cris.ai/swagger/ui/index) |
| | [Gesprek transcriptie](conversation-transcription-service.md) | Hiermee schakelt u realtime spraak herkenning, identificatie van de spreker en diarization in. Het is ideaal voor het overzetten van persoonlijke vergaderingen met de mogelijkheid om de luid sprekers te onderscheiden. | Ja | Nee |
| | [Custom Speech modellen maken](#customize-your-speech-experience) | Als u spraak-naar-tekst gebruikt voor herkenning en transcriptie in een unieke omgeving, kunt u aangepaste akoestische, taal en uitspraak modellen maken en trainen om omgevings lawaai of branchespecifieke woorden lijsten te verhelpen. | Nee | [Ja](https://westus.cris.ai/swagger/ui/index) |
| [Tekst naar spraak](text-to-speech.md) | Tekst naar spraak | Tekst-naar-spraak zet invoer tekst om in Human-achtige, geprogrammeerde spraak met behulp van [SSML (Speech synthese Markup Language)](text-to-speech.md#speech-synthesis-markup-language-ssml). Kies uit standaard stemmen en Neural stemmen (Zie [taal ondersteuning](language-support.md)). | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Aangepaste stemmen maken](#customize-your-speech-experience) | Aangepaste spraak lettertypen maken die uniek zijn voor uw merk of product. | Nee | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Spraakomzetting](speech-translation.md) | Spraakomzetting | Met spraak omzetting kan spraak op meerdere talen worden omgezet in uw toepassingen, hulpprogram ma's en apparaten. Gebruik deze service voor conversie van spraak naar spraak en spraak naar tekst. | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | Nee |
| [Virtuele assistenten voor spraak](voice-first-virtual-assistants.md) | Virtuele assistenten voor spraak | Met aangepaste virtuele assistenten die gebruikmaken van Azure speech Services kunnen ontwikkel aars natuurlijke, menselijke-achtige gespreks interfaces maken voor hun toepassingen en ervaringen. Het directe-lijn spraak kanaal van het bot-Framework verbetert deze mogelijkheden door een gecoördineerd, georganisatied ingangs punt te bieden aan een compatibele bot waarmee gesp roken communicatie mogelijk is met lage latentie en hoge betrouw baarheid. | [Ja](voice-first-virtual-assistants.md) | Nee |

## <a name="news-and-updates"></a>Nieuws en updates

Meer informatie over wat er nieuw is in de Azure speech Services.

* 2019 augustus
  * **Nieuwe zelf studie**: [Spraak inschakelen voor uw bot met de Speech-SDK, C#](tutorial-voice-enable-your-bot-speech-sdk.md)
  * Er is voor de [`chat`](speech-synthesis-markup.md#adjust-speaking-styles) `en-US-JessaNeural` spraak een nieuwe spraak stijl toegevoegd. 
* Juni 2019
  * 1\.6.0 van vrijgegeven spraak-SDK. Zie [release opmerkingen](releasenotes.md)voor een volledige lijst met updates, verbeteringen en bekende problemen.
* Mei 2019-documentatie is nu beschikbaar voor [transcriptie](conversation-transcription-service.md), [Call Center transcriptie](call-center-transcription.md)en [spraak-eerste virtuele assistenten](voice-first-virtual-assistants.md).
* Mei 2019
  * 1\.5.1 van vrijgegeven spraak-SDK. Zie [release opmerkingen](releasenotes.md)voor een volledige lijst met updates, verbeteringen en bekende problemen.
  * 1\.5.0 van vrijgegeven spraak-SDK. Zie [release opmerkingen](releasenotes.md)voor een volledige lijst met updates, verbeteringen en bekende problemen.

## <a name="try-speech-services"></a>Probeer spraak Services

We bieden Quick starts in de populairste programmeer talen, die allemaal ontworpen zijn voor het uitvoeren van code in minder dan 10 minuten. Deze tabel bevat de populairste Quick starts voor elke functie. Gebruik de linkernavigatiebalk om extra talen en platformen te verkennen.

| Spraak naar tekst (SDK) | Tekst-naar-spraak (SDK) | Vertaling (SDK) |
|----------------------|----------------------|-------------------|
| [C#, .NET core (Windows)](quickstart-csharp-dotnet-windows.md) | [C#, .NET Framework (Windows)](quickstart-text-to-speech-dotnet-windows.md) | [Java (Windows, Linux)](quickstart-translate-speech-java-jre.md) |
| [Java script (browser)](quickstart-js-browser.md) | [C++Windows](quickstart-text-to-speech-cpp-windows.md) | [C#, .NET core (Windows)](quickstart-translate-speech-dotnetcore-windows.md) |
| [Python (Windows, Linux, macOS)](quickstart-python.md) | [C++Spreek](quickstart-text-to-speech-cpp-linux.md) | [C#, .NET Framework (Windows)](quickstart-translate-speech-dotnetframework-windows.md) |
| [Java (Windows, Linux)](quickstart-java-jre.md) | | [C++Windows](quickstart-translate-speech-cpp-windows.md) |

> [!NOTE]
> Spraak-naar-tekst en tekst-naar-spraak hebben ook REST-eind punten en bijbehorende Quick starts.

Nadat u de spraak Services hebt gebruikt, kunt u zelf zelf studie proberen om de intenties van spraak te herkennen met behulp van de Speech SDK en LUIS.

* [Zelfstudie: Intenties herkennen vanuit spraak met de Speech SDK en LUIS,C#](how-to-recognize-intents-from-speech-csharp.md)
* [Zelfstudie: Stem uw bot in met de Speech SDK,C#](tutorial-voice-enable-your-bot-speech-sdk.md)
* [Zelfstudie: Bouw een kolf-app voor het vertalen van tekst, analyseer sentiment en vertaalde tekst naar spraak, REST](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>Voorbeeldcode ophalen

De voorbeeld code is beschikbaar op GitHub voor elk van de Azure speech-Services. Deze voor beelden hebben betrekking op veelvoorkomende scenario's, zoals het lezen van audio van een bestand of stream, continue en eenmalige herkenning en het werken met aangepaste modellen. Gebruik deze koppelingen om SDK-en REST-voor beelden weer te geven:

* [Voor beelden van spraak naar tekst, tekst naar spraak en spraak omzetting (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Voor beelden van batch transcriptie (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
* [Voor beelden van tekst naar spraak (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
* [Voor beelden van spraak-eerste virtuele assistent (SDK)](https://aka.ms/csspeech/samples)

## <a name="customize-your-speech-experience"></a>Uw spraak ervaring aanpassen

Azure speech Services werkt goed met ingebouwde modellen, maar u kunt de ervaring voor uw product of omgeving verder aanpassen en afstemmen. Aanpassings opties variëren van het verfijnen van akoestische modellen tot unieke spraak lettertypen voor uw merk. Nadat u een aangepast model hebt gemaakt, kunt u het gebruiken met een van de Azure-spraak Services.

| Speech Service | Platform | Description |
|----------------|-------------|-------------|
| Spraak naar tekst | [Aangepaste spraak](https://aka.ms/customspeech) | Pas de modellen voor spraak herkenning aan uw behoeften en beschik bare gegevens aan. Elimineer hindernissen bij spraakherkenning, zoals spreekstijl, vocabulaire en achtergrondgeluiden. |
| Tekst naar spraak | [Aangepaste spraak](https://aka.ms/customvoice) | Bouw een herken bare, eenmalige stem voor uw tekst-naar-spraak-apps met uw gesp roken gegevens. U kunt de spraak uitvoer verder verfijnen door een set spraak parameters aan te passen. |

## <a name="reference-docs"></a>Referentiedocumenten

* [Speech-SDK](speech-sdk-reference.md)
* [SDK voor spraak apparaten](speech-devices-sdk.md)
* [REST API: Spraak naar tekst](rest-speech-to-text.md)
* [REST API: Tekst-naar-spraak](rest-text-to-speech.md)
* [REST API: Batch-transcriptie en-aanpassing](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Gratis een abonnements sleutel voor spraak Services aanschaffen](get-started.md)
