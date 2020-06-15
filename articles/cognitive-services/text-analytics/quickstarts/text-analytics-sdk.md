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
ms.date: 06/11/2020
ms.author: aahi
ms.custom: tracking-python
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: c475c17e5d196c0eedf38df83f782309143ad979
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/12/2020
ms.locfileid: "84735539"
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
> * De nieuwste stabiele versie van de Text Analytics-API is `3.0` .
>    * Volg alleen de instructies voor de versie die u gebruikt.
> * De code in dit artikel maakt gebruik van synchrone methoden en onbeveiligde referentie opslag om eenvoud. Voor productie scenario's wordt u aangeraden de batch-asynchrone methoden te gebruiken voor prestaties en schaal baarheid. Zie de onderstaande naslag documentatie.

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * De nieuwste stabiele versie van de Text Analytics-API is `3.0` .
> * De code in dit artikel maakt gebruik van synchrone methoden en onbeveiligde referentie opslag om eenvoud. Voor productie scenario's wordt u aangeraden de batch-asynchrone methoden te gebruiken voor prestaties en schaal baarheid. Zie de onderstaande naslag documentatie.

[!INCLUDE [Java quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

> [!IMPORTANT]
> * De nieuwste stabiele versie van de Text Analytics-API is `3.0` .
>    * Volg alleen de instructies voor de versie die u gebruikt.
> * De code in dit artikel maakt gebruik van synchrone methoden en onbeveiligde referentie opslag om eenvoud. Voor productie scenario's wordt u aangeraden de batch-asynchrone methoden te gebruiken voor prestaties en schaal baarheid. Zie de onderstaande naslag documentatie.
> * U kunt deze versie ook uitvoeren van de Text Analytics-client bibliotheek [in uw browser](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md).

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/nodejs-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

> [!IMPORTANT]
> * De nieuwste stabiele versie van de Text Analytics-API is `3.0` .
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
| Go       | [Versie 2,1](go-sdk.md) | 

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
