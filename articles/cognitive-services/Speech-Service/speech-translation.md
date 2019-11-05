---
title: Spraak omzetting met spraak service
titleSuffix: Azure Cognitive Services
description: Met de spraak service kunt u een end-to-end, realtime vertaling van spraak naar uw toepassingen, hulpprogram ma's en apparaten toevoegen. Dezelfde API kan worden gebruikt voor conversie van spraak naar spraak en spraak naar tekst.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 17ad32cba18915bf7f83163cd876686d42323750
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468756"
---
# <a name="what-is-speech-translation"></a>Wat is spraak omzetting?

Spraak omzetting van Azure speech Services, biedt de mogelijkheid om audio gegevensstromen in realtime, meertalige spraak naar spraak en spraak naar tekst te converteren. Met de spraak-SDK hebben uw toepassingen, hulpprogram ma's en apparaten toegang tot de bron-transcripties en de Vertaal uitvoer voor de audio. Tijdelijke transcriptie en vertaal resultaten worden geretourneerd als spraak wordt gedetecteerd en de eind resultaten kunnen worden omgezet in gesynthesizerde spraak.

De Vertaal engine van micro soft wordt aangedreven door twee verschillende benaderingen: statistische machine vertaling (SMT) en Neural machine translation (NMT). SMT maakt gebruik van geavanceerde statistische analyses om de best mogelijke vertalingen te ramen, op basis van de context van enkele woorden. Met NMT worden Neural-netwerken gebruikt om nauw keurige, natuurlijke geluids omzetting te bieden met behulp van de volledige context van zinnen voor het vertalen van woorden.

Momenteel gebruikt micro soft NMT voor de vertaling van de populairste talen. Alle [talen die beschikbaar zijn voor conversie van spraak naar spraak](language-support.md#speech-translation) , worden aangedreven door NMT. Conversie van spraak naar tekst kan gebruikmaken van SMT of NMT, afhankelijk van het taal paar. Wanneer de doel taal wordt ondersteund door NMT, is de volledige vertaling NMTd. Wanneer de doel taal niet wordt ondersteund door NMT, is de vertaling een hybride van NMT en SMT, met behulp van Engels als ' draai tabel ' tussen de twee talen.

## <a name="core-features"></a>Kern functies

Hier volgen de functies die beschikbaar zijn via de Speech SDK en REST Api's:

| Toepassing | SDK | REST |
|----------|-----|------|
| Conversie van spraak naar tekst met herkennings resultaten. | Ja | Nee |
| Conversie van spraak naar spraak. | Ja | Nee |
| Tussentijdse herkenning en omzettings resultaten. | Ja | Nee |

## <a name="get-started-with-speech-translation"></a>Aan de slag met spraak omzetting

We bieden Quick starts die zijn ontworpen om code in minder dan 10 minuten uit te voeren. Deze tabel bevat een lijst met Quick starts voor spraak vertalingen, geordend op taal.

| Snelstart | Platform | API-verwijzing |
|------------|----------|---------------|
| [C#, .NET core](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore) | Windows | [Door](https://aka.ms/csspeech/csharpref) |
| [C#.NET Framework](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet) | Windows | [Door](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp) | Windows | [Door](https://aka.ms/csspeech/csharpref) |
| [C++](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-cpp&tabs=windows) | Windows | [Door](https://aka.ms/csspeech/cppref)|
| [Java](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-java&tabs=jre) | Windows, Linux, macOS | [Door](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Voorbeeldcode

Voorbeeld code voor de Speech SDK is beschikbaar op GitHub. Deze voor beelden hebben betrekking op veelvoorkomende scenario's, zoals het lezen van audio van een bestand of stream, continue en eenmalige herkenning/omzetting en werken met aangepaste modellen.

* [Voor beelden van spraak naar tekst en vertaling (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>Migratiehandleidingen

Als uw toepassingen, hulpprogram ma's of producten de [Translator Speech-API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/overview)gebruiken, hebben we gidsen gemaakt om u te helpen migreren naar de spraak Services.

* [Migreren van de Translator Speech-API naar de spraak Services](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>Referentiedocumenten

* [Speech-SDK](speech-sdk-reference.md)
* [SDK voor spraak apparaten](speech-devices-sdk.md)
* [REST API: spraak naar tekst](rest-speech-to-text.md)
* [REST API: tekst-naar-spraak](rest-text-to-speech.md)
* [REST API: batch transcriptie en-aanpassing](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Volgende stappen

* [Gratis een abonnements sleutel voor spraak Services aanschaffen](get-started.md)
* [De Speech SDK ophalen](speech-sdk.md)
