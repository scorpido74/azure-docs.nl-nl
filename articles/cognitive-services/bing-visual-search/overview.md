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
ms.date: 08/30/2019
ms.author: scottwhi
ms.openlocfilehash: 22a8bb2609439a82a825bf7f542709d46771ad92
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195217"
---
# <a name="what-is-the-bing-visual-search-api"></a>Wat is Bing Visual Search-API?

Het Bing Visual Search-API retourneert inzichten voor een afbeelding. U kunt een installatie kopie uploaden of een URL naar een afbeelding opgeven. Inzichten zijn visueel vergelijkbaar met afbeeldingen, Shop ping, webpagina's die de afbeelding bevatten, en meer. Inzichten die door de Bing Visual Search-API zijn geretourneerd, zijn vergelijkbaar met die van Bing.com/images. 

Als u de [Bing afbeeldingen zoeken-API](../bing-image-search/overview.md)gebruikt, kunt u Insight-tokens van de zoek resultaten van de API gebruiken voor uw Bing Visual Search in plaats van een installatie kopie te uploaden.

> [!IMPORTANT]
> Als u afbeeldingsinzichten ophaalt met behulp van Bing Afbeeldingen zoeken-API, kunt u overwegen om over te schakelen naar Bing Visual Search-API, omdat deze uitgebreidere inzichten biedt.

## <a name="insights"></a>Inzichten

U kunt de volgende inzichten vinden met behulp van Bing Visual Search:

| Inzicht                              | Description |
|--------------------------------------|-------------|
| Visueel vergelijkbare afbeeldingen              | Een lijst met afbeeldingen die visueel vergelijkbaar zijn met de ingevoerde afbeelding. |
| Visueel vergelijkbare producten            | Producten die visueel vergelijkbaar zijn met het weergegeven product.            |
| Winkelbronnen                     | Een lijst met plaatsen waar u het artikel kunt kopen dat wordt weergegeven op de ingevoerde afbeelding.            |
| Verwante zoekopdrachten                     | Een lijst met verwante zoekopdrachten die door anderen zijn uitgevoerd, of die zijn gebaseerd op de inhoud van de afbeelding.            |
| Webpagina's die de afbeelding bevatten     | Webpagina's die de ingevoerde afbeelding bevatten.            |
| Recepten                              | Webpagina's die recepten bevatten voor het maken van de schaal die wordt weer gegeven in de invoer afbeelding.            |
| Entiteiten                             | Bekende personen, plaatsen en dingen. |

Naast inzichten retourneert Bing Visual Search verschillende termen (dat wil zeggen, Tags) die zijn afgeleid van de invoer afbeelding. De Tags stellen gebruikers in staat om concepten te verkennen die in de installatie kopie zijn gevonden. Als de invoer installatie kopie bijvoorbeeld van een beroemde-Athlete is, kan een van de tags de naam van de Athlete zijn. een andere tag kan sporten zijn. Of, als de invoer afbeelding van een Apple-cirkel is, kunnen de Tags Apple-cirkel-, cirkeldiagram-en desserts zijn.

Bing Visual Search resultaten bevatten ook selectie kaders voor regio's die van belang zijn in de afbeelding. Als de installatie kopie bijvoorbeeld meerdere beroemdheden bevat, bevatten de resultaten mogelijk selectie kaders voor elk van de herkende beroemdheden. Als Bing een product of kleren in de afbeelding herkent, kan het resultaat ook een selectie kader voor het herkende item bevatten.

## <a name="workflow"></a>Werkstroom

Bing Visual Search-API is een RESTful-webservice die eenvoudig kan worden aangeroepen vanuit elke programmeertaal waarmee HTTP-aanvragen kunnen worden gedaan en JSON kan worden geparseerd. U kunt de REST API of de SDK voor de service gebruiken.

1. Maak een [Cognitive Services-account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) om toegang te krijgen tot de Bing zoeken-API's. Als u geen Azure-abonnement hebt, kunt u [gratis een account maken](https://azure.microsoft.com/free/). Nadat u een gratis proef versie hebt geactiveerd, kunt u uw abonnements sleutel ophalen uit de [Azure Portal](../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) na het maken van uw account of [Azure-website](https://azure.microsoft.com/try/cognitive-services/my-apis) .
2. Een aanvraag verzenden naar de API met een geldige Zoek query.
3. Verwerk de API-reactie door het geretourneerde JSON-bericht te parseren.

## <a name="next-steps"></a>Volgende stappen

Probeer eerst de [interactieve demo](https://azure.microsoft.com/services/cognitive-services/bing-visual-search/)van Bing Visual Search-API.
De demo laat zien hoe u snel een zoek opdracht kunt aanpassen en op op het web voor installatie kopieën.

Zie deze quickstarts om snel aan de slag te gaan met uw eerste aanvraag: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [Node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md).

## <a name="see-also"></a>Zie ook

* De naslag informatie voor [installatie kopieën-Visual Search](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch) beschrijft definities en gegevens over de eind punten, aanvraag headers, reacties en query parameters die u kunt gebruiken om op afbeeldingen gebaseerde Zoek resultaten aan te vragen.

* De vereisten voor het gebruik van de [Bing Search-API en de weer gave bevatten](../bing-web-search/use-display-requirements.md) acceptabel gebruik van de inhoud en gegevens die zijn verkregen via de Bing zoeken-api's.
