---
title: Ingebouwde extractie van gegevens, natuurlijke taal, verwerking van installatie kopieën-Azure Search
description: Gegevens extractie, natuurlijke taal, cognitieve vaardig heden voor afbeeldings verwerking Voeg semantiek en structuur toe aan onbewerkte inhoud in een Azure Search-pijp lijn.
manager: nitinme
author: luiscabrer
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: 8d367f7588feb892f667522a515ad4411bf96624
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70183435"
---
# <a name="predefined-skills-for-content-enrichment-azure-search"></a>Vooraf gedefinieerde vaardig heden voor inhouds verrijking (Azure Search)

In dit artikel vindt u meer informatie over de cognitieve vaardig heden die worden meegeleverd met Azure Search. Een *cognitieve vaardigheid* is een bewerking waarmee inhoud op een of andere manier wordt getransformeerd. Vaak is het een onderdeel dat gegevens ophaalt of een structuur verkrijgt, waardoor we onze informatie over de invoer gegevens verg Roten. Bijna altijd is de uitvoer op basis van tekst. Een *vakkennisset* is het verzamelen van vaardig heden die de verrijkings pijplijn definiëren. 

> [!NOTE]
> Als u het bereik uitbreidt door de verwerkings frequentie te verhogen, meer documenten toe te voegen of meer AI-algoritmen toe te voegen, moet u [een factureer bare Cognitive Services resource koppelen](cognitive-search-attach-cognitive-services.md). Er worden kosten in rekening gebracht bij het aanroepen van Api's in Cognitive Services en voor het ophalen van afbeeldingen als onderdeel van de fase voor het kraken van documenten in Azure Search. Er worden geen kosten in rekening gebracht voor het ophalen van tekst uit documenten.
>
> De uitvoering van ingebouwde vaardig heden wordt in rekening gebracht op basis van de bestaande [Cognitive Services betalen naar](https://azure.microsoft.com/pricing/details/cognitive-services/)gebruik-prijs. Prijzen voor Image extractie worden beschreven op de [pagina met Azure Search prijzen](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="predefined-skills"></a>Vooraf gedefinieerde vaardigheden

Verschillende vaardig heden zijn flexibel in wat ze gebruiken of produceren. Over het algemeen zijn de meeste vaardig heden gebaseerd op vooraf getrainde modellen, wat betekent dat u het model niet kunt trainen met uw eigen trainings gegevens. In de volgende tabel worden de vaardig heden van micro soft opgesomd en beschreven. 

| Eigen | Description |
|-------|-------------|
| [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md) | Deze vaardigheid maakt gebruik van een voortraind model voor het detecteren van belang rijke zinnen op basis van de term plaatsing, taal kundige regels, nabij de andere voor waarden en hoe ongebruikelijk de term binnen de bron gegevens valt. |
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | Deze vaardigheid maakt gebruik van een voortraind model om te detecteren welke taal wordt gebruikt (één taal-ID per document). Wanneer er meerdere talen worden gebruikt binnen dezelfde tekst segmenten, is de uitvoer de LCID van de taal die het meest in gebruik is.|
| [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md) | Consolideert tekst van een verzameling velden in één veld.  |
| [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) | Deze vaardigheid maakt gebruik van een voortraind model voor het instellen van entiteiten voor een vaste set categorieën: personen, locatie, organisatie, e-mails, Url's, datum/tijd-velden. |
| [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)  | Deze vaardigheid maakt gebruik van een pretraind model om positieve of negatieve sentiment te beoordelen op basis van een record. De Score ligt tussen 0 en 1. Er treden neutrale scores op voor de null-case wanneer sentiment niet kan worden gedetecteerd en voor tekst die als neutraal wordt beschouwd.  |
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | Splitst tekst op pagina's zodat u inhoud stapsgewijs kunt verrijken of uitbreiden. |
| [Micro soft. skills. Text. TranslationSkill](cognitive-search-skill-text-translation.md) | Deze vaardigheid maakt gebruik van een voortraind model voor het vertalen van de invoer tekst in een groot aantal talen voor het gebruik van normalisatie-of lokalisatie-use cases. |
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | Deze vaardigheid maakt gebruik van een algoritme voor detectie van de afbeelding om de inhoud van een afbeelding te identificeren en een beschrijving van een tekst te genereren. |
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | Optische teken herkenning. |
| [Micro soft. skills. util. ConditionalSkill](cognitive-search-skill-conditional.md) | Staat filteren toe, het toewijzen van een standaard waarde en het samen voegen van gegevens op basis van een voor waarde.|
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | Wijst uitvoer toe aan een complex type (een gegevens type met meerdere delen, dat kan worden gebruikt voor een volledige naam, een adres van meerdere regels of een combi natie van achternaam en persoonlijke id). |
| [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md) | Maakt uitbreid baarheid van cognitieve Zoek pijplijn mogelijk door een HTTP-aanroep naar een aangepaste web-API te maken |


Zie [een aangepaste interface definiëren](cognitive-search-custom-skill-interface.md) voor meer informatie over het maken van een [aangepaste vaardigheid](cognitive-search-custom-skill-web-api.md) [: Een aangepaste vaardigheid maken voor cognitieve Zoek](cognitive-search-create-custom-skill-example.md)opdrachten.

## <a name="see-also"></a>Zie ook

+ [Een vaardig heden definiëren](cognitive-search-defining-skillset.md)
+ [Definitie van aangepaste vaardigheden interface](cognitive-search-custom-skill-interface.md)
+ [Zelfstudie: Verrijkte indexering met cognitieve zoek functie](cognitive-search-tutorial-blob.md)
