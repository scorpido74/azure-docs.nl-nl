---
title: Wat is Bing Visual Search-API?
titleSuffix: Azure Cognitive Services
description: Bing Visual Search biedt details of inzichten over een afbeelding, zoals vergelijkbare afbeeldingen of winkelbronnen.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: overview
ms.date: 12/19/2019
ms.author: scottwhi
ms.openlocfilehash: 52e3e65fb4f42530373decca547674a28a60f759
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75446659"
---
# <a name="what-is-the-bing-visual-search-api"></a>Wat is Bing Visual Search-API?

De Bing Visual Search API retourneert inzichten voor een afbeelding. U een afbeelding uploaden of een URL aan een afbeelding opgeven. Inzichten zijn visueel vergelijkbare afbeeldingen, winkelbronnen, webpagina's met de afbeelding en meer. Inzichten die worden geretourneerd door de Bing Visual Search API zijn vergelijkbaar met die op Bing.com/images. 

Als u de [Bing Image Search API](../bing-image-search/overview.md)gebruikt, u insight tokens uit de zoekresultaten van die API gebruiken voor uw Bing Visual Search in plaats van een afbeelding te uploaden.

> [!IMPORTANT]
> Als u afbeeldingsinzichten ophaalt met behulp van Bing Afbeeldingen zoeken-API, kunt u overwegen om over te schakelen naar Bing Visual Search-API, omdat deze uitgebreidere inzichten biedt.

## <a name="insights"></a>Inzichten

U de volgende inzichten ontdekken met Bing Visual Search:

| Inzicht                              | Beschrijving |
|--------------------------------------|-------------|
| Visueel vergelijkbare afbeeldingen              | Een lijst met afbeeldingen die visueel vergelijkbaar zijn met de ingevoerde afbeelding. |
| Visueel vergelijkbare producten            | Producten die visueel vergelijkbaar zijn met het weergegeven product.            |
| Winkelbronnen                     | Een lijst met plaatsen waar u het artikel kunt kopen dat wordt weergegeven op de ingevoerde afbeelding.            |
| Verwante zoekopdrachten                     | Een lijst met verwante zoekopdrachten die door anderen zijn uitgevoerd, of die zijn gebaseerd op de inhoud van de afbeelding.            |
| Webpagina's met de afbeelding     | Webpagina's die de ingevoerde afbeelding bevatten.            |
| Recepten                              | Webpagina's met recepten voor het maken van het gerecht in de invoerafbeelding.            |
| Entiteiten                             | Bekende mensen, plaatsen en zo. |

Bing Visual Search retourneert naast inzichten een verscheidenheid aan termen (dat wil zeggen tags) die zijn afgeleid van de invoerafbeelding. De tags stellen gebruikers in staat om concepten in de afbeelding te verkennen. Als de invoerafbeelding bijvoorbeeld van een beroemde atleet is, kan een van de tags de naam van de atleet zijn, een andere tag kan Sport zijn. Of als de invoerafbeelding van een appeltaart is, kunnen de tags Apple Pie, Pies en Desserts zijn.

De visuele zoekresultaten van Bing bevatten ook selectiekaders voor regio's die van belang zijn voor de afbeelding. Als de afbeelding bijvoorbeeld meerdere beroemdheden bevat, kunnen de resultaten selectievakken bevatten voor elk van de erkende beroemdheden. Of als Bing een product of kleding in de afbeelding herkent, kan het resultaat een selectiekader voor het herkende item bevatten.

## <a name="workflow"></a>Werkstroom

Bing Visual Search-API is een RESTful-webservice die eenvoudig kan worden aangeroepen vanuit elke programmeertaal waarmee HTTP-aanvragen kunnen worden gedaan en JSON kan worden geparseerd. U de REST API of de SDK voor de service gebruiken.

1. Maak een [Cognitive Services-account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) aan om toegang te krijgen tot de Bing Search API's. Als u geen Azure-abonnement hebt, kunt u [gratis een account maken](https://azure.microsoft.com/free/). U uw abonnementssleutel van de [Azure-portal](../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) ophalen nadat u uw account of [Azure-website](https://azure.microsoft.com/try/cognitive-services/my-apis) hebt gemaakt nadat u een gratis proefversie hebt geactiveerd.
2. Stuur een aanvraag naar de API met een geldige zoekopdracht.
3. Verwerk de API-reactie door het geretourneerde JSON-bericht te parseren.

## <a name="next-steps"></a>Volgende stappen

Probeer eerst de interactieve [demo](https://azure.microsoft.com/services/cognitive-services/bing-visual-search/)van de Bing Visual Search API.
De demo laat zien hoe u snel een zoekopdracht aanpassen en het web doorzoeken naar afbeeldingen.

Om snel aan de slag te gaan met je eerste aanvraag, zie de quickstarts: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md).

## <a name="see-also"></a>Zie ook

* De verwijzing [Afbeeldingen - Visueel zoeken](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch) beschrijft definities en informatie over de eindpunten, opgevraagde koppen, antwoorden en queryparameters die u gebruiken om op afbeeldingen gebaseerde zoekresultaten op te vragen.

* De [gebruiks- en weergavevereisten van Bing Search API](../bing-web-search/use-display-requirements.md) specificeren acceptabel gebruik van de inhoud en informatie die is verkregen via de Bing-zoek-API's.

* Ga naar de [hubpagina van bing Search API](../bing-web-search/search-the-web.md) om de andere beschikbare API's te verkennen.