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
ms.date: 08/27/2020
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: cef6ff109607455881970aeb95eaf0859f6f4f05
ms.sourcegitcommit: 420c30c760caf5742ba2e71f18cfd7649d1ead8a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2020
ms.locfileid: "89055529"
---
# <a name="what-is-speech-translation"></a>Wat is spraakomzetting?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Spraak omzetting van de spraak service biedt de mogelijkheid om audio gegevensstromen in realtime, meertalige spraak naar spraak en spraak naar tekst te converteren. Met de spraak-SDK hebben uw toepassingen, hulpprogram ma's en apparaten toegang tot de bron-transcripties en de Vertaal uitvoer voor de audio. Tijdelijke transcriptie en vertaal resultaten worden geretourneerd als spraak wordt gedetecteerd en de eind resultaten kunnen worden omgezet in gesynthesizerde spraak.

De Vertaal engine van micro soft wordt aangedreven door twee verschillende benaderingen: statistische machine vertaling (SMT) en Neural machine translation (NMT). SMT maakt gebruik van geavanceerde statistische analyses om de best mogelijke vertalingen te ramen, op basis van de context van enkele woorden. Met NMT worden Neural-netwerken gebruikt om nauw keurige, natuurlijke geluids omzetting te bieden met behulp van de volledige context van zinnen voor het vertalen van woorden.

Momenteel gebruikt micro soft NMT voor de vertaling van de populairste talen. Alle [talen die beschikbaar zijn voor conversie van spraak naar spraak](language-support.md#speech-translation) , worden aangedreven door NMT. Conversie van spraak naar tekst kan gebruikmaken van SMT of NMT, afhankelijk van het taal paar. Wanneer de doel taal wordt ondersteund door NMT, is de volledige vertaling NMTd. Wanneer de doel taal niet wordt ondersteund door NMT, is de vertaling een hybride van NMT en SMT, met behulp van Engels als ' draai tabel ' tussen de twee talen.

## <a name="core-features"></a>Kern functies

* Conversie van spraak naar tekst met herkennings resultaten.
* Conversie van spraak naar spraak.
* Ondersteuning voor vertaling naar meerdere doel talen.
* Tussentijdse herkenning en omzettings resultaten.

## <a name="get-started"></a>Aan de slag 

Raadpleeg de [Snelstartgids](get-started-speech-translation.md) om aan de slag te gaan met spraak omzetting. De service voor spraak omzetting is beschikbaar via de [Speech SDK](speech-sdk.md) en de [Speech cli](spx-overview.md).

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

* De [Snelstartgids](get-started-speech-translation.md) voor spraak omzetting volt ooien
* [Gratis een abonnements sleutel voor een spraak service ophalen](get-started.md)
* [De Speech SDK ophalen](speech-sdk.md)
