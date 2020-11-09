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
ms.openlocfilehash: 6b715d4b801159b451957c30ee9ef7b406f3a2f4
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095370"
---
# <a name="what-is-bing-local-business-search"></a>Wat is Bing Local Business Search?

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services overgezet. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](https://aka.ms/cogsvcs/bingmove) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](https://aka.ms/cogsvcs/bingmigration) voor migratie-instructies.
De Bing Local Business Search-API is een RESTful-service waarmee uw toepassingen informatie over lokale bedrijven kunnen vinden op basis van zoekopdrachten. Bijvoorbeeld `q=<business-name> in Redmond, Washington` of `q=Italian restaurants near me`. 

## <a name="features"></a>Functies
| Functie | Beschrijving |  
| -- | -- | 
| [Lokale bedrijven en plaatsen zoeken](quickstarts/local-quickstart.md) | De Bing Local Business Search-API geeft gelokaliseerde resultaten weer op basis van een zoekopdracht. De resultaten omvatten een URL naar de bedrijfswebsite, tekst, een telefoonnummer en een geografische locatie, waaronder: GPS-coördinaten, plaats, adres |  
| [Lokale resultaten filteren op geografische grenzen](specify-geographic-search.md) | Voeg coördinaten toe als zoekparameters om de resultaten te beperken tot een specifiek geografisch gebied, op basis van een straal of een vierkant begrenzingsvak. | 
| [Lokale bedrijfsresultaten filteren op categorie](local-categories.md) | Zoek naar lokale bedrijfsresultaten per categorie. Deze optie maakt gebruik van omgekeerde IP-locatie of GPS-coördinaten van de aanroeper om gelokaliseerde resultaten in verschillende bedrijfscategorieën te retourneren.|

## <a name="workflow"></a>Werkstroom
Roep de Bing Local Business Search-API aan vanuit een programmeertaal waarmee HTTP-aanvragen kunnen worden gedaan en JSON-antwoorden kunnen worden geparseerd. Deze service is toegankelijk via de REST API.
 
1. Maak een [account voor de Cognitive Services-API](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) met toegang tot de Bing Search-API's. Als u geen Azure-abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/cognitive-services/) maken.   
2. Pas URL-codering toe op uw zoektermen voor de zoekopdrachtparameter `q=""`. Bijvoorbeeld `q=nearby+restaurant` of `q=nearby%20restaurant`. Stel indien nodig ook de paginering in. 
3. Stuur een [aanvraag naar de Bing Local Business Search-API](quickstarts/local-quickstart.md). 
4. Het JSON-antwoord parseren 

> [!NOTE]
> Local Business Search biedt momenteel: 
> * Alleen ondersteuning voor de markt `en-US` 
> * Geen ondersteuning voor Bing Automatische suggesties 

## <a name="next-steps"></a>Volgende stappen
- [Zoekopdrachten en antwoorden](local-search-query-response.md)
- [Quickstart over Business Search](quickstarts/local-quickstart.md)
- [Referentie voor de Bing-API voor zoeken naar lokale bedrijven](local-search-reference.md)
- [Vereisten voor gebruik en weergave](use-display-requirements.md)
