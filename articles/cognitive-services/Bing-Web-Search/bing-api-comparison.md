---
title: Wat zijn de Bing Search API's?
titleSuffix: Azure Cognitive Services
description: Gebruik dit artikel voor meer informatie over de Bing Search API's en hoe u cognitieve internetzoekopdrachten in uw apps en services inschakelen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: aahi
ms.openlocfilehash: 82b1f13562a49284059c25bcbd39a33daf949dcc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74775537"
---
# <a name="what-are-the-bing-search-apis"></a>Wat zijn de Bing Search API's?

Met de Bing Search-API's kunt u web-apps en -services maken die webpagina's, afbeeldingen, nieuws, locaties en nog veel meer vinden zonder advertenties. Door zoekverzoeken te verzenden met behulp van de Bing Search REST API's of SDKs, u relevante informatie en inhoud krijgen voor zoekopdrachten op het web. Gebruik dit artikel om meer te weten te komen over de verschillende Bing-zoek-API's en hoe u cognitieve zoekopdrachten integreren in uw toepassingen en services. Prijzen en tarieflimieten kunnen variëren tussen API's.

## <a name="the-bing-web-search-api"></a>De Bing Web Search API

De [Bing Web Search API](../Bing-Web-Search/overview.md) retourneert webpagina's, afbeeldingen, video's, nieuws en meer. U de zoekopdrachten die naar deze API worden verzonden filteren om bepaalde inhoudstypen op te nemen of uit te sluiten.

Overweeg de Bing Web Search API te gebruiken in toepassingen die mogelijk moeten zoeken naar alle soorten relevante webinhoud. Als uw toepassing zoekt naar een specifiek type online-inhoud, u een van de onderstaande zoek-API's overwegen:

## <a name="content-specific-bing-search-apis"></a>Inhoudsspecifieke Bing-zoek-API's

De volgende Bing-zoek-API's retourneren specifieke inhoud van het web, zoals afbeeldingen, nieuws, lokale bedrijven en video's.

| Bing-API | Beschrijving |
| -- | -- |
| [Entiteiten zoeken](../Bing-Entities-Search/overview.md) | De Bing Entity Search API retourneert zoekresultaten met entiteiten, die mensen, plaatsen of dingen kunnen zijn. Afhankelijk van de query retourneert de API een of meer entiteiten die aan de zoekopdracht voldoen. De zoekopdracht kan opmerkelijke personen, lokale bedrijven, oriëntatiepunten, bestemmingen en meer bevatten. |
| [Afbeeldingen zoeken](../Bing-Image-Search/overview.md) | Met de Bing Image Search API u zoeken naar statische en geanimeerde afbeeldingen van hoge kwaliteit die vergelijkbaar zijn met [Bing.com/images.](https://www.Bing.com/images) U zoekopdrachten verfijnen om afbeeldingen op te nemen of uit te sluiten op kenmerk, inclusief grootte, kleur, licentie en versheid. U ook zoeken naar trending afbeeldingen, afbeeldingen uploaden om er inzicht in te krijgen en miniatuurvoorbeelden weergeven. |
| [Nieuws zoeken](../Bing-News-Search/search-the-web.md) | Met de Bing News Search API u nieuwsberichten vinden die vergelijkbaar zijn met [Bing.com/news.](https://www.Bing.com/news) De API retourneert nieuwsartikelen uit meerdere bronnen of specifieke domeinen. U in verschillende categorieën zoeken om trending artikelen, topverhalen en koppen te krijgen. |
| [Video's zoeken](../Bing-Video-Search/overview.md) | Met de Bing Video Search API u video's vinden op het web. Krijg trending video's, gerelateerde inhoud en miniatuurvoorbeelden. |
| [Visueel zoeken](../Bing-visual-search/overview.md) | Upload een afbeelding of gebruik een URL om er inzichtelijke informatie over te krijgen, zoals visueel vergelijkbare producten, afbeeldingen en gerelateerde zoekopdrachten. |
 [Zoeken in lokaal bedrijf](../bing-local-business-search/overview.md) | Met de Bing Local Business Search API kunnen uw toepassingen contact- en locatiegegevens vinden over lokale bedrijven op basis van zoekopdrachten. |

## <a name="the-bing-custom-search-api"></a>De Api voor aangepast zoeken bing

Als u een aangepaste zoekinstantie maakt met de [Bing Custom Search](../Bing-Custom-Search/overview.md) API, u een zoekervaring maken die alleen is gericht op inhoud en onderwerpen die u belangrijk vindt. Nadat u bijvoorbeeld de domeinen, websites en specifieke webpagina's hebt opgegeven die Bing zal doorzoeken, stemt Bing Custom Search de resultaten af op die specifieke inhoud. U de API's voor het aanpassen van bing-autosuggest, afbeelding en videozoeken opnemen om uw zoekervaring verder aan te passen.

## <a name="additional-bing-search-apis"></a>Aanvullende Bing-zoek-API's

Met de volgende Bing Search API's u uw zoekervaring verbeteren door deze te combineren met andere Bing-zoek-API's.

| API | Beschrijving |
| -- | -- |
| [Bing Automatische suggesties](../Bing-Autosuggest/get-suggested-search-terms.md) | Verbeter de zoekervaring van uw toepassing met de Bing Autosuggest API door voorgestelde zoekopdrachten in realtime terug te sturen.  |
| [Bing-statistieken](bing-web-stats.md) | Bing Statistics biedt analyses voor de Bing Search API's die uw toepassing gebruikt. Enkele van de beschikbare analyses omvatten oproepvolume, topquerytekenreeksen en geografische distributie. |

## <a name="next-steps"></a>Volgende stappen

* Prijsgegevens [pricing details](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) van Bing Search API
* In de [Bing-vereisten voor gebruik en weergave](./use-display-requirements.md) staan het acceptabele gebruik van de inhoud en informatie die is verkregen via de Bing Zoeken-API's.
