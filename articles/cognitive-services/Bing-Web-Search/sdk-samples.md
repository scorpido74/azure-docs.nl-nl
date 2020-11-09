---
title: Voorbeelden van de Bing Webzoekopdrachten-SDK
titleSuffix: Azure Cognitive Services
description: Gebruik de Bing Webzoekopdrachten-SDK om zoekmogelijkheden toe te voegen aan uw toepassing in Python, Node.js, C# of Java.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: sample
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: b31335df7c1dc48f82699aa3676561721474071d
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93076211"
---
# <a name="bing-web-search-sdk-samples"></a>Voorbeelden van de Bing Webzoekopdrachten-SDK

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services verplaatst. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht volgens het proces dat [hier](https://aka.ms/cogsvcs/bingmove) is beschreven.
> Bing Search-API's ingericht met behulp van Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Zie [Bing Search Services](https://aka.ms/cogsvcs/bingmigration) voor migratie-instructies.

De Bing Webzoekopdrachten-SDK is beschikbaar voor Python, Node.js, C# en Java. Op GitHub vindt u codevoorbeelden, vereisten en bouwinstructies. De volgende scenario's worden behandeld:

* Een afzonderlijk woord opvragen en zowel de naam als de URL van het eerste resultaat afdrukken voor webpagina's, afbeeldingen, nieuwsartikelen en video's.
* Een zin opvragen, het aantal resultaten controleren en zowel de naam als de URL van het eerste videoresultaat afdrukken.
* Een zoekterm opvragen met antwoordfilters ingesteld op `news` en details van nieuwsresultaten afdrukken.
* Een zoekterm opvragen met de parameters `answerCount` en `promote`, en vervolgens details van de resultaten afdrukken.

## <a name="sdks-and-libraries"></a>SDK’s en bibliotheken

Gebruik deze koppelingen voor toegang tot de SDK voor de taal van uw voorkeur.

* Aan de slag met [Python-voorbeelden](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)
  * Zie ook [Python-bibliotheken](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-search-websearch) voor definities en afhankelijkheden.
* Aan de slag met [Node.js-voorbeelden](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
  * Zie ook [Node.js Web Search](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesWebSearch).
* Aan de slag met [.NET-voorbeelden](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
  * [NuGet-pakket](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.WebSearch/1.2.0)
  * Zie ook [.NET-bibliotheken](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingWebSearch) voor definities en afhankelijkheden.
* Aan de slag met [Java-voorbeelden](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)
  * Zie ook [Java-bibliotheken](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingWebSearch) voor definities en afhankelijkheden.
