---
title: Migratie handleiding voor v2 van de Text Analytics-API
titleSuffix: Azure Cognitive Services
description: Meer informatie over hoe u uw toepassingen kunt verplaatsen om versie 3 van de Text Analytics-API te gebruiken.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 06/25/2020
ms.author: aahi
ms.openlocfilehash: 12c09ad8e1db3914263fcc864c9c2d09069d63a6
ms.sourcegitcommit: fdaad48994bdb9e35cdd445c31b4bac0dd006294
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85412580"
---
# <a name="migrate-to-version-3x-of-the-text-analytics-api"></a>Migreren naar versie 3. x van de Text Analytics-API

[!INCLUDE [v3 region availability](includes/v3-region-availability.md)]

Als u versie 2,1 van de Text Analytics-API gebruikt, helpt dit artikel u bij het upgraden van uw toepassing tot het gebruik van versie 3. x. Versie 3,0 is algemeen beschikbaar en introduceert nieuwe functies zoals uitgebreide [entity Recognition (ner)](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) en [model versie beheer](concepts/model-versioning.md). Er is ook een preview-versie van v 3.1 (v 3.1-Preview. x) beschikbaar, waarmee u functies zoals [opinie Mining](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)kunt toevoegen. De modellen die in v2 worden gebruikt, ontvangen geen toekomstige updates. 

#### <a name="sentiment-analysis"></a>[Sentiment analyse](#tab/sentiment-analysis)

## <a name="feature-changes"></a>Functie wijzigingen 

Sentimentanalyse in versie 2,1 retourneert sentiment scores tussen 0 en 1 voor elk document dat naar de API wordt verzonden, met scores dichter bij 1 en dat meer positieve sentiment. In plaats daarvan retourneert versie 3 sentiment-labels (zoals ' positief ' of ' negatief ') voor zowel de zinnen als het document als geheel en de bijbehorende vertrouwens scores. 

## <a name="steps-to-migrate"></a>Te migreren stappen

### <a name="rest-api"></a>REST-API

Als uw toepassing het REST API gebruikt, werkt u het aanvraag eindpunt bij naar het v3-eind punt voor sentiment analyse. Bijvoorbeeld: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment` . U moet de toepassing ook bijwerken om de sentiment-labels te gebruiken die in het [JSON-antwoord](how-tos/text-analytics-how-to-sentiment-analysis.md#view-the-results)zijn geretourneerd. 

### <a name="client-libraries"></a>Clientbibliotheken

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

#### <a name="ner-and-entity-linking"></a>[NER en entiteits koppeling](#tab/named-entity-recognition)

## <a name="feature-changes"></a>Functie wijzigingen

> [!NOTE] 
> Op dit moment worden [v3-entiteits categorieën](named-entity-types.md) alleen geretourneerd op Engelse en Spaanse tekst. De API retourneert versie 2,1-resultaten voor aanvragen in andere talen, op voor waarde dat deze worden ondersteund in versie 2,1.

In versie 2,1 gebruikt de Text Analytics-API één eind punt voor benoemde entiteits herkenning (NER) en entiteits koppelingen. Versie 3 biedt uitgebreide detectie van benoemde entiteiten en maakt gebruik van afzonderlijke eind punten voor NER en aanvragen voor het koppelen van entiteiten. Vanaf v 3.1-Preview. 1 kan NER ook persoonlijke `pii` informatie en status gegevens detecteren `phi` . 

## <a name="steps-to-migrate"></a>Te migreren stappen

### <a name="rest-api"></a>REST-API

Als uw toepassing het REST API gebruikt, werkt u het aanvraag eindpunt bij naar de V3-eind punten voor NER en/of entiteits koppeling.

Entiteiten koppelen
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/linking`

NER
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`

U moet uw toepassing ook bijwerken om de [entiteits categorieën](named-entity-types.md) te gebruiken die in het [JSON-antwoord](how-tos/text-analytics-how-to-entity-linking.md#view-results)worden geretourneerd.

### <a name="client-libraries"></a>Clientbibliotheken

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]


#### <a name="language-detection"></a>[Taal detectie](#tab/language-detection)

## <a name="feature-changes"></a>Functie wijzigingen 

De functie voor taal detectie is niet gewijzigd in v3 buiten de eindpunt versie, maar het JSON-antwoord zal `ConfidenceScore` in plaats van worden opgenomen `score` . V3 retourneert ook slechts één taal in de uitvoer. 

## <a name="steps-to-migrate"></a>Te migreren stappen

### <a name="rest-api"></a>REST-API

Als uw toepassing het REST API gebruikt, werkt u het aanvraag eindpunt bij naar het v3-eind punt voor taal detectie. Bijvoorbeeld: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/languages` . U moet ook de toepassing bijwerken zodat deze wordt gebruikt `ConfidenceScore` in plaats van `score` in het [JSON-antwoord](how-tos/text-analytics-how-to-language-detection.md#step-3-view-the-results). 

### <a name="client-libraries"></a>Clientbibliotheken

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]


#### <a name="key-phrase-extraction"></a>[Extractie van sleutel woorden](#tab/key-phrase-extraction)

## <a name="feature-changes"></a>Functie wijzigingen 

De functie voor het uitpakken van sleutel woorden is niet gewijzigd in v3 buiten de eindpunt versie.

## <a name="steps-to-migrate"></a>Te migreren stappen

### <a name="rest-api"></a>REST-API

Als uw toepassing het REST API gebruikt, werkt u het aanvraag eindpunt bij naar het v3-eind punt voor het uitpakken van de sleutel woord groep. Bijvoorbeeld: `https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases`

### <a name="client-libraries"></a>Clientbibliotheken

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

---


## <a name="see-also"></a>Zie ook

* [Naslag informatie over Text Analytics-API v2](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/)
* [Wat is het Text Analytics-API](overview.md)
* [Taalondersteuning](language-support.md)
* [Versiebeheer model](concepts/model-versioning.md)