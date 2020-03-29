---
title: Ingebouwde tekst- en beeldverwerking tijdens het indexeren
titleSuffix: Azure Cognitive Search
description: Gegevensextractie, natuurlijke taal, cognitieve vaardigheden voor beeldverwerking voegen semantiek en structuur toe aan ruwe inhoud in een Azure Cognitive Search-pijplijn.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 9e1f62dcdb122726fc1c08b7bea4e4c214ce7906
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76933371"
---
# <a name="built-in-cognitive-skills-for-text-and-image-processing-during-indexing-azure-cognitive-search"></a>Ingebouwde cognitieve vaardigheden voor tekst- en beeldverwerking tijdens indexering (Azure Cognitive Search)

In dit artikel leert u over de cognitieve vaardigheden die zijn geleverd met Azure Cognitive Search die u opnemen in een skillset om inhoud en structuur te extraheren. Een *cognitieve vaardigheid* is een module of bewerking die inhoud op een bepaalde manier transformeert. Vaak is het een component die gegevens extraheert of structuur afleidt, en daarom ons begrip van de invoergegevens vergroot. Bijna altijd is de uitvoer tekstgebaseerd. Een *skillset* is het verzamelen van vaardigheden die de verrijkingspijplijn definiëren. 

> [!NOTE]
> Terwijl u het bereik uitbreidt door de frequentie van de verwerking te verhogen, meer documenten toe te voegen of meer AI-algoritmen toe te voegen, moet u [een factureerbare bron voor cognitive services toevoegen.](cognitive-search-attach-cognitive-services.md) Er worden kosten in rekening gebracht bij het aanroepen van API's in Cognitive Services en voor het extraheren van afbeeldingen als onderdeel van de fase van het kraken van documenten in Azure Cognitive Search. Er zijn geen kosten voor tekstextractie uit documenten.
>
> Uitvoering van ingebouwde vaardigheden wordt in rekening gebracht tegen de bestaande [Cognitive Services pay-as-you-go prijs.](https://azure.microsoft.com/pricing/details/cognitive-services/) De prijzen voor imageextractie worden beschreven op de [prijspagina azure cognitive search.](https://go.microsoft.com/fwlink/?linkid=2042400)


## <a name="built-in-skills"></a>Ingebouwde vaardigheden

Verschillende vaardigheden zijn flexibel in wat ze consumeren of produceren. Over het algemeen zijn de meeste vaardigheden gebaseerd op vooraf getrainde modellen, wat betekent dat u het model niet trainen met uw eigen trainingsgegevens. In de volgende tabel worden de vaardigheden van Microsoft opgesomd en beschreven. 

| Behendigheid | Beschrijving |
|-------|-------------|
|[Microsoft.Skills.Text.CustomEntityLookupSkill](cognitive-search-skill-custom-entity-lookup.md)| Zoekt naar tekst uit een aangepaste, door de gebruiker gedefinieerde lijst met woorden en zinnen.|
| [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md) | Deze vaardigheid maakt gebruik van een vooraf opgeleid model om belangrijke zinnen te detecteren op basis van term plaatsing, taalkundige regels, nabijheid van andere termen, en hoe ongebruikelijk de term is binnen de brongegevens. |
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | Deze vaardigheid maakt gebruik van een vooraf opgeleid model om te detecteren welke taal wordt gebruikt (één taal-ID per document). Wanneer meerdere talen binnen dezelfde tekstsegmenten worden gebruikt, is de uitvoer de LCID van de overwegend gebruikte taal.|
| [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md) | Consolideert tekst uit een verzameling velden in één veld.  |
| [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) | Deze vaardigheid maakt gebruik van een vooraf opgeleid model om entiteiten voor een vaste set categorieën vast te stellen: personen, locatie, organisatie, e-mails, URL's, datetime-velden. |
| [Microsoft.Skills.Text.PIIDetectionSkill](cognitive-search-skill-pii-detection.md)  | Deze vaardigheid maakt gebruik van een voorgetraind model om persoonlijk identificeerbare informatie uit een bepaalde tekst te halen. De vaardigheid geeft ook verschillende opties voor het maskeren van de gedetecteerde PII-entiteiten in de tekst.  |
| [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)  | Deze vaardigheid maakt gebruik van een voorgetraind model om positief of negatief sentiment te scoren op een record op recordbasis. De score ligt tussen 0 en 1. Neutrale scores vinden plaats voor zowel de null-case wanneer sentiment niet kan worden gedetecteerd als voor tekst die als neutraal wordt beschouwd.  |
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | Hiermee splitst u tekst op in pagina's, zodat u inhoud stapsgewijs verrijken of vergroten. |
| [Microsoft.Skills.Text.TranslationSkill](cognitive-search-skill-text-translation.md) | Deze vaardigheid maakt gebruik van een vooraf opgeleid model om de invoertekst te vertalen naar verschillende talen voor normalisatie- of lokalisatievoorbeelden. |
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | Deze vaardigheid maakt gebruik van een beelddetectiealgoritme om de inhoud van een afbeelding te identificeren en een tekstbeschrijving te genereren. |
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | Optische tekenherkenning. |
| [Microsoft.Skills.Util.ConditionalSkill](cognitive-search-skill-conditional.md) | Hiermee u filteren, een standaardwaarde toewijzen en gegevens samenvoegen op basis van een voorwaarde.|
| [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md) | Haalt inhoud uit een bestand in de verrijkingspijplijn. |
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | Hiermee wordt de uitvoer toegewezen aan een complex type (een meerdelige gegevenstype, dat kan worden gebruikt voor een volledige naam, een multiregeladres of een combinatie van achternaam en een persoonlijke id.) |
| [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md) | Hiermee u uitbreidbaarheid van een AI-verrijkingspijplijn door een HTTP-aanroep in een aangepaste web-API te maken |


Zie [Een aangepaste interface](cognitive-search-custom-skill-interface.md) definiëren en [Voorbeeld: Een aangepaste vaardigheid maken voor](cognitive-search-create-custom-skill-example.md)het maken van een aangepaste vaardigheid voor het maken van een aangepaste [vaardigheid.](cognitive-search-custom-skill-web-api.md)

## <a name="see-also"></a>Zie ook

+ [Een vaardighedenset definiëren](cognitive-search-defining-skillset.md)
+ [Definitie van de interface aangepaste vaardigheden](cognitive-search-custom-skill-interface.md)
+ [Zelfstudie: Verrijkt indexeren met AI](cognitive-search-tutorial-blob.md)
