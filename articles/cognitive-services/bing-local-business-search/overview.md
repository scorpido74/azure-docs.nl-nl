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
ms.date: 09/13/2019
ms.author: aahi
ms.openlocfilehash: bdada613ef46881bbf051144efded541ac3d0974
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996693"
---
# <a name="what-is-bing-local-business-search"></a>Wat is zoeken in lokale bedrijven in Bing?
De Bing Local Business Search-API is een REST-service waarmee uw toepassingen informatie kunnen vinden over lokale bedrijven op basis van zoek query's. Bijvoorbeeld `q=<business-name> in Redmond, Washington`, of `q=Italian restaurants near me`. 

## <a name="features"></a>Functies
| Functie | Description |  
| -- | -- | 
| [Lokale bedrijven en locaties zoeken](quickstarts/local-quickstart.md) | De Bing Local Business Search-API krijgt gelokaliseerde resultaten van een query. Tot de resultaten behoren een URL voor de website van het bedrijf en de weergave tekst, het telefoon nummer en de geografische locatie, waaronder: GPS-coördinaten, plaats, straat adres |  
| [Lokale resultaten filteren met geografische grenzen](specify-geographic-search.md) | Voeg coördinaten als zoek parameters toe om de resultaten te beperken tot een specifiek geografisch gebied, opgegeven door een omsluitend gebied of vier kant vak. | 
| [Lokale bedrijfs resultaten filteren op categorie](local-categories.md) | Zoek naar lokale bedrijfs resultaten per categorie. Deze optie maakt gebruik van omgekeerde IP-locatie of GPS-coördinaten van de aanroeper om gelokaliseerde resultaten te retour neren in verschillende categorieën van bedrijven.|

## <a name="workflow"></a>Werkstroom
Roep de Bing lokale Business Search-API aan vanuit elke programmeer taal die HTTP-aanvragen kan maken en JSON-antwoorden kan parseren. Deze service is toegankelijk via de REST API.
 
1. Maak een [Cognitive Services-API-account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) met toegang tot de Bing zoeken-API's. Als u geen Azure-abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) maken.   
2. URL Codeer uw zoek termen voor `q=""` de query parameter. Bijvoorbeeld, `q=nearby+restaurant` of `q=nearby%20restaurant`. Stel de paginering ook in, indien nodig. 
3. Een [aanvraag verzenden naar de Bing lokale zakelijke zoek-API](quickstarts/local-quickstart.md) 
4. Het JSON-antwoord parseren 

> [!NOTE]
> Lokale bedrijfs zoekopdrachten ondersteunt momenteel alleen de `en-US` markt. 
> [!NOTE]
> Op dit moment biedt lokale zakelijke zoek acties geen ondersteuning voor automatische suggesties. 

## <a name="next-steps"></a>Volgende stappen
- [Query en antwoord](local-search-query-response.md)
- [Snelstartgids voor lokale zakelijke Zoek opdrachten](quickstarts/local-quickstart.md)
- [Naslag informatie over lokale zoek-API'S voor bedrijven](local-search-reference.md)
- [Vereisten voor gebruik en weergave](use-display-requirements.md)
