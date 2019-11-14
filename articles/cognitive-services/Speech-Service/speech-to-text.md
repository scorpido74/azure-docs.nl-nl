---
title: Spraak-naar-tekst-spraak-service
titleSuffix: Azure Cognitive Services
description: Met de functie voor spraak naar tekst kunt u transcriptie in realtime maken in tekst die uw toepassingen, hulpprogram ma's of apparaten kunnen gebruiken, weer geven en actie ondernemen als opdracht invoer. Deze service werkt probleemloos met de functies voor tekst naar spraak (spraak synthese) en spraak omzetting.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 49bfa4a0dbf0adc498d545a2908c20f0ffa35b4b
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075723"
---
# <a name="what-is-speech-to-text"></a>Wat is spraak-naar-tekst?

Spraak-naar-tekst van Azure speech Services, ook wel spraak naar tekst genoemd, biedt realtime transcriptie van audio-streams in tekst die uw toepassingen, hulpprogram ma's of apparaten kunnen gebruiken, weer geven en actie ondernemen als opdracht invoer. Deze service wordt aangestuurd door dezelfde herkennings technologie die door micro soft wordt gebruikt voor Cortana en Office-producten en werkt naadloos samen met de vertaling en tekst-naar-spraak. Zie [ondersteunde talen](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#speech-to-text)voor een volledige lijst met beschik bare spraak-naar-tekst talen.

De spraak-naar-tekst-service maakt standaard gebruik van het universele-taal model. Dit model is getraind met gegevens van micro soft en wordt geïmplementeerd in de Cloud. Het is optimaal voor gespreks-en dicteer scenario's. Als u spraak-naar-tekst gebruikt voor herkenning en transcriptie in een unieke omgeving, kunt u aangepaste akoestische, taal en uitspraak modellen maken en trainen om omgevings lawaai of branchespecifieke woorden lijsten te verhelpen.

U kunt eenvoudig audio van een microfoon vastleggen, vanuit een stroom lezen of audio bestanden openen vanuit Storage met de spraak-SDK en REST Api's. De Spraak-SDK ondersteunt WAV/PCM 16-bit, 16 kHz/8 kHz geluid met één kanaal voor spraakherkenning. Extra geluidsindelingen worden ondersteund als u de [Spraak-naar-tekst REST-eindpunt](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) of de [batchtranscriptieservice](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats) gebruikt.

## <a name="core-features"></a>Kern functies

Hier volgen de functies die beschikbaar zijn via de Speech SDK en REST Api's:

| Toepassing | SDK | REST |
|--------- | --- | ---- |
| Vertranscribeer korte uitingen (< 15 seconden). Biedt alleen ondersteuning voor het uiteindelijke transcriptie resultaat. | Ja | Ja |
| Continue transcriptie van lange uitingen en streaming audio (> 15 seconden). Ondersteunt tussentijdse en definitieve transcriptie-resultaten. | Ja | Nee |
| Intenties afleiden van herkennings resultaten met [Luis](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis). | Ja | Nee\* |
| Batch-transcriptie van audio bestanden asynchroon. | Nee  | Ja\*\* |
| Spraak modellen maken en beheren. | Nee | Ja\*\* |
| Aangepaste model implementaties maken en beheren. | Nee  | Ja\*\* |
| Maak nauwkeurigheids tests om de nauw keurigheid van het basis model versus aangepaste modellen te meten. | Nee  | Ja\*\* |
| Abonnementen beheren. | Nee  | Ja\*\* |

\*_Luis intenties en entiteiten kunnen worden afgeleid met behulp van een afzonderlijke Luis-abonnement. Met dit abonnement kan de SDK LUIS voor u aanroepen en de resultaten van de entiteit en het doel opgeven. Met de REST API kunt u LUIS zelf aanroepen om intenties en entiteiten af te leiden met uw LUIS-abonnement._

\*\*_deze services zijn beschikbaar via het CRIS.ai-eind punt. Zie [Swagger-verwijzing](https://westus.cris.ai/swagger/ui/index)._

## <a name="get-started-with-speech-to-text"></a>Aan de slag met spraak naar tekst

We bieden Quick starts in de populairste programmeer talen, die allemaal ontworpen zijn voor het uitvoeren van code in minder dan 10 minuten. [Deze tabel](https://aka.ms/csspeech#5-minute-quickstarts) bevat een volledige lijst met Quick starts voor spraak-SDK, geordend op basis van platform en taal. API-verwijzing kan [hier](https://aka.ms/csspeech#reference)ook worden gevonden.

Zie [rest api's](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)als u de spraak-naar-tekst rest-service wilt gebruiken.

## <a name="tutorials-and-sample-code"></a>Zelf studies en voorbeeld code

Nadat u de spraak Services hebt gebruikt, kunt u zelf zelf studie proberen om de intenties van spraak te herkennen met behulp van de Speech SDK en LUIS.

- [Zelf studie: intenties herkennen vanuit spraak met de Speech SDK en LUIS,C#](how-to-recognize-intents-from-speech-csharp.md)

Voorbeeld code voor de Speech SDK is beschikbaar op GitHub. Deze voor beelden hebben betrekking op veelvoorkomende scenario's, zoals het lezen van audio van een bestand of stream, continue en eenmalige herkenning en het werken met aangepaste modellen.

- [Voor beelden van spraak naar tekst (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Voor beelden van batch transcriptie (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>Aanpassing

Naast het standaard basislijn model dat wordt gebruikt door de spraak Services, kunt u modellen aanpassen aan uw behoeften met beschik bare gegevens, om de obstakels voor spraak herkenning te overwinnen, zoals spreek stijl, vocabulaire en achtergrond ruis, Zie [Custom speech](how-to-custom-speech.md)

> [!NOTE]
> Aanpassings opties variëren per taal/land instelling (Zie [ondersteunde talen](supported-languages.md)).

## <a name="migration-guides"></a>Migratiehandleidingen

> [!WARNING]
> Bing Speech is uit bedrijf genomen op 15 oktober 2019.

Als uw toepassingen, hulpprogram ma's of producten gebruikmaken van de Bing Speech Api's of Custom Speech, hebben we gidsen gemaakt om u te helpen migreren naar spraak Services.

- [Migreren van Bing Speech naar de spraak Services](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-bing-speech)
- [Migreren van Custom Speech naar de spraak Services](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-custom-speech-service)

## <a name="reference-docs"></a>Referentiedocumenten

- [Speech-SDK](https://aka.ms/csspeech)
- [SDK voor spraak apparaten](speech-devices-sdk.md)
- [REST API: spraak naar tekst](rest-speech-to-text.md)
- [REST API: tekst-naar-spraak](rest-text-to-speech.md)
- [REST API: batch transcriptie en-aanpassing](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Volgende stappen

- [Gratis een abonnements sleutel voor spraak Services aanschaffen](get-started.md)
- [De Speech SDK ophalen](speech-sdk.md)
