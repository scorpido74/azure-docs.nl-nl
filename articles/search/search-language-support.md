---
title: Indexering in meerdere talen voor niet-Engelse zoekopdrachten
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search ondersteunt 56 talen en maakt gebruik van taalanalysers van Lucene- en Natural Language Processing-technologie van Microsoft.
manager: nitinme
author: yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ca2bc66c755da2011cc7016f37b194caa6200d9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793593"
---
# <a name="how-to-create-an-index-for-multiple-languages-in-azure-cognitive-search"></a>Een index maken voor meerdere talen in Azure Cognitive Search

Indexen kunnen velden bevatten die inhoud uit meerdere talen bevatten, bijvoorbeeld het maken van afzonderlijke velden voor taalspecifieke tekenreeksen. Wijs voor de beste resultaten tijdens het indexeren en opvragen een taalanalyzer toe die de juiste taalregels biedt. 

Azure Cognitive Search biedt een grote selectie van taalanalysers van zowel Lucene als Microsoft die kunnen worden toegewezen aan afzonderlijke velden met behulp van de eigenschap Analyzer. U ook een taalanalyzer opgeven in de portal, zoals beschreven in dit artikel.

## <a name="add-analyzers-to-fields"></a>Analysers toevoegen aan velden

Een taalanalyzer wordt opgegeven wanneer een veld wordt gemaakt. Als u een analyzer toevoegt aan een bestaande velddefinitie, moet de index worden overschreven (en herladen) of een nieuw veld worden gemaakt dat identiek is aan het origineel, maar met een analyzer-toewijzing. U dan het ongebruikte veld op uw gemak verwijderen.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en zoek uw zoekservice.
1. Klik **op Index toevoegen** in de opdrachtbalk boven aan het servicedashboard om een nieuwe index te starten of open een bestaande index om een analyzer in te stellen op nieuwe velden die u aan een bestaande index toevoegt.
1. Start een velddefinitie door een naam op te geven.
1. Kies het gegevenstype Edm.String. Alleen tekenreeksvelden zijn doorzoekbaar voor volledige tekst.
1. Stel het kenmerk **Doorzoekbaar** in om de eigenschap Analyzer in te schakelen. Een veld moet tekstgebaseerd zijn om gebruik te kunnen maken van een taalanalyzer.
1. Kies een van de beschikbare analysers. 

![Taalanalysers toewijzen tijdens velddefinitie](media/search-language-support/select-analyzer.png "Taalanalysers toewijzen tijdens velddefinitie")

Standaard gebruiken alle doorzoekbare velden de [Standaard Lucene analyzer](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) die taalagnostisch is. Zie [Taalanalysers toevoegen aan een Azure Cognitive Search-index](index-add-language-analyzers.md)als u de volledige lijst met ondersteunde analysators wilt weergeven.

In het portaal zijn analysers bedoeld om te worden gebruikt zoals het is. Als u aanpassing of een specifieke configuratie van filters en tokenizers nodig hebt, moet u [een aangepaste analyzer](index-add-custom-analyzers.md) in code maken. De portal biedt geen ondersteuning voor het selecteren of configureren van aangepaste analysers.

## <a name="query-language-specific-fields"></a>Taalspecifieke velden voor query's

Zodra de taalanalyzer is geselecteerd voor een veld, wordt deze gebruikt bij elke indexerings- en zoekaanvraag voor dat veld. Wanneer een query wordt uitgegeven tegen meerdere velden met verschillende analysers, wordt de query onafhankelijk verwerkt door de toegewezen analysers voor elk veld.

Als de taal van de agent die een query uitgeeft bekend is, kan een zoekaanvraag worden gebruikt voor een specifiek veld met behulp van de queryparameter **searchFields.** De volgende query wordt alleen uitgegeven tegen de beschrijving in het Pools:

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=PolishContent&api-version=2019-05-06`

U uw index opvragen via de portal en [**zoekverkenner**](search-explorer.md) gebruiken om een query te plakken die vergelijkbaar is met de bovenstaande query.

## <a name="boost-language-specific-fields"></a>Taalspecifieke velden stimuleren

Soms is de taal van de agent die een query uitgeeft niet bekend, in welk geval de query tegelijkertijd tegen alle velden kan worden uitgegeven. Indien nodig kan de voorkeur voor resultaten in een bepaalde taal worden gedefinieerd met behulp van [scoreprofielen.](index-add-scoring-profiles.md) In het onderstaande voorbeeld worden wedstrijden in de beschrijving in het Engels hoger gescoord ten opzichte van wedstrijden in het Pools en Frans:

    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2019-05-06`

## <a name="next-steps"></a>Volgende stappen

Als u een .NET-ontwikkelaar bent, u taalanalysers configureren met de eigenschap [Azure Cognitive Search .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Search) en de eigenschap [Analyzer.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet) 