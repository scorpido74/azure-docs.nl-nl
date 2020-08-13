---
title: 'Quickstart: Text Analytics-clientbibliotheek v3 | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Gebruik deze quickstart om uw toepassingen te verbinden met de Text Analytics-API van Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/27/2020
ms.author: aahi
ms.custom: devx-track-python, devx-track-javascript
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: ba53b4a4765a2b3db0aa048a990a0a48f74682d0
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87874976"
---
# <a name="quickstart-use-the-text-analytics-client-library"></a>Quickstart: De Text Analytics-clientbibliotheek gebruiken

Aan de slag met de Text Analytics-clientbibliotheek. Volg deze stappen om het pakket te installeren en de voorbeeldcode voor basistaken uit te proberen.

Gebruik de Text Analytics-clientbibliotheek om het volgende uit te voeren:

* Sentimentanalyse
* Taaldetectie
* Herkenning van entiteiten
* Sleuteltermextractie

::: zone pivot="programming-language-csharp"

> [!IMPORTANT]
> * De meest recente stabiele versie van de Text Analytics-API is `3.0`.
>    * Volg alleen de instructies voor de versie die u gebruikt.
> * De code in dit artikel maakt gebruik van synchrone methoden en onbeveiligde referentieopslag voor de eenvoud. Voor productiescenario's wordt aanbevolen om de batch-asynchrone methoden te gebruiken voor prestaties en schaalbaarheid. Zie de referentiedocumentatie hieronder.

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * De meest recente stabiele versie van de Text Analytics-API is `3.0`.
> * De code in dit artikel maakt gebruik van synchrone methoden en onbeveiligde referentieopslag voor de eenvoud. Voor productiescenario's wordt aanbevolen om de batch-asynchrone methoden te gebruiken voor prestaties en schaalbaarheid. Zie de referentiedocumentatie hieronder.

[!INCLUDE [Java quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

> [!IMPORTANT]
> * De meest recente stabiele versie van de Text Analytics-API is `3.0`.
>    * Volg alleen de instructies voor de versie die u gebruikt.
> * De code in dit artikel maakt gebruik van synchrone methoden en onbeveiligde referentieopslag voor de eenvoud. Voor productiescenario's wordt aanbevolen om de batch-asynchrone methoden te gebruiken voor prestaties en schaalbaarheid. Zie de referentiedocumentatie hieronder.
> * U kunt deze versie van de Text Analytics-clientbibliotheek ook uitvoeren [in de browser](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md).

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/nodejs-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

> [!IMPORTANT]
> * De meest recente stabiele versie van de Text Analytics-API is `3.0`.
>    * Volg alleen de instructies voor de versie die u gebruikt.
> * De code in dit artikel maakt gebruik van synchrone methoden en onbeveiligde referentieopslag voor de eenvoud. Voor productiescenario's wordt aanbevolen om de batch-asynchrone methoden te gebruiken voor prestaties en schaalbaarheid. Zie de referentiedocumentatie hieronder. 

[!INCLUDE [Python quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-other"

## <a name="additional-language-support"></a>Aanvullende computertaalondersteuning

Als u op dit tabblad hebt geklikt, ziet u waarschijnlijk geen quickstart in uw favoriete computertaal. Maakt u zich geen zorgen. Er zijn nog meer quickstarts beschikbaar. Gebruik de tabel om het juiste voorbeeld voor uw programmeertaal te vinden.

| Taal | Beschikbare versie | 
|----------|------------------------|
| Ruby     | [Versie 2.1](ruby-sdk.md) | 
| Aan de slag       | [Versie 2.1](go-sdk.md) | 

::: zone-end

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een oplossing verkennen](../text-analytics-user-scenarios.md#analyze-recorded-inbound-customer-calls)

* [Overzicht van Text Analytics](../overview.md)
* [Sentimentanalyse](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Herkenning van entiteiten](../how-tos/text-analytics-how-to-entity-linking.md)
* [Taal detecteren](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Taalherkenning](../how-tos/text-analytics-how-to-language-detection.md)
