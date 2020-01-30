---
title: Ontwerpen voor taal-QnA Maker
description: De QnA Maker resource en alle kennis grondslagen in die resource ondersteunen één taal. De ene taal is nodig om de beste antwoord resultaten te bieden voor een query.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 5cb1dcd35649debbafd2e234606ad4c9d6906ea6
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843418"
---
# <a name="design-knowledge-base-for-content-language"></a>De Knowledge Base ontwerpen voor de inhouds taal

De QnA Maker resource en alle kennis grondslagen in die resource ondersteunen één taal. De ene taal is nodig om de beste antwoord resultaten te bieden voor een query.

## <a name="single-language-per-resource"></a>Eén taal per resource

QnA Maker overwegingen voor taal ondersteuning:

* Een QnA Maker-service en alle kennis slagen ondersteunen slechts één taal.
* De taal wordt expliciet ingesteld wanneer de eerste kennis basis van de service wordt gemaakt
* De taal wordt bepaald op basis van de bestanden en Url's die worden toegevoegd wanneer de Knowledge Base wordt gemaakt.
* De taal kan niet worden gewijzigd voor andere kennis grondslagen in de service
* De taal wordt door de Cognitive Search-service (Rank #1) en de QnA Maker-service (Rank #2) gebruikt voor het genereren van het beste antwoord op een query

## <a name="supporting-multiple-languages"></a>Ondersteuning voor meerdere talen

Als u een Knowledge Base-systeem moet ondersteunen dat verschillende talen bevat, kunt u een van de volgende methoden kiezen:

* Gebruik de [Vertaal tekst service](../../translator/translator-info-overview.md) om een vraag in één taal te vertalen voordat u de vraag naar uw kennis database verzendt. Zo kunt u zich richten op de kwaliteit van één taal en de kwaliteit van de alternatieve vragen en antwoorden.
* Maak een QnA Maker resource en een kennis database in die resource voor elke taal. Hierdoor kunt u afzonderlijke alternatieve vragen en antwoord tekst beheren die voor elke taal meer worden genuanced. Dit biedt veel meer flexibiliteit, maar vereist veel hogere onderhouds kosten wanneer de vragen of antwoorden in alle talen worden gewijzigd.

Controleer [talen](../overview/language-support.md) die worden ondersteund voor QnA Maker.

### <a name="support-each-language-with-a-qna-maker-resource"></a>Ondersteuning voor elke taal met een QnA Maker bron

* Een QnA Maker resource maken voor elke taal
* Alleen bestanden en Url's voor die taal toevoegen
* Gebruik een naamgevings Conventie voor de resource om de taal te identificeren. Een voor beeld is `qna-maker-fr` voor alle kennis grondslagen voor Franse documenten

## <a name="next-steps"></a>Volgende stappen

Meer [informatie over het](query-knowledge-base.md) uitvoeren van een query op de Knowledge Base voor een antwoord.