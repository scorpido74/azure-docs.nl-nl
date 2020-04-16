---
title: Spraak-naar-tekst - Spraakservice
titleSuffix: Azure Cognitive Services
description: De spraak-naar-tekst-functie maakt real-time transcriptie van audiostreams in tekst mogelijk. Uw toepassingen, hulpprogramma's of apparaten kunnen deze tekstinvoer verbruiken, weergeven en actie ondernemen. Deze service werkt naadloos samen met de tekst-naar-spraak (spraaksynthese) en spraakvertaalfuncties.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: trbye
ms.openlocfilehash: e0aea7e55381e9571b156701699f5f45315bb384
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399706"
---
# <a name="what-is-speech-to-text"></a>Wat is spraak-naar-tekst?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Spraak-naar-tekst van de Spraakservice, ook wel spraakherkenning genoemd, maakt real-time transcriptie van audiostreams in tekst mogelijk. Uw toepassingen, hulpprogramma's of apparaten kunnen deze tekst gebruiken, weergeven en actie ondernemen als opdrachtinvoer. Deze service wordt aangedreven door dezelfde herkenningstechnologie die Microsoft gebruikt voor Cortana- en Office-producten. Het werkt naadloos samen met het <a href="./speech-translation.md" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> vertaal-</a> en <a href="./text-to-speech.md" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> tekst-naar-spraak-serviceaanbod.</a> Zie [ondersteunde talen](language-support.md#speech-to-text)voor een volledige lijst met beschikbare spraak-naar-teksttalen.

De spraak-naar-tekstservice wordt standaard gebruikt bij het gebruik van het universele taalmodel. Dit model is getraind met gegevens die eigendom zijn van Microsoft en wordt geïmplementeerd in de cloud. Het is optimaal voor gespreks- en dicteerscenario's. Wanneer u spraak-naar-tekst gebruikt voor herkenning en transcriptie in een unieke omgeving, u aangepaste akoestische, taal- en uitspraakmodellen maken en trainen. Maatwerk is handig voor het aanpakken van omgevingsgeluid of branchespecifieke woordenschat.

> [!NOTE]
> Bing Speech is op 15 oktober 2019 uit de commissie verwijderd. Als uw toepassingen, hulpprogramma's of producten de Bing-spraak-API's gebruiken, hebben we handleidingen gemaakt om u te helpen migreren naar de spraakservice.
> - [Migreren van Bing Speech naar de spraakservice](how-to-migrate-from-bing-speech.md)

## <a name="get-started-with-speech-to-text"></a>Aan de slag met spraak-naar-tekst

De spraak-naar-tekstservice is beschikbaar via de [Speech SDK.](speech-sdk.md) Er zijn verschillende veelvoorkomende scenario's beschikbaar als quickstarts, in verschillende talen en platforms:

 - [Snelstart: spraak herkennen met microfooningang](quickstarts/speech-to-text-from-microphone.md)
 - [Snelstart: spraak uit een bestand herkennen](quickstarts/speech-to-text-from-file.md)
 - [Snelstart: spraak herkennen die is opgeslagen in blobopslag](quickstarts/from-blob.md)

Zie [REST-API's](rest-speech-to-text.md)als u de restservice van spraak naar tekst wilt gebruiken.

## <a name="tutorials-and-sample-code"></a>Zelfstudies en voorbeeldcode

Nadat u de spraakservice hebt kunnen gebruiken, probeert u onze zelfstudie die u leert hoe u intenties van spraak herkennen met behulp van de Speech SDK en LUIS.

- [Zelfstudie: Intents van spraak herkennen met de Speech SDK en LUIS met C #](how-to-recognize-intents-from-speech-csharp.md)

Voorbeeldcode voor de Speech SDK is beschikbaar op GitHub. Deze voorbeelden hebben betrekking op veelvoorkomende scenario's zoals het lezen van audio uit een bestand of stream, continue en single-shot herkenning en het werken met aangepaste modellen.

- [Spraak-naar-tekstvoorbeelden (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Batch transcriptie monsters (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>Aanpassing

Naast het standaard Spraak-servicemodel u aangepaste modellen maken. Maatwerk helpt om spraakherkenningsbarrières zoals spreekstijl, woordenschat en achtergrondgeluid te overwinnen, zie [Aangepaste spraak.](how-to-custom-speech.md) Aanpassingsopties verschillen per taal/land, zie [ondersteunde talen](supported-languages.md) om ondersteuning te verifiëren.

[!INCLUDE [speech-reference-doc-links](includes/speech-reference-doc-links.md)]

## <a name="next-steps"></a>Volgende stappen

- [Ontvang gratis een abonnementssleutel voor spraakservice](get-started.md)
- [Download de Spraak-SDK](speech-sdk.md)
