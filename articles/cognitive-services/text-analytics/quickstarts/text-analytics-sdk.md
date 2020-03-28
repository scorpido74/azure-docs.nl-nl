---
title: 'Snelstart: Text Analytics-clientbibliotheek v3 | Microsoft Documenten'
titleSuffix: Azure Cognitive Services
description: Gebruik deze snelstart om uw toepassingen te verbinden met de Text Analytics API van Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: cc3f48ec1113f954336cfae0bda2cba2499d9a1d
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80240132"
---
# <a name="quickstart-use-the-text-analytics-client-library"></a>Snelstart: de tekstanalyse-clientbibliotheek gebruiken

Ga aan de slag met de text analytics-clientbibliotheek. Volg deze stappen om het pakket te installeren en probeer de voorbeeldcode voor basistaken uit.

Gebruik de clientbibliotheek Text Analytics om uit te voeren:

* Sentimentanalyse
* Taaldetectie
* Herkenning van entiteiten
* Sleuteltermextractie

::: zone pivot="programming-language-csharp"

> [!IMPORTANT]
> * De nieuwste preview-versie van `3.0-preview`de Text Analytics API is , die een openbare preview bevat voor verbeterde [Sentiment Analysis](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) en [Named Entity Recognition (NER).](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) De nieuwste stabiele `2.1`versie is .
>    * Zorg ervoor dat u alleen de instructies voor de versie die u gebruikt opvolgt.
> * De code in dit artikel maakt gebruik van synchrone methoden en niet-beveiligde referenties opslag om redenen van eenvoud. Voor productiescenario's raden we aan om de gebatcheerde asynchrone methoden te gebruiken voor prestaties en schaalbaarheid. Zie de referentiedocumentatie hieronder.

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * Deze quickstart is `3.0-preview` alleen voor de versie van de text analytics-clientbibliotheek, die een openbare preview bevat voor verbeterde [sentimentanalyse](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) en [benoemde entiteitsherkenning (NER).](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)
> * De code in dit artikel maakt gebruik van synchrone methoden en niet-beveiligde referenties opslag om redenen van eenvoud. Voor productiescenario's raden we aan om de gebatcheerde asynchrone methoden te gebruiken voor prestaties en schaalbaarheid. Zie de referentiedocumentatie hieronder.

[!INCLUDE [Java quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

> [!IMPORTANT]
> * De nieuwste preview-versie van `3.0-preview`de Text Analytics API is , die een openbare preview bevat voor verbeterde [Sentiment Analysis](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) en [Named Entity Recognition (NER).](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) De nieuwste stabiele `2.1`versie is .
>    * Zorg ervoor dat u alleen de instructies voor de versie die u gebruikt opvolgt.
> * De code in dit artikel maakt gebruik van synchrone methoden en niet-beveiligde referenties opslag om redenen van eenvoud. Voor productiescenario's raden we aan om de gebatcheerde asynchrone methoden te gebruiken voor prestaties en schaalbaarheid. Zie de referentiedocumentatie hieronder.
> * U deze versie van de text analytics-clientbibliotheek ook [in uw browser](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md)uitvoeren.

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/nodejs-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

> [!IMPORTANT]
> * De nieuwste preview-versie van `3.0-preview`de Text Analytics API is , die een openbare preview bevat voor verbeterde [Sentiment Analysis](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) en [Named Entity Recognition (NER).](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) De nieuwste stabiele `2.1`versie is .
>    * Zorg ervoor dat u alleen de instructies voor de versie die u gebruikt opvolgt.
> * De code in dit artikel maakt gebruik van synchrone methoden en niet-beveiligde referenties opslag om redenen van eenvoud. Voor productiescenario's raden we aan om de gebatcheerde asynchrone methoden te gebruiken voor prestaties en schaalbaarheid. Zie de referentiedocumentatie hieronder. 

[!INCLUDE [Python quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-other"

## <a name="additional-language-support"></a>Aanvullende taalondersteuning

Als u op dit tabblad hebt geklikt, hebt u waarschijnlijk geen snelle start in uw favoriete programmeertaal gezien. Maak je geen zorgen, we hebben extra quickstarts beschikbaar. Gebruik de tabel om het juiste voorbeeld voor uw programmeertaal te vinden.

| Taal | Beschikbare versie | 
|----------|------------------------|
| Ruby     | [Versie 2.1](ruby-sdk.md) | 
| Aan de slag       | [Versie 2.1](go-sdk.md) | 

::: zone-end

## <a name="clean-up-resources"></a>Resources opschonen

Als u een abonnement voor Cognitive Services wilt opschonen en verwijderen, u de bron- of brongroep verwijderen. Als u de brongroep verwijdert, worden ook andere bronnen verwijderd die eraan zijn gekoppeld.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Ontdek een oplossing](../text-analytics-user-scenarios.md#analyze-recorded-inbound-customer-calls)

* [Overzicht van Text Analytics](../overview.md)
* [Sentimentanalyse](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Herkenning van entiteiten](../how-tos/text-analytics-how-to-entity-linking.md)
* [Taal detecteren](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Taalherkenning](../how-tos/text-analytics-how-to-language-detection.md)
