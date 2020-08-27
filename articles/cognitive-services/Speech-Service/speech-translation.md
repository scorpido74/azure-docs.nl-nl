---
title: Spraak omzetting met spraak service
titleSuffix: Azure Cognitive Services
description: Met de spraak service kunt u een end-to-end, realtime vertaling van spraak naar uw toepassingen, hulpprogram ma's en apparaten toevoegen. Dezelfde API kan worden gebruikt voor omzetting van zowel spraak-naar-spraak als spraak-naar-tekst.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: 670d7f160285283bd44371b893c63904b2685926
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934494"
---
# <a name="what-is-speech-translation"></a>Wat is spraakomzetting?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Spraak omzetting van de spraak service biedt de mogelijkheid om audio gegevensstromen in realtime, meertalige spraak naar spraak en spraak naar tekst te converteren. Met de spraak-SDK hebben uw toepassingen, hulpprogram ma's en apparaten toegang tot de bron-transcripties en de Vertaal uitvoer voor de audio. Tijdelijke transcriptie en vertaal resultaten worden geretourneerd als spraak wordt gedetecteerd en de eind resultaten kunnen worden omgezet in gesynthesizerde spraak.

De Vertaal engine van micro soft wordt aangedreven door twee verschillende benaderingen: statistische machine vertaling (SMT) en Neural machine translation (NMT). SMT maakt gebruik van geavanceerde statistische analyses om de best mogelijke vertalingen te ramen, op basis van de context van enkele woorden. Met NMT worden Neural-netwerken gebruikt om nauw keurige, natuurlijke geluids omzetting te bieden met behulp van de volledige context van zinnen voor het vertalen van woorden.

Momenteel gebruikt micro soft NMT voor de vertaling van de populairste talen. Alle [talen die beschikbaar zijn voor conversie van spraak naar spraak](language-support.md#speech-translation) , worden aangedreven door NMT. Conversie van spraak naar tekst kan gebruikmaken van SMT of NMT, afhankelijk van het taal paar. Wanneer de doel taal wordt ondersteund door NMT, is de volledige vertaling NMTd. Wanneer de doel taal niet wordt ondersteund door NMT, is de vertaling een hybride van NMT en SMT, met behulp van Engels als ' draai tabel ' tussen de twee talen.

## <a name="core-features"></a>Kern functies

Hier volgen de functies die beschikbaar zijn via de Speech SDK en REST Api's:

| Gebruiksvoorbeeld | SDK | REST |
|----------|-----|------|
| Conversie van spraak naar tekst met herkennings resultaten. | Ja | Nee |
| Conversie van spraak naar spraak. | Ja | Nee |
| Tussentijdse herkenning en omzettings resultaten. | Ja | Nee |

## <a name="get-started-with-speech-translation"></a>Aan de slag met spraak omzetting

We bieden Quick starts die zijn ontworpen om code in minder dan 10 minuten uit te voeren. Deze tabel bevat een lijst met Quick starts voor spraak vertalingen, geordend op taal.

| Snelstart | Platform | API-verwijzing |
|------------|----------|---------------|
| [C#, .NET core](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore) | Windows | [Bladeren](https://aka.ms/csspeech/csharpref) |
| [C#, .NET Framework](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet) | Windows | [Bladeren](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp) | Windows | [Bladeren](https://aka.ms/csspeech/csharpref) |
| [C++](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-cpp&tabs=windows) | Windows | [Bladeren](https://aka.ms/csspeech/cppref)|
| [Java](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-java&tabs=jre) | Windows, Linux, macOS | [Bladeren](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Voorbeeldcode

Voorbeeld code voor de Speech SDK is beschikbaar op GitHub. Deze voor beelden hebben betrekking op veelvoorkomende scenario's, zoals het lezen van audio van een bestand of stream, continue en eenmalige herkenning/omzetting en werken met aangepaste modellen.

* [Voor beelden van spraak naar tekst en vertaling (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>Migratiehandleidingen

Als uw toepassingen, hulpprogram ma's of producten de [Translator Speech-API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/overview)gebruiken, hebben we gidsen gemaakt om u te helpen migreren naar de spraak service.

* [Migreren van de Translator Speech-API naar de speech-service](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>Referentie documenten

* [Speech-SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [REST API: spraak naar tekst](rest-speech-to-text.md)
* [REST API: tekst-naar-spraak](rest-text-to-speech.md)
* [REST API: batch transcriptie en-aanpassing](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Volgende stappen

* [Gratis een abonnements sleutel voor een spraak service ophalen](get-started.md)
* [De Speech SDK ophalen](speech-sdk.md)
