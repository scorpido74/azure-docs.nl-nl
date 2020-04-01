---
title: Wat is de Bing-API voor zoeken naar lokale bedrijven?
titleSuffix: Azure Cognitive Services
description: De Bing-API voor zoeken naar lokale bedrijven is een RESTful-service waarmee uw toepassingen informatie over lokale plaatsen en bedrijven kunnen vinden op basis van zoekquery's.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: overview
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 0006761126eb1d561da7eeff97e8a9928d62ddb0
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478932"
---
# <a name="what-is-bing-local-business-search"></a>Wat is Bing Local Business Search?
De Bing Local Business Search API is een RESTful-service waarmee uw toepassingen informatie over lokale bedrijven kunnen vinden op basis van zoekopdrachten. Bijvoorbeeld, `q=<business-name> in Redmond, Washington`of `q=Italian restaurants near me`. 

## <a name="features"></a>Functies
| Functie | Beschrijving |  
| -- | -- | 
| [Lokale bedrijven en locaties zoeken](quickstarts/local-quickstart.md) | De Bing Local Business Search API krijgt gelokaliseerde resultaten van een query. De resultaten omvatten een URL voor de website van het bedrijf en weergavetekst, telefoonnummer en geografische locatie, waaronder: GPS-coördinaten, stad, adres |  
| [Lokale resultaten filteren met geografische grenzen](specify-geographic-search.md) | Voeg coördinaten toe als zoekparameters om resultaten te beperken tot een specifiek geografisch gebied, opgegeven door een cirkelvormig gebied of een vierkant selectiekader. | 
| [Lokale bedrijfsresultaten filteren op categorie](local-categories.md) | Zoek naar lokale bedrijfsresultaten per categorie. Deze optie maakt gebruik van omgekeerde IP-locatie of GPS-coördinaten van de beller om gelokaliseerde resultaten in verschillende categorieën van het bedrijfsleven terug te keren.|

## <a name="workflow"></a>Werkstroom
Bel de Bing Local Business Search API vanuit elke programmeertaal die HTTP-verzoeken kan indienen en JSON-antwoorden kan ontleden. Deze service is toegankelijk via de REST API.
 
1. Maak een [API-account voor Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) met toegang tot de Bing Search API's. Als u geen Azure-abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) maken.   
2. URL codeert uw `q=""` zoektermen voor de queryparameter. Bijvoorbeeld `q=nearby+restaurant` of `q=nearby%20restaurant`. Stel pagination ook, indien nodig. 
3. Een [aanvraag verzenden naar de Bing Local Business Search API](quickstarts/local-quickstart.md) 
4. Het JSON-antwoord parseren 

> [!NOTE]
> Momenteel zoekt Lokaal bedrijf: 
> * Ondersteunt alleen `en-US` de markt. 
> * Ondersteunt Bing Autosuggest niet. 

## <a name="next-steps"></a>Volgende stappen
- [Query en antwoord](local-search-query-response.md)
- [Zoeken in lokaal bedrijf snel gestart](quickstarts/local-quickstart.md)
- [Referentie voor de Bing-API voor zoeken naar lokale bedrijven](local-search-reference.md)
- [Vereisten voor gebruik en weergave](use-display-requirements.md)
