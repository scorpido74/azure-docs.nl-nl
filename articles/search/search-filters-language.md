---
title: Filteren op taal in een zoekindex
titleSuffix: Azure Cognitive Search
description: Filtercriteria om multi-language search te ondersteunen, query-uitvoering te scoping naar taalspecifieke velden.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 5dbf32610e54df4ff009d4cb0a0b080babb4ec73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112062"
---
# <a name="how-to-filter-by-language-in-azure-cognitive-search"></a>Filteren op taal in Azure Cognitive Search 

Een belangrijke vereiste in een meertalige zoektoepassing is de mogelijkheid om resultaten in de eigen taal van de gebruiker te zoeken en op te halen. In Azure Cognitive Search u onder andere voldoen aan de taalvereisten van een meertalige app door een reeks velden te maken die zijn gewijd aan het opslaan van tekenreeksen in een specifieke taal en vervolgens zoeken in volledige tekst te beperken tot alleen die velden tijdens de query.

Queryparameters op de aanvraag worden gebruikt om de zoekbewerking te bereiken en vervolgens de resultaten bij te snijden van velden die geen inhoud bieden die compatibel is met de zoekervaring die u wilt leveren.

| Parameters | Doel |
|-----------|--------------|
| **zoekvelden** | Hiermee beperkt u het zoeken naar volledige tekst tot de lijst met benoemde velden. |
| **$select** | Knipt het antwoord bij om alleen de velden op te nemen die u opgeeft. Standaard worden alle opvraagbare velden geretourneerd. Met **de parameter $select** u kiezen welke parameter u wilt retourneren. |

Het succes van deze techniek hangt af van de integriteit van de inhoud van het veld. Azure Cognitive Search vertaalt geen tekenreeksen of voert geen taaldetectie uit. Het is aan jou om ervoor te zorgen dat velden de tekenreeksen bevatten die u verwacht.

## <a name="define-fields-for-content-in-different-languages"></a>Velden definiëren voor inhoud in verschillende talen

In Azure Cognitive Search richten query's zich op één index. Ontwikkelaars die taalspecifieke tekenreeksen in één zoekervaring willen bieden, definiëren doorgaans specifieke velden om de waarden op te slaan: één veld voor Engelse tekenreeksen, één voor Frans, enzovoort. 

In onze voorbeelden, inclusief het [onderstaande vastgoedmonster,](search-get-started-portal.md) hebt u mogelijk velddefinities gezien die vergelijkbaar zijn met de volgende schermafbeelding. Merk op hoe in dit voorbeeld de toewijzingen van de taalanalysevoor de velden in deze index worden weergegeven. Velden die tekenreeksen bevatten, presteren beter in het zoeken naar volledige tekst wanneer ze zijn gekoppeld aan een analyzer die is ontworpen om de taalkundige regels van de doeltaal te verwerken.

  ![](./media/search-filters-language/lang-fields.png)

> [!Note]
> Zie [Een index definiëren (.NET)](https://docs.microsoft.com/azure/search/search-create-index-dotnet) en Een index definiëren [(REST)](search-create-index-rest-api.md)voor codevoorbeelden met velddefinities met talenanalysers.

## <a name="build-and-load-an-index"></a>Een index bouwen en laden

Een tussenliggende (en misschien voor de hand liggende) stap is dat u de index moet [opbouwen en invullen](https://docs.microsoft.com/azure/search/search-create-index-dotnet) voordat u een query formuleert. We vermelden deze stap hier voor volledigheid. Een manier om te bepalen of de index beschikbaar is, is door de indexenlijst in het [portaal](https://portal.azure.com)te controleren .

## <a name="constrain-the-query-and-trim-results"></a>De query- en bijsnijdresultaten beperken

Parameters in de query worden gebruikt om zoeken te beperken tot specifieke velden en vervolgens de resultaten van velden bij te snijden die niet nuttig zijn voor uw scenario. Als u het doel hebt zoeken te beperken tot velden met Franse tekenreeksen, gebruikt u **zoekvelden** om de query te targeten op velden met tekenreeksen in die taal. 

Standaard retourneert een zoekopdracht alle velden die zijn gemarkeerd als opvraagbaar. Als zodanig u velden uitsluiten die niet voldoen aan de taalspecifieke zoekervaring die u wilt bieden. Als u specifiek zoeken beperkt tot een veld met Franse tekenreeksen, wilt u waarschijnlijk velden met Engelse tekenreeksen uitsluiten van uw resultaten. Met de **parameter $select** query u bepalen welke velden worden geretourneerd naar de aanroepende toepassing.

```csharp
parameters =
    new SearchParameters()
    {
        searchFields = "description_fr" 
        Select = new[] { "description_fr"  }
    };
```
> [!Note]
> Hoewel er geen $filter argument op de query is, is deze use case sterk verbonden met filterconcepten, dus presenteren we deze als een filterscenario.

## <a name="see-also"></a>Zie ook

+ [Filters in Azure Cognitive Search](search-filters.md)
+ [Taalanalyse](https://docs.microsoft.com/rest/api/searchservice/language-support)
+ [Hoe zoeken in de volledige tekst werkt in Azure Cognitive Search](search-lucene-query-architecture.md)
+ [REST API voor documenten zoeken](https://docs.microsoft.com/rest/api/searchservice/search-documents)

