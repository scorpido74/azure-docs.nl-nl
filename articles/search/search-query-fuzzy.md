---
title: Fuzzy zoekopdracht
titleSuffix: Azure Cognitive Search
description: Implementeer een zoekervaring 'bedoelde' om een verkeerd gespelde term of typfout automatisch te corrigeren.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 32ad34bcfb42bf8fc45ba7fdb7fba5e797ee6106
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262431"
---
# <a name="fuzzy-search-to-correct-misspellings-and-typos"></a>Fuzzy zoeken om spelfouten en typefouten te corrigeren

Azure Cognitive Search ondersteunt fuzzy search, een type query dat type query's compenseert die typefouten en verkeerd gespelde termen in de invoertekenreeks compenseert. Het doet dit door te scannen op termen met een vergelijkbare samenstelling. Het uitbreiden van zoeken naar near-matches heeft het effect van het automatisch corrigeren van een typfout wanneer de discrepantie slechts een paar misplaatste tekens is. 

## <a name="what-is-fuzzy-search"></a>Wat is fuzzy search?

Het is een expansie-oefening die een match produceert op termen met een vergelijkbare samenstelling. Wanneer een fuzzy search is opgegeven, bouwt de engine een grafiek (gebaseerd op [deterministische eindige automatontheorie)](https://en.wikipedia.org/wiki/Deterministic_finite_automaton)van vergelijkbaar samengestelde termen, voor alle hele termen in de query. Als uw query bijvoorbeeld drie termen 'universiteit van Washington' bevat, wordt `search=university~ of~ washington~` voor elke term in de query een grafiek gemaakt (er is geen stopwoordverwijdering in fuzzy search, dus 'van' krijgt een grafiek).

De grafiek bestaat uit maximaal 50 uitbreidingen, of permutaties, van elke term, het vastleggen van zowel de juiste als onjuiste varianten in het proces. De engine retourneert vervolgens de meest relevante wedstrijden in het antwoord. 

Voor een term als "universiteit", de grafiek zou kunnen hebben "unversty, universty, universiteit, universum, omgekeerd". Alle documenten die overeenkomen met die in de grafiek zijn opgenomen in de resultaten. In tegenstelling tot andere query's die de tekst analyseren om verschillende vormen van hetzelfde woord te verwerken ("muizen" en "muis"), worden de vergelijkingen in een vage query tegen nominale waarde genomen zonder enige taalkundige analyse van de tekst. "Universe" en "inverse", die semantisch verschillend zijn, zullen overeenkomen omdat de syntactische discrepanties klein zijn.

Een overeenkomst slaagt als de verschillen beperkt zijn tot twee of minder bewerkingen, waarbij een bewerking een ingevoegd, verwijderd, vervangen of getransponeerd teken is. Het tekenreekscorrectiealgoritme dat het differentieel aangeeft, is de [afstandsstatistiek Damerau-Levenshtein,](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance) beschreven als het "minimumaantal bewerkingen (invoegingen, verwijderingen, vervangingen of omzettingen van twee aangrenzende tekens) die nodig zijn om het ene woord in het andere te veranderen". 

In Azure Cognitive Search:

+ Fuzzy query is van toepassing op hele termen, maar u zinnen ondersteunen via EN constructies. Bijvoorbeeld, "Unviersty ~ van ~ "Wshington~" zou overeenkomen op "Universiteit van Washington".

+ De standaardafstand van een bewerking is 2. Een waarde `~0` van betekent geen uitbreiding (alleen de exacte term `~1` wordt beschouwd als een overeenkomst), maar u opgeven voor een mate van verschil, of een bewerking. 

+ Een vage query kan een term tot 50 extra permutaties uitbreiden. Deze limiet is niet configureerbaar, maar u het aantal uitbreidingen effectief verminderen door de bewerkingsafstand te verkleinen tot 1.

+ De antwoorden bestaan uit documenten met een relevante overeenkomst (tot 50).

Gezamenlijk worden de grafieken ingediend als wedstrijdcriteria ten opzichte van tokens in de index. Zoals u zich voorstellen, fuzzy zoeken is inherent langzamer dan andere query formulieren. De grootte en complexiteit van uw index kan bepalen of de voordelen voldoende zijn om de latentie van de respons te compenseren.

> [!NOTE]
> Omdat fuzzy search meestal traag is, is het misschien de moeite waard om alternatieven zoals n-gram indexering te onderzoeken, met de progressie van korte tekensequenties (twee en drie tekensequenties voor bigram- en trigramtokens). Afhankelijk van uw taal- en queryoppervlak kan n-gram u betere prestaties bieden. De afweging is dat n-gram indexering is zeer opslag-intensief en genereert veel grotere indexen.
>
> Een ander alternatief, dat je zou kunnen overwegen als je wilt alleen de meest flagrante gevallen te behandelen, zou een [synoniem kaart](search-synonyms.md). Bijvoorbeeld het toewijzen van "zoeken" naar "serach, serch, sarch" of "retrieve" naar "retreive".

## <a name="indexing-for-fuzzy-search"></a>Indexeren voor vaag zoeken

Analysers worden niet gebruikt tijdens queryverwerking om een uitbreidingsgrafiek te maken, maar dat betekent niet dat analysators moeten worden genegeerd in vage zoekscenario's. Immers, analysers worden gebruikt tijdens het indexeren om tokens te maken waartegen matching wordt gedaan, of de query nu een vrije vorm, gefilterd zoeken of een vage zoekopdracht is met een grafiek als invoer. 

Over het algemeen is bij het toewijzen van analysers per veld de beslissing om de analyseketen te verfijnen gebaseerd op de primaire use case (een filter of full text search) in plaats van gespecialiseerde queryformulieren zoals fuzzy search. Om deze reden is er geen specifieke analyzer aanbeveling voor fuzzy zoeken. 

Als testquery's echter niet de overeenkomsten produceren die u verwacht, u proberen de indexeringsanalyzer te variëren en deze in te stellen op een [taalanalyzer](index-add-language-analyzers.md)om te zien of u betere resultaten krijgt. Sommige talen, met name talen met klinkermutaties, kunnen profiteren van de verbuiging en onregelmatige woordvormen die worden gegenereerd door de natuurlijke taalverwerkers van Microsoft. In sommige gevallen kan het gebruik van de juiste taalanalyzer een verschil maken in de vraag of een term wordt tokenized op een manier die compatibel is met de waarde die door de gebruiker wordt geleverd.

## <a name="how-to-use-fuzzy-search"></a>Fuzzy Search gebruiken

Vage query's worden geconstrueerd met behulp van de volledige syntaxis van Lucene query, waarbij een beroep wordt gedaan op de [Lucene queryparser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

1. Stel de volledige Lucene parser`queryType=full`in op de query ( ).

1. Eventueel u de aanvraag naar specifieke`searchFields=<field1,field2>`velden toepassingsgebieden met behulp van deze parameter ( ). 

1. De betegelde`~`operator aan het einde van`search=<string>~`de gehele termijn toevoegen ( ).

   Neem een optionele parameter op, een getal tussen 0 en 2 (standaard), als u de bewerkingsafstand wilt opgeven (`~1`). Bijvoorbeeld, "blue~" of "blue~1" zou terugkeren "blauw", "blues", en "lijm".

In Azure Cognitive Search zijn er naast de term en afstand (maximaal 2) geen extra parameters in te stellen voor de query.

> [!NOTE]
> Tijdens queryverwerking ondergaan vage query's geen [lexicale analyse.](search-lucene-query-architecture.md#stage-2-lexical-analysis) De queryinvoer wordt rechtstreeks aan de querystructuur toegevoegd en uitgevouwen om een grafiek met termen te maken. De enige transformatie uitgevoerd is lagere behuizing.

## <a name="testing-fuzzy-search"></a>Fuzzy zoeken testen

Voor eenvoudige tests raden we [Zoekverkenner](search-explorer.md) of [Postman](search-get-started-postman.md) aan om een queryexpressie te herhalen. Beide tools zijn interactief, wat betekent dat u snel meerdere varianten van een term doorlopen en de reacties evalueren die terugkomen.

Wanneer de resultaten dubbelzinnig zijn, kan [hithighlighting](search-pagination-page-layout.md#hit-highlighting) u helpen de overeenkomst in het antwoord te identificeren. 

> [!Note]
> Het gebruik van hit highlighting om fuzzy matches te identificeren heeft beperkingen en werkt alleen voor basic fuzzy search. Als uw index scoreprofielen heeft of als u de query met extra syntaxis laageert, kan het zijn dat hitmarkeringen de overeenkomst niet identificeren. 

### <a name="example-1-fuzzy-search-with-the-exact-term"></a>Voorbeeld 1: vaag zoeken met de exacte term

Ga ervan uit dat `"Description"` de volgende tekenreeks in een veld in een zoekdocument bestaat:`"Test queries with special characters, plus strings for MSFT, SQL and Java."`

Begin met een vage zoekopdracht op 'speciaal' en voeg hithighlighting toe aan het veld Beschrijving:

    search=special~&highlight=Description

In het antwoord, omdat u hithighlighting hebt toegevoegd, wordt opmaak toegepast op 'speciaal' als overeenkomende term.

    "@search.highlights": {
        "Description": [
            "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
        ]

Probeer het verzoek opnieuw, spelfouten "speciaal" door het nemen van verschillende letters ("pe"):

    search=scial~&highlight=Description

Tot nu toe, geen verandering in het antwoord. Met behulp van de standaard van 2 graden afstand, het verwijderen van twee tekens "pe" van "speciale" nog steeds zorgt voor een succesvolle wedstrijd op die term.

    "@search.highlights": {
        "Description": [
            "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
        ]

Probeer nog een verzoek, verder wijzigen van de zoekterm door het nemen van een laatste teken voor een totaal van drie schrappingen (van "speciale" naar "scal"):

    search=scal~&highlight=Description

Merk op dat dezelfde reactie wordt geretourneerd, maar nu in plaats van matching op "special", de fuzzy match is op "SQL".

            "@search.score": 0.4232868,
            "@search.highlights": {
                "Description": [
                    "Mix of special characters, plus strings for MSFT, <em>SQL</em>, 2019, Linux, Java."
                ]

Het punt van dit uitgebreide voorbeeld is om de helderheid te illustreren die het markeren van de treffers tot dubbelzinnige resultaten kan brengen. In alle gevallen wordt hetzelfde document geretourneerd. Als u had vertrouwd op document-id's om een overeenkomst te verifiëren, had u misschien de verschuiving gemist van "speciaal" naar "SQL".

## <a name="see-also"></a>Zie ook

+ [Hoe zoeken in volledige tekst werkt in Azure Cognitive Search (queryparsing-architectuur)](search-lucene-query-architecture.md)
+ [Zoekverkenner](search-explorer.md)
+ [Hoe query's in .NET](search-query-dotnet.md)
+ [Vragen in REST](search-create-index-rest-api.md)
