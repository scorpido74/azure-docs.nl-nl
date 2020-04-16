---
title: Tekst-naar-spraak - Spraakservice
titleSuffix: Azure Cognitive Services
description: De tekst-naar-spraak-functie in de Spraakservice stelt uw toepassingen, hulpprogramma's of apparaten in staat om tekst om te zetten in natuurlijke menselijke gesynthetiseerde spraak. Kies vooraf ingestelde stemmen of maak uw eigen aangepaste stem.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: trbye
ms.openlocfilehash: 1a8b458eb004b44d0045f36b18d88e11e019c4d2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399506"
---
# <a name="what-is-text-to-speech"></a>Wat is tekst-naar-spraak?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Tekst-naar-spraak van de Spraakservice stelt uw toepassingen, hulpprogramma's of apparaten in staat om tekst om te zetten in menselijke gesynthetiseerde spraak. Kies uit standaard- en neurale stemmen of creëer een aangepaste stem die uniek is voor uw product of merk. 75+ standaardstemmen zijn beschikbaar in meer dan 45 talen en land, en 5 neurale stemmen zijn beschikbaar in een select aantal talen en land. Zie [ondersteunde talen](language-support.md#text-to-speech)voor een volledige lijst met ondersteunde stemmen, talen en landplaatsen.

> [!NOTE]
> Bing Speech is op 15 oktober 2019 uit de commissie verwijderd. Als uw toepassingen, hulpprogramma's of producten de Bing Speech API's of Aangepaste spraak gebruiken, hebben we handleidingen gemaakt om u te helpen migreren naar de spraakservice.
> - [Migreren van Bing Speech naar de spraakservice](how-to-migrate-from-bing-speech.md)

## <a name="core-features"></a>Kernfuncties

* Spraaksynthese - Gebruik de [Speech SDK](quickstarts/text-to-speech-audio-file.md) of [REST API](rest-text-to-speech.md) om tekst-naar-spraak om te zetten met behulp van standaard,neurale of aangepaste stemmen.

* Asynchrone synthese van lange audio - Gebruik de [Lange Audio API](long-audio-api.md) om tekst-naar-spraakbestanden asynchroon te synthetiseren langer dan 10 minuten (bijvoorbeeld audioboeken of lezingen). In tegenstelling tot synthese uitgevoerd met behulp van de Speech SDK of speech-to-text REST API, worden reacties niet in realtime geretourneerd. De verwachting is dat aanvragen asynchroon worden verzonden, antwoorden worden gepeild voor, en dat de gesynthetiseerde audio wordt gedownload wanneer beschikbaar gesteld van de dienst. Alleen aangepaste neurale stemmen worden ondersteund.

* Standaardstemmen - Gemaakt met behulp van statistische parametrische synthese en/of concatenatiesynthesetechnieken. Deze stemmen zijn zeer verstaanbaar en klinken natuurlijk. U uw toepassingen eenvoudig laten spreken in meer dan 45 talen, met een breed scala aan spraakopties. Deze stemmen bieden een hoge uitspraak nauwkeurigheid, met inbegrip van ondersteuning voor afkortingen, acroniem uitbreidingen, datum / tijd interpretaties, polyfoons, en nog veel meer. Zie ondersteunde talen voor een volledige lijst met [standaardstemmen.](language-support.md#text-to-speech)

* Neurale stemmen - Diepe neurale netwerken worden gebruikt om de grenzen van de traditionele spraaksynthese te overwinnen met betrekking tot stress en intonatie in gesproken taal. Prosodie voorspelling en stemsynthese worden gelijktijdig uitgevoerd, wat resulteert in meer vloeiende en natuurlijk klinkende uitgangen. Neurale stemmen kunnen worden gebruikt om interacties met chatbots en spraakassistenten natuurlijker en boeiender te maken, digitale teksten zoals e-books om te zetten in audioboeken en navigatiesystemen in de auto te verbeteren. Met de mensachtige natuurlijke prosodie en duidelijke articulatie van woorden verminderen neurale stemmen de luistervermoeidheid aanzienlijk wanneer u met AI-systemen communiceert. Voor een volledige lijst van neurale stemmen, zie [ondersteunde talen](language-support.md#text-to-speech).

* Spraaksyntheseopmaaktaal (SSML) - een op XML gebaseerde opmaaktaal die wordt gebruikt om spraak-naar-tekst-uitvoer aan te passen. Met SSML u de toonhoogte aanpassen, pauzes toevoegen, de uitspraak verbeteren, de spreeksnelheid versnellen of vertragen, het volume verhogen of verlagen en meerdere stemmen aan één document toewijzen. Zie [SSML](speech-synthesis-markup.md).

## <a name="get-started"></a>Aan de slag

De tekst-naar-spraakservice is beschikbaar via de [Speech SDK.](speech-sdk.md) Er zijn verschillende veelvoorkomende scenario's beschikbaar als quickstarts, in verschillende talen en platforms:

* [Spraaksynthese in een audiobestand](quickstarts/text-to-speech-audio-file.md)
* [Spraaksynthese naar een luidspreker](quickstarts/text-to-speech.md)
* [Asynchroon synthetiseren lange-vorm audio](quickstarts/text-to-speech/async-synthesis-long-form-audio.md)

Als u dat liever hebt, is de tekst-naar-spraak-service toegankelijk via [REST.](rest-text-to-speech.md)

## <a name="sample-code"></a>Voorbeeldcode

Voorbeeldcode voor tekst-naar-spraak is beschikbaar op GitHub. Deze voorbeelden hebben betrekking op tekst-naar-spraak conversie in de meest populaire programmeertalen.

- [Tekst-naar-spraakvoorbeelden (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Tekst-naar-spraakvoorbeelden (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="customization"></a>Aanpassing

Naast standaard- en neurale stemmen u aangepaste stemmen maken en verfijnen die uniek zijn voor uw product of merk. Alles wat nodig is om te beginnen zijn een handvol audiobestanden en de bijbehorende transcripties. Zie [Aan de slag met Aangepaste stem](how-to-custom-voice.md) voor meer informatie

## <a name="pricing-note"></a>Prijsnota

Wanneer u de tekst-naar-spraakservice gebruikt, wordt u gefactureerd voor elk teken dat wordt omgezet in spraak, inclusief interpunctie. Hoewel het SSML-document zelf niet factureerbaar is, worden optionele elementen die worden gebruikt om aan te passen hoe de tekst wordt omgezet in spraak, zoals fonemen en toonhoogte, geteld als factureerbare tekens. Hier is een lijst van wat factureerbaar is:

- Tekst doorgegeven aan de tekst-naar-spraakdienst in de SSML-instantie van het verzoek
- Alle markeringen in het tekstveld van de aanvraaginstantie in `<speak>` de `<voice>` SSML-indeling, met uitzondering van en tags
- Letters, interpunctie, spaties, tabbladen, markeringen en alle witruimtetekens
- Elk codepunt gedefinieerd in Unicode

Zie [Prijzen voor](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)gedetailleerde informatie.

> [!IMPORTANT]
> Elk Chinees, Japans en Koreaans karakter wordt geteld als twee tekens voor facturering.

## <a name="reference-docs"></a>Referentiedocumenten

- [Speech-SDK](speech-sdk.md)
- [REST API: Tekst-naar-spraak](rest-text-to-speech.md)

## <a name="next-steps"></a>Volgende stappen

- [Ontvang een gratis Speech-serviceabonnement](get-started.md)
- [Download de Spraak-SDK](speech-sdk.md)
