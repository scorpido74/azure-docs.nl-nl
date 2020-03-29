---
title: Spraakvertaling met spraakservice
titleSuffix: Azure Cognitive Services
description: Met spraakservice u end-to-end, real-time, meertalige vertaling van spraak toevoegen aan uw toepassingen, hulpprogramma's en apparaten. Dezelfde API kan worden gebruikt voor omzetting van zowel spraak-naar-spraak als spraak-naar-tekst.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: erhopf
ms.openlocfilehash: f51288da6af3580ba7592950cde4f17d7adad529
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80052620"
---
# <a name="what-is-speech-translation"></a>Wat is spraakomzetting?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Spraakvertaling van de Spraakservice maakt real-time, meertalige spraak-naar-spraak en spraak-naar-tekstvertaling van audiostreams mogelijk. Met de Speech SDK hebben uw toepassingen, hulpprogramma's en apparaten toegang tot brontranscripties en vertaaluitvoer voor geleverde audio. Tussentijdse transcriptie- en vertaalresultaten worden geretourneerd wanneer spraak wordt gedetecteerd en de resultaten van de finales kunnen worden omgezet in gesynthetiseerde spraak.

De vertaalengine van Microsoft wordt aangedreven door twee verschillende benaderingen: statistische machinetranslation (SMT) en neurale machine translation (NMT). SMT maakt gebruik van geavanceerde statistische analyse om de best mogelijke vertalingen te schatten, gezien de context van een paar woorden. Met NMT worden neurale netwerken gebruikt om nauwkeurigere, natuurlijk klinkende vertalingen te leveren door de volledige context van zinnen te gebruiken om woorden te vertalen.

Tegenwoordig gebruikt Microsoft NMT voor vertaling naar de populairste talen. Alle talen die beschikbaar zijn [voor spraak-naar-spraak vertaling](language-support.md#speech-translation) worden aangedreven door NMT. Spraak-naar-tekst vertaling kan smt of NMT gebruiken, afhankelijk van het taalpaar. Wanneer de doeltaal wordt ondersteund door NMT, wordt de volledige vertaling NMT-aangedreven. Wanneer de doeltaal niet wordt ondersteund door NMT, is de vertaling een hybride van NMT en SMT, met Behulp van Engels als een "spil" tussen de twee talen.

## <a name="core-features"></a>Kernfuncties

Hier zijn de functies die beschikbaar zijn via de Speech SDK en REST API's:

| Gebruiksvoorbeeld | SDK | REST |
|----------|-----|------|
| Spraak-naar-tekst vertaling met herkenningsresultaten. | Ja | Nee |
| Spraak-naar-spraak vertaling. | Ja | Nee |
| Tussentijdse erkenning en vertaalresultaten. | Ja | Nee |

## <a name="get-started-with-speech-translation"></a>Aan de slag met spraakvertaling

We bieden quickstarts die zijn ontworpen om u in minder dan 10 minuten code te laten uitvoeren. Deze tabel bevat een lijst met snel gestarte spraakvertalingen, georganiseerd op taal.

| Snelstartgids | Platform | API-verwijzing |
|------------|----------|---------------|
| [C#, .NET Core](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore) | Windows | [Bladeren](https://aka.ms/csspeech/csharpref) |
| [C#, .NET Framework](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet) | Windows | [Bladeren](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp) | Windows | [Bladeren](https://aka.ms/csspeech/csharpref) |
| [C++](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-cpp&tabs=windows) | Windows | [Bladeren](https://aka.ms/csspeech/cppref)|
| [Java](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-java&tabs=jre) | Windows, Linux, macOS | [Bladeren](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Voorbeeldcode

Voorbeeldcode voor de Speech SDK is beschikbaar op GitHub. Deze voorbeelden hebben betrekking op veelvoorkomende scenario's zoals het lezen van audio uit een bestand of stream, continue en single-shot herkenning / vertaling, en het werken met aangepaste modellen.

* [Spraak-naar-tekst- en vertaalvoorbeelden (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>Migratiehandleidingen

Als uw toepassingen, hulpprogramma's of producten de [Translator Speech API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/overview)gebruiken, hebben we handleidingen gemaakt om u te helpen migreren naar de spraakservice.

* [Migreren van de Api voor vertalersspraak naar de spraakservice](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>Referentiedocumenten

* [Speech-SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [REST-API: spraak-naar-tekst](rest-speech-to-text.md)
* [REST API: Tekst-naar-spraak](rest-text-to-speech.md)
* [REST API: Batch transcriptie en aanpassing](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Volgende stappen

* [Ontvang gratis een abonnementssleutel voor spraakservice](get-started.md)
* [Download de Spraak-SDK](speech-sdk.md)
