---
title: Taal ondersteuning-QnA Maker
titleSuffix: Azure Cognitive Services
description: Een lijst met cultuur, natuurlijke talen die door QnA Maker worden ondersteund voor uw Knowledge Base. Maak geen talen in dezelfde Knowledge Base.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 09/24/2019
ms.openlocfilehash: c990b6980dea871679b0b301e293e4fb94748db7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89650894"
---
# <a name="language-support-for-a-qna-maker-resource-and-knowledge-bases"></a>Taal ondersteuning voor een QnA Maker resource en kennis bases

De taal voor de service wordt geselecteerd wanneer u de eerste Knowledge Base maakt in de resource. Alle aanvullende kennis grondslagen in de resource moeten zich in dezelfde taal bekomen. 

De taal bepaalt de relevantie van de resultaten QnA Maker worden uitgevoerd als reactie op gebruikers query's. De QnA Maker resource en alle kennis grondslagen in die resource ondersteunen één taal. De ene taal is nodig om de beste antwoord resultaten te bieden voor een query.

## <a name="single-language-per-resource"></a>Eén taal per resource

Overweeg de volgende:

* Een QnA Maker-service en alle kennis slagen ondersteunen slechts één taal.
* De taal wordt expliciet ingesteld wanneer de eerste kennis basis van de service wordt gemaakt
* De taal wordt bepaald op basis van de bestanden en Url's die worden toegevoegd wanneer de Knowledge Base wordt gemaakt.
* De taal kan niet worden gewijzigd voor andere kennis grondslagen in de service
* De taal wordt door de Cognitive Search-service (Rank #1) en de QnA Maker-service (Rank #2) gebruikt voor het genereren van het beste antwoord op een query

## <a name="supporting-multiple-languages"></a>Ondersteuning voor meerdere talen

Als u een Knowledge Base-systeem moet ondersteunen dat verschillende talen bevat, kunt u het volgende doen:

* Gebruik de [Translator-service](../../translator/translator-info-overview.md) om een vraag in één taal te vertalen voordat u de vraag naar uw kennis database verzendt. Zo kunt u zich richten op de kwaliteit van één taal en de kwaliteit van de alternatieve vragen en antwoorden.
* Maak een QnA Maker resource en een kennis database in die resource voor elke taal. Hierdoor kunt u afzonderlijke alternatieve vragen en antwoord tekst beheren die voor elke taal meer worden genuanced. Dit biedt veel meer flexibiliteit, maar vereist veel hogere onderhouds kosten wanneer de vragen of antwoorden in alle talen worden gewijzigd.

Controleer [talen](../overview/language-support.md) die worden ondersteund voor QnA Maker.

### <a name="support-each-language-with-a-qna-maker-resource"></a>Ondersteuning voor elke taal met een QnA Maker bron

* Een QnA Maker resource maken voor elke taal
* Alleen bestanden en Url's voor die taal toevoegen
* Gebruik een naamgevings Conventie voor de resource om de taal te identificeren. Een voor beeld is `qna-maker-fr` voor alle kennis grondslagen voor Franse documenten


## <a name="languages-supported"></a>Ondersteunde talen

De volgende lijst bevat de talen die worden ondersteund voor een QnA Maker bron. 

|Taal|
|--|
|Arabisch|
|Armeens|
|Bengaals|
|Baskisch|
|Bulgaars|
|Catalaans|
|Chinese_Simplified|
|Chinese_Traditional|
|Kroatisch|
|Tsjechisch|
|Deens|
|Nederlands|
|Engels|
|Ests|
|Fins|
|Frans|
|Galicisch|
|Duits|
|Grieks|
|Gujarati|
|Hebreeuws|
|Hindi|
|Hongaars|
|IJslands|
|Indonesisch|
|Iers|
|Italiaans|
|Japans|
|Kannada|
|Koreaans|
|Lets|
|Litouws|
|Malayalam|
|Maleisisch|
|Noors|
|Pools|
|Portugees|
|Punjabi|
|Roemeens|
|Russisch|
|Serbian_Cyrillic|
|Serbian_Latin|
|Slowaaks|
|Sloveens|
|Spaans|
|Zweeds|
|Tamil|
|Telugu|
|Thai|
|Turks|
|Oekraïens|
|Urdu|
|Vietnamees|

## <a name="query-matching-and-relevance"></a>Overeenkomende treffers en relevantie van query's
QnA Maker is afhankelijk van de [taal analyse functies van Azure Cognitive Search](https://docs.microsoft.com/rest/api/searchservice/language-support) voor het leveren van resultaten.

Hoewel de mogelijkheden van Azure Cognitive Search op pari zijn voor ondersteunde talen, heeft QnA Maker een extra rangorde die zich boven de Azure Search-resultaten bevindt. In dit classificatie model gebruiken we enkele speciale semantische en op woord gebaseerde functies in de volgende talen.

|Talen met extra rangorde|
|--|
|Chinees|
|Tsjechisch|
|Nederlands|
|Engels|
|Frans|
|Duits|
|Hongaars|
|Italiaans|
|Japans|
|Koreaans|
|Pools|
|Portugees|
|Spaans|
|Zweeds|

Deze extra classificatie is een interne werking van de QnA Makerer.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Taal selecteren](../how-to/language-knowledge-base.md)
