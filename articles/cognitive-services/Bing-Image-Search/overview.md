---
title: Wat is de Bing Afbeeldingen zoeken-API?
titleSuffix: Azure Cognitive Services
description: "Met de Bing Afbeeldingen zoeken-API kunt u de cognitieve zoekfunctionaliteit voor afbeeldingen van Bing in uw toepassing gebruiken. Door gebruikerszoekquery's te verzenden met de API kunt u relevante en hoogwaardige afbeeldingen ophalen en weergeven die vergelijkbaar zijn met Bing: afbeeldingen."
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 1446AD8B-A685-4F5F-B4AA-74C8E9A40BE9
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: eb657c16f6f3ff67f4379134f3aa478f10d8ef94
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "85603533"
---
# <a name="what-is-the-bing-image-search-api"></a>Wat is de Bing Afbeeldingen zoeken-API?

Met de Bing Afbeeldingen zoeken-API kunt u de zoekfunctionaliteit voor afbeeldingen van Bing in uw toepassing gebruiken. Door zoekquery's te verzenden naar de API kunt u hoogwaardige afbeeldingen ophalen die vergelijkbaar zijn met [bing.com/images](https://www.bing.com/images).

Hoewel de Bing afbeeldingen zoeken-API alleen zoekresultaten met afbeeldingen genereert, kunt u de andere beschikbare [Bing zoeken-API's](../bing-web-search/bing-api-comparison.md) combineren of gebruiken om veel soorten inhoud te vinden op het web.

## <a name="bing-image-search-features"></a>Functies van Bing Afbeeldingen zoeken

| Functie                                                                                                                                                                                 | Beschrijving                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Zoektermen in realtime voorstellen](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries) | Verbeter uw app-ervaring met de [Bing Automatische suggestie-API](../bing-autosuggest/get-suggested-search-terms.md) om voorgestelde zoektermen weer te geven wanneer deze worden getypt. |
| [Afbeeldingsresultaten filteren en beperken](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images)                       | Filter de afbeeldingen die via Bing worden geretourneerd door queryparameters te bewerken.                                                                                                       |
| [Miniaturen bijsnijden en weergeven en het formaat hiervan wijzigen](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/resize-and-crop-thumbnails)                                                | Bewerk miniatuurweergaven voor de afbeeldingen die zijn geretourneerd door Bing Afbeeldingen zoeken en geef deze weer.                                                                                      |
| [Gebruikerszoekquery's draaien en uitvouwen](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries)               | Breid uw zoekmogelijkheden uit door voorgestelde zoektermen door Bing op te nemen en weer te geven in query's.                                                                    |
| [Trending afbeeldingen ophalen](trending-images.md)                                                                     | Pas een zoekopdracht voor trending afbeeldingen van over de hele wereld aan.                                                                                                          |

## <a name="workflow"></a>Werkstroom

De Bing Afbeeldingen zoeken-API is een RESTful-webservice die eenvoudig kan worden aangeroepen vanuit elke programmeertaal waarmee HTTP-aanvragen kunnen worden gedaan en JSON kan worden geparseerd. U kunt de service gebruiken met de [REST API](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/quickstarts/csharp?) of de [SDK](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart).

1. Maak een [Account voor Cognitive Services-API](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) met toegang tot de Bing Zoeken-API's. Als u geen Azure-abonnement hebt, kunt u gratis [een account maken](https://azure.microsoft.com/free/cognitive-services/).
2. Verzend een aanvraag naar de API met een geldige [zoekquery](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries).
3. Verwerk de API-reactie door het geretourneerde JSON-bericht te parseren.

## <a name="next-steps"></a>Volgende stappen

Probeer eerst de [interactieve demo](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/) van de Bing Afbeeldingen zoeken-API uit.
In deze demo ziet u hoe u snel een zoekquery kunt aanpassen en internet kunt doorzoeken op afbeeldingen.

Als u snel aan de slag wilt met uw eerste API-aanvraag, kunt u het volgende leren:

* [Zoekquery's naar Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/quickstarts/csharp) verzenden met de REST API of
* De afbeeldingen [aanvragen en filteren](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart) die door Bing worden geretourneerd met behulp van de SDK.

## <a name="see-also"></a>Zie tevens

* [Prijsinformatie](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) voor Bing Zoek-API's. 

* De referentiesectie [Bing Afbeeldingen zoeken-API versie 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) bevat informatie over de API-eindpunten, headers, API-reacties en queryparameters.

* In de [Bing-vereisten voor gebruik en weergave](./useanddisplayrequirements.md) staan het acceptabele gebruik van de inhoud en informatie die is verkregen via de Bing Zoeken-API's.

* In het artikel [Afbeeldingen ophalen van internet met de Bing Afbeeldingen zoeken-API](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images) wordt beschreven hoe u afbeeldingen kunt zoeken en ophalen van internet.

* In het artikel [Zoekquery's verzenden en ermee werken](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries) wordt beschreven hoe u zoekquery's kunt maken, aanpassen en draaien.

* Bezoek de [hubpagina voor de Bing Search-API](../bing-web-search/search-the-web.md) om de andere beschikbare API's te verkennen.
