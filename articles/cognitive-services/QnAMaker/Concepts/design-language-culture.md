---
title: Ontwerp voor taal - QnA Maker
description: De QnA Maker-bron en alle kennisbanken in die bron ondersteunen één taal. De enkele taal is nodig om de beste antwoordresultaten voor een query te geven.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 5cb1dcd35649debbafd2e234606ad4c9d6906ea6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76843418"
---
# <a name="design-knowledge-base-for-content-language"></a>Ontwerp kennisbank voor inhoudstaal

De QnA Maker-bron en alle kennisbases in die bron ondersteunen één taal. De enkele taal is nodig om de beste antwoordresultaten voor een query te geven.

## <a name="single-language-per-resource"></a>Eén taal per resource

QnA Maker overwegingen voor taalondersteuning:

* Een QnA Maker-service en al zijn kennisbanken ondersteunen slechts één taal.
* De taal wordt expliciet ingesteld wanneer de eerste kennisbasis van de service wordt gemaakt
* De taal wordt bepaald aan de hand van de bestanden en URL's die worden toegevoegd wanneer de kennisbank wordt gemaakt
* De taal kan niet worden gewijzigd voor andere kennisbanken in de dienst
* De taal wordt gebruikt door de Cognitive Search-service (ranker #1) en de QnA Maker-service (#2) om het beste antwoord op een query te genereren

## <a name="supporting-multiple-languages"></a>Ondersteuning voor meerdere talen

Als u een knowledge base-systeem moet ondersteunen, dat meerdere talen omvat, u een van de volgende methoden kiezen:

* Gebruik de [vertaaltekstservice](../../translator/translator-info-overview.md) om een vraag in één taal te vertalen voordat u de vraag naar uw kennisbank stuurt. Hierdoor u zich richten op de kwaliteit van één taal en de kwaliteit van de alternatieve vragen en antwoorden.
* Maak een QnA Maker-bron en een kennisbank in die bron voor elke taal. Hiermee u afzonderlijke alternatieve vragen beheren en tekst beantwoorden die voor elke taal genuanceerder is. Dit geeft u veel meer flexibiliteit, maar vereist een veel hogere onderhoudskosten wanneer de vragen of antwoorden veranderen in alle talen.

Lees [talen die worden ondersteund](../overview/language-support.md) voor QnA Maker.

### <a name="support-each-language-with-a-qna-maker-resource"></a>Elke taal ondersteunen met een QnA Maker-bron

* Een QnA Maker-bron maken voor elke taal
* Alleen bestanden en URL's toevoegen voor die taal
* Gebruik een naamgevingsconventie voor de resource om de taal te identificeren. Een voorbeeld `qna-maker-fr` is voor alle kennisbanken voor Franse documenten

## <a name="next-steps"></a>Volgende stappen

Leer [concepten](query-knowledge-base.md) over het opvragen van de kennisbank voor een antwoord.