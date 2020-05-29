---
title: 'Snelstartgids: Text Analytics-client bibliotheek v3 | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Gebruik deze Quick Start om uw toepassingen te verbinden met de Text Analytics-API van Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 05/25/2020
ms.author: aahi
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: b51dcc214e099ed573fa297d979cfc55ba1513b9
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140705"
---
# <a name="quickstart-use-the-text-analytics-client-library"></a>Snelstartgids: de Text Analytics-client bibliotheek gebruiken

Aan de slag met de Text Analytics-client bibliotheek. Volg deze stappen om het pakket te installeren en de voorbeeld code voor basis taken uit te proberen.

De Text Analytics-client bibliotheek gebruiken om uit te voeren:

* Sentimentanalyse
* Taaldetectie
* Herkenning van entiteiten
* Sleuteltermextractie

::: zone pivot="programming-language-csharp"

> [!IMPORTANT]
> * De laatste stabiele versie van de Text Analytics-client bibliotheek is `3.0` .
>    * Volg alleen de instructies voor de versie die u gebruikt.
> * De code in dit artikel maakt gebruik van synchrone methoden en onbeveiligde referentie opslag om eenvoud. Voor productie scenario's wordt u aangeraden de batch-asynchrone methoden te gebruiken voor prestaties en schaal baarheid. Zie de onderstaande naslag documentatie.

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * De laatste stabiele versie van de Text Analytics-client bibliotheek is `3.0` .
> * De code in dit artikel maakt gebruik van synchrone methoden en onbeveiligde referentie opslag om eenvoud. Voor productie scenario's wordt u aangeraden de batch-asynchrone methoden te gebruiken voor prestaties en schaal baarheid. Zie de onderstaande naslag documentatie.

[!INCLUDE [Java quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

> [!IMPORTANT]
> * De laatste stabiele versie van de Text Analytics-client bibliotheek is `3.0` .
>    * Volg alleen de instructies voor de versie die u gebruikt.
> * De code in dit artikel maakt gebruik van synchrone methoden en onbeveiligde referentie opslag om eenvoud. Voor productie scenario's wordt u aangeraden de batch-asynchrone methoden te gebruiken voor prestaties en schaal baarheid. Zie de onderstaande naslag documentatie.
> * U kunt deze versie ook uitvoeren van de Text Analytics-client bibliotheek [in uw browser](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md).

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/nodejs-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

> [!IMPORTANT]
> * De laatste stabiele versie van de Text Analytics-client bibliotheek is `3.0` .
>    * Volg alleen de instructies voor de versie die u gebruikt.
> * De code in dit artikel maakt gebruik van synchrone methoden en onbeveiligde referentie opslag om eenvoud. Voor productie scenario's wordt u aangeraden de batch-asynchrone methoden te gebruiken voor prestaties en schaal baarheid. Zie de onderstaande naslag documentatie. 

[!INCLUDE [Python quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-other"

## <a name="additional-language-support"></a>Aanvullende taal ondersteuning

Als u op dit tabblad hebt geklikt, ziet u waarschijnlijk geen Snelstartgids in uw favoriete programmeer taal. U hebt geen zorgen. er zijn nog meer Quick starts beschikbaar. Gebruik de tabel om het juiste voor beeld voor uw programmeer taal te vinden.

| Taal | Beschik bare versie | 
|----------|------------------------|
| Ruby     | [Versie 2,1](ruby-sdk.md) | 
| Aan de slag       | [Versie 2,1](go-sdk.md) | 

::: zone-end

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resource groep verwijderen. Als u de resource groep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een oplossing verkennen](../text-analytics-user-scenarios.md#analyze-recorded-inbound-customer-calls)

* [Overzicht van Text Analytics](../overview.md)
* [Sentiment analyse](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Entiteit herkenning](../how-tos/text-analytics-how-to-entity-linking.md)
* [Taal detecteren](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Taal herkenning](../how-tos/text-analytics-how-to-language-detection.md)
