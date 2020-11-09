---
title: Wat is de Bing Nieuws zoeken-API?
titleSuffix: Azure Cognitive Services
description: Ontdek hoe u de Bing Nieuws zoeken-API gebruikt om op internet te zoeken naar nieuws in meerdere categorieën, waaronder in kopteksten en in populaire onderwerpen.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 9ba749d671e24e86e2cd0a299e98ba03e47cf354
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101677"
---
# <a name="what-is-the-bing-news-search-api"></a>Wat is de Bing Nieuws zoeken-API?

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services verplaatst. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht volgens het proces dat [hier](https://aka.ms/cogsvcs/bingmove) is beschreven.
> Bing Search-API's ingericht met behulp van Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Zie [Bing Search Services](https://aka.ms/cogsvcs/bingmigration) voor migratie-instructies.

Met de Bing Nieuws zoeken-API kunt u de cognitieve zoekmogelijkheden makkelijk in uw toepassingen integreren. De API biedt een soortgelijke ervaring als [Bing News](https://www.bing.com/news). U kunt dus query's doorzoeken en relevante nieuwsartikelen ontvangen.

Let erop dat met de Bing Nieuws zoeken-API de resultaten alleen zoekresultaten over nieuws bevatten. Gebruik de [Bing Webzoekopdrachten-API](../bing-web-search/search-the-web.md), [Video's zoeken-API](../bing-video-search/search-the-web.md) en [Afbeeldingen zoeken-API](../bing-image-search/overview.md) voor andere typen webinhoud.

## <a name="bing-news-search-api-features"></a>Functies van Bing Nieuws zoeken-API

Terwijl de Bing Nieuws zoeken-API hoofdzakelijk relevante nieuwsartikelen zoekt en retourneert, biedt het een aantal functies voor het intelligent en gericht ophalen van nieuws op internet.

|Functie  |Beschrijving  |
|---------|---------|
|[Zoektermen voorstellen en gebruiken](concepts/search-for-news.md#suggest-and-use-search-terms)     | Verbeter uw zoekervaring met de [Automatische suggestie-API voor Bing](../bing-autosuggest/get-suggested-search-terms.md) om voorgestelde zoektermen weer te geven wanneer deze worden getypt.         |
|[Algemeen nieuws ophalen](concepts/search-for-news.md#get-general-news)     | Zoek nieuws door een zoekopdracht te versturen naar de Bing Nieuws zoeken-API, waarna u een lijst met relevante nieuwsartikelen retour krijgt.           |
|[Belangrijkste nieuws van vandaag](concepts/search-for-news.md#get-todays-top-news)      | Haal de belangrijkste nieuwsberichten van vandaag op, in alle categorieën.       |
|[Nieuws per categorie](concepts/search-for-news.md)     | Zoek nieuws in bepaalde categorieën.        | 
|[Nieuwskoppen](concepts/search-for-news.md)     | Zoek de belangrijkste koppen, in alle categorieën.         |

## <a name="workflow"></a>Werkstroom

De Bing Nieuws zoeken-API is een RESTful-webservice die eenvoudig kan worden aangeroepen vanuit elke programmeertaal waarmee HTTP-aanvragen kunnen worden gedaan en JSON kan worden geparseerd. U kunt de service gebruiken met de REST API of de SDK.

1. Maak een [Account voor Cognitive Services-API](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) met toegang tot de Bing Zoeken-API's. Als u geen Azure-abonnement hebt, kunt u gratis [een account maken](https://azure.microsoft.com/free/cognitive-services/).
2. Verzend een aanvraag naar de API met een geldige zoekquery.
3. Verwerk de API-reactie door het geretourneerde JSON-bericht te parseren.

## <a name="next-steps"></a>Volgende stappen

Probeer eerst de [interactieve demo](https://azure.microsoft.com/services/cognitive-services/bing-news-search-api/) van de Bing Nieuws zoeken-API uit. In deze demo ziet u hoe u snel een zoekquery kunt aanpassen en nieuws op internet kunt zoeken.

Als u snel aan de slag wilt met uw eerste API-aanvraag, kunt u een quickstart proberen voor de [REST API](quickstart.md) of een van de [SDK's](sdk.md).

## <a name="see-also"></a>Zie tevens

* De referentiesectie [Bing Nieuws zoeken-API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) bevat definities en informatie over de eindpunten, headers, API-antwoorden en queryparameters waarmee u zoekresultaten voor afbeeldingen kunt aanvragen.
* In de [Bing-vereisten voor gebruik en weergave](./useanddisplayrequirements.md) staan het acceptabele gebruik van de inhoud en informatie die is verkregen via de Bing Zoeken-API's.
* Bezoek de [hubpagina voor de Bing Search-API](../bing-web-search/search-the-web.md) om de andere beschikbare API's te verkennen.