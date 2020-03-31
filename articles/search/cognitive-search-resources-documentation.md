---
title: Documentatiekoppelingen voor AI-verrijking
titleSuffix: Azure Cognitive Search
description: Een geannoteerde lijst met artikelen, zelfstudies, voorbeelden en blogberichten met betrekking tot AI-verrijkingsworkloads in Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: d2b25fb93a1e35ffa82cf49c60d181b841b1692d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616194"
---
# <a name="documentation-resources-for-ai-enrichment-in-azure-cognitive-search"></a>Documentatiebronnen voor AI-verrijking in Azure Cognitive Search

AI-verrijking is een mogelijkheid voor Azure Cognitive Search-indexering die latente informatie in niet-tekstbronnen en ongedifferentieerde tekst vindt en deze omzet in doorzoekbare inhoud met volledige tekst in Azure Cognitive Search.

De volgende artikelen zijn de volledige documentatie voor AI verrijking.

## <a name="getting-started"></a>Aan de slag
+ [Inleiding tot AI in Azure Cognitive Search](cognitive-search-concept-intro.md)
+ [Snelstart: een cognitieve skillset maken in de Azure-portal](cognitive-search-quickstart-blob.md)
+ [Zelfstudie: Verrijkt indexeren met AI](cognitive-search-tutorial-blob.md)
+ [Voorbeeld: Een aangepaste vaardigheid maken voor AI-verrijking](cognitive-search-create-custom-skill-example.md)

## <a name="how-to-guidance"></a>How-to begeleiding
+ [Een vaardighedenset definiëren](cognitive-search-defining-skillset.md)
+ [Verwijzen naar aantekeningen in een vaardighedenset](cognitive-search-concept-annotations-syntax.md)
+ [Velden toewijzen aan een index](cognitive-search-output-field-mapping.md)
+ [Informatie uit afbeeldingen verwerken en extraheren](cognitive-search-concept-image-scenarios.md)
+ [Een Azure Cognitive Search-index opnieuw opbouwen](search-howto-reindex.md)
+ [Een interface voor aangepaste vaardigheden definiëren](cognitive-search-custom-skill-interface.md)
+ [Tips voor probleemoplossing](cognitive-search-concept-troubleshooting.md)

## <a name="reference"></a>Naslaginformatie

+ [Ingebouwde vaardigheden](cognitive-search-predefined-skills.md)
  + [Microsoft.Skills.text.KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)
  + [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.Skills.Text.PIIDetectionSkill](cognitive-search-skill-pii-detection.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft.Skills.Text.TranslationSkill](cognitive-search-skill-text-translation.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft.Skills.Util.ConditionalSkill](cognitive-search-skill-conditional.md)
  + [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ Aangepaste vaardigheden
  + [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md)

+ [Afgeschafte vaardigheden](cognitive-search-skill-deprecated.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)

+ [REST-API](https://docs.microsoft.com/rest/api/searchservice/)
  + [Skillset maken (api-versie=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
  + [Indexeren maken (api-versie=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>Zie ook

+ [Azure Cognitive Search REST API](https://docs.microsoft.com/rest/api/searchservice/)
+ [Indexeerfuncties in Azure Cognitive Search](search-indexer-overview.md)
+ [Wat is Azure Cognitive Search?](search-what-is-azure-search.md)
