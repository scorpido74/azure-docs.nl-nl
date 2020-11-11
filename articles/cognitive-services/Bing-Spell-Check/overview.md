---
title: Wat is de Bing Spellingcontrole-API?
titleSuffix: Azure Cognitive Services
description: Meer informatie over de Bing spellingcontrole-API, die gebruikmaakt van machine learning en statistische automatische vertaling voor contextuele spellingcontrole.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: overview
ms.date: 12/19/2019
ms.author: aahi
ms.openlocfilehash: c0453fa99376cb6a5dba1e427cdc0deccb3e03de
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2020
ms.locfileid: "94367043"
---
# <a name="what-is-the-bing-spell-check-api"></a>Wat is de Bing Spellingcontrole-API?

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services overgezet. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](https://aka.ms/cogsvcs/bingmove) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](https://aka.ms/cogsvcs/bingmigration) voor migratie-instructies.

Met de Bing Spellingcontrole-API kunt u contextuele grammatica- en spellingcontrole toepassen op teksten. De meeste spellingcontroles zijn afhankelijk van regelsets op basis van een woordenlijst, maar de Bing-spellingcontrole maakt gebruik van machine learning en statistische automatische vertaling voor nauwkeurige en contextuele correcties. 

## <a name="features"></a>Functies

| Functie | Beschrijving |
|---------|---------|
|Meerdere modi van spellingcontroles     | Met meerdere modi van spellingcontroles kunt u correcties op het gebied van grammatica en/of spelling uitvoeren. |
|Herkenning van slang en informeel taalgebruik     | Herkenning van veelgebruikte uitdrukkingen en informele termen die in tekst worden gebruikt.         |
|Onderscheid maken tussen woorden die op elkaar lijken     | Correct gebruik herkennen van woorden die op elkaar lijken, maar een verschillende betekenis hebben (bijvoorbeeld ‘lijden’ en ‘leiden’)        |
|Herkenning van merken, titels en populair woordgebruik is ondersteund     | Herkenning van nieuwe merken, titels en andere populaire uitdrukkingen wanneer deze opkomen |

## <a name="workflow"></a>Werkstroom

De Bing Spellingcontrole-API kan eenvoudig worden aangeroepen vanuit elke programmeertaal waarmee HTTP-aanvragen kunnen worden gedaan en JSON-antwoorden kunnen worden geparseerd. De service is toegankelijk via de REST-API of de Bing Spellingcontrole-SDK's. 

1. Maak een [Account voor Cognitive Services-API](../cognitive-services-apis-create-account.md) met toegang tot de Bing Zoeken-API's. Als u geen Azure-abonnement hebt, kunt u een gratis account maken. 
2. Verzend een aanvraag naar de Bing Webzoekopdrachten-API.
3. Het JSON-antwoord parseren

## <a name="next-steps"></a>Volgende stappen

Probeer eerst de [interactieve demo](https://azure.microsoft.com/services/cognitive-services/spell-check/) van de Bing Spellingcontrole-API om te zien hoe u snel een grote verscheidenheid aan teksten kunt controleren.

Wanneer u klaar bent om de API aan te roepen, maakt u een [account voor Cognitive Services-API](../../cognitive-services/cognitive-services-apis-create-account.md). Als u geen Azure-abonnement hebt, kunt u gratis [een account maken](https://azure.microsoft.com/free/cognitive-services/).

U kunt ook de [hubpagina voor Bing Search API](../bing-web-search/overview.md) bezoeken om de andere beschikbare API's te verkennen.