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
ms.date: 03/12/2020
ms.author: aahi
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: 1f5658c6fa52caa67de1f60c50048014dd77af13
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371312"
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
> * De nieuwste preview-versie van de Text Analytics-API is `3.0-preview`, dat een open bare Preview bevat voor verbeterde [sentimentanalyse](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) en [benoemde entiteits herkenning (ner)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features). De nieuwste stabiele versie is `2.1`.
>    * Volg alleen de instructies voor de versie die u gebruikt.
> * De code in dit artikel maakt gebruik van synchrone methoden en onbeveiligde referentie opslag om eenvoud. Voor productie scenario's wordt u aangeraden de batch-asynchrone methoden te gebruiken voor prestaties en schaal baarheid. Zie de onderstaande naslag documentatie.

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * Deze Quick start is alleen voor versie `3.0-preview` van de Text Analytics-client bibliotheek, die een open bare Preview bevat voor verbeterde [sentimentanalyse](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) en [named entity Recognition (ner)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features).
> * De code in dit artikel maakt gebruik van synchrone methoden en onbeveiligde referentie opslag om eenvoud. Voor productie scenario's wordt u aangeraden de batch-asynchrone methoden te gebruiken voor prestaties en schaal baarheid. Zie de onderstaande naslag documentatie.

[!INCLUDE [Java quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

> [!IMPORTANT]
> * De nieuwste preview-versie van de Text Analytics-API is `3.0-preview`, dat een open bare Preview bevat voor verbeterde [sentimentanalyse](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) en [benoemde entiteits herkenning (ner)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features). De nieuwste stabiele versie is `2.1`.
>    * Volg alleen de instructies voor de versie die u gebruikt.
> * De code in dit artikel maakt gebruik van synchrone methoden en onbeveiligde referentie opslag om eenvoud. Voor productie scenario's wordt u aangeraden de batch-asynchrone methoden te gebruiken voor prestaties en schaal baarheid. Zie de onderstaande naslag documentatie.
> * U kunt deze versie ook uitvoeren van de Text Analytics-client bibliotheek [in uw browser](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md).

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/nodejs-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

> [!IMPORTANT]
> * De nieuwste preview-versie van de Text Analytics-API is `3.0-preview`, dat een open bare Preview bevat voor verbeterde [sentimentanalyse](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) en [benoemde entiteits herkenning (ner)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features). De nieuwste stabiele versie is `2.1`.
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
| OK       | [Versie 2,1](go-sdk.md) | 

::: zone-end

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resource groep verwijderen. Als u de resource groep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een oplossing verkennen](../text-analytics-user-scenarios.md#analyze-recorded-inbound-customer-calls)

* [Overzicht van Text Analytics](../overview.md)
* [Sentiment analyse](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Entiteit herkenning](../how-tos/text-analytics-how-to-entity-linking.md)
* [Taal detecteren](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Taal herkenning](../how-tos/text-analytics-how-to-language-detection.md)
