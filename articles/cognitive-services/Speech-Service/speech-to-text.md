---
title: Spraak-naar-tekst-spraak-service
titleSuffix: Azure Cognitive Services
description: Met de functie voor spraak naar tekst kunt u transcriptie in realtime omzetten in tekst. Uw toepassingen, hulpprogram ma's of apparaten kunnen deze tekst invoer gebruiken, weer geven en actie ondernemen. Deze service werkt probleemloos met de functies voor tekst naar spraak (spraak synthese) en spraak omzetting.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: erhopf
ms.openlocfilehash: 8518f92a4f4df1686d4b338783a93d969e04d219
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79219542"
---
# <a name="what-is-speech-to-text"></a>Wat is spraak-naar-tekst?

Spraak-naar-tekst van de speech-service, ook wel spraak herkenning genoemd, biedt realtime transcriptie van audio-streams in tekst. Uw toepassingen, hulpprogram ma's of apparaten kunnen deze tekst gebruiken, weer geven en actie ondernemen als invoer van de opdracht. Deze service wordt aangestuurd door dezelfde herkennings technologie die door micro soft wordt gebruikt voor Cortana en Office-producten. Het werkt probleemloos met de <a href="./speech-translation.md" target="_blank">Vertaal <span class="docon docon-navigate-external x-hidden-focus"></span> </a> -en <a href="./text-to-speech.md" target="_blank">tekst-naar- <span class="docon docon-navigate-external x-hidden-focus"></span> spraak-</a> service aanbiedingen. Zie [ondersteunde talen](language-support.md#speech-to-text)voor een volledige lijst met beschik bare spraak-naar-tekst talen.

De spraak-naar-tekst-service is standaard ingesteld op het gebruik van het universele-taal model. Dit model is getraind met gegevens van micro soft en wordt geïmplementeerd in de Cloud. Het is optimaal voor gespreks-en dicteer scenario's. Wanneer u spraak naar tekst gebruikt voor herkenning en transcriptie in een unieke omgeving, kunt u aangepaste akoestische, taal-en uitspraak modellen maken en trainen. Aanpassing is handig voor het adresseren van omgevings lawaai of branchespecifieke woorden lijsten.

> [!NOTE]
> Bing Speech is uit bedrijf genomen op 15 oktober 2019. Als uw toepassingen, hulpprogram ma's of producten gebruikmaken van de Bing Speech Api's of Custom Speech, hebben we gidsen gemaakt om u te helpen migreren naar de speech-service.
> - [Migreren van Bing Speech naar de speech-service](how-to-migrate-from-bing-speech.md)
> - [Migreren van Custom Speech naar de speech-service](how-to-migrate-from-custom-speech-service.md)

## <a name="get-started-with-speech-to-text"></a>Aan de slag met spraak naar tekst

De spraak-naar-tekst-service is beschikbaar via de [Speech-SDK](speech-sdk.md). Er zijn verschillende algemene scenario's beschikbaar als Quick starts in verschillende talen en platformen:

 - [Snelstartgids: spraak herkennen met microfoon invoer](quickstarts/speech-to-text-from-microphone.md)
 - [Quick Start: spraak herkennen vanuit een bestand](quickstarts/speech-to-text-from-file.md)
 - [Quick Start: spraak herkennen die zijn opgeslagen in Blob Storage](quickstarts/from-blob.md)

Zie [rest api's](rest-speech-to-text.md)als u de spraak-naar-tekst rest-service wilt gebruiken.

## <a name="tutorials-and-sample-code"></a>Zelf studies en voorbeeld code

Nadat u de spraak service hebt bekeken, kunt u de zelf studie uitproberen die u leert hoe u de intenties kunt herkennen vanuit spraak met behulp van de Speech SDK en LUIS.

- [Zelf studie: intenties herkennen vanuit spraak met de Speech SDK en LUIS, met behulp vanC#](how-to-recognize-intents-from-speech-csharp.md)

Voorbeeld code voor de Speech SDK is beschikbaar op GitHub. Deze voor beelden hebben betrekking op veelvoorkomende scenario's, zoals het lezen van audio van een bestand of stream, continue en eenmalige herkenning en het werken met aangepaste modellen.

- [Voor beelden van spraak naar tekst (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Voor beelden van batch transcriptie (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>Aanpassing

Naast het standaard spraak service model kunt u aangepaste modellen maken. Aanpassing helpt de obstakels voor spraak herkenning, zoals spreek stijl, vocabulaire en achtergrond ruis, te overwinnen [Custom speech](how-to-custom-speech.md). Aanpassings opties variëren per taal/land instelling. Zie [ondersteunde talen](supported-languages.md) voor het controleren van de ondersteuning.

[!INCLUDE [speech-reference-doc-links](includes/speech-reference-doc-links.md)]

## <a name="next-steps"></a>Volgende stappen

- [Gratis een abonnements sleutel voor een spraak service ophalen](get-started.md)
- [De Speech SDK ophalen](speech-sdk.md)
