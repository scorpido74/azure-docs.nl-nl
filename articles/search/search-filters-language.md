---
title: Taal filters voor inhoud in meerdere talen in een zoek index-Azure Search
description: Filter criteria voor het ondersteunen van zoeken in meerdere talen, het bereik van query's uitvoeren op taalspecifieke velden.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 10/23/2017
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 1eced868b180a916355d6f9fbfc8cd47a5d7d6e2
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69649871"
---
# <a name="how-to-filter-by-language-in-azure-search"></a>Filteren op taal in Azure Search 

Een belang rijke vereiste in een meertalige Zoek toepassing is de mogelijkheid om de resultaten in de eigen taal van de gebruiker te doorzoeken en op te halen. In Azure Search, een manier om te voldoen aan de taal vereisten van een meertalige app is het maken van een reeks velden die zijn toegewezen aan het opslaan van teken reeksen in een specifieke taal, en vervolgens de zoek opdracht voor volledige tekst beperken tot deze velden op de query tijd.

Query parameters op de aanvraag worden gebruikt om de zoek bewerking te bereiken en de resultaten te verkorten van alle velden die geen inhoud bieden die compatibel is met de zoek ervaring die u wilt leveren.

| Parameters | Doel |
|-----------|--------------|
| **searchFields** | Hiermee beperkt u het zoeken in volledige tekst in de lijst met benoemde velden. |
| **$select** | Hiermee verkleint u het antwoord op alleen de velden die u opgeeft. Standaard worden alle ophaalbaar velden geretourneerd. Met de para meter **$Select** kunt u kiezen welke items u wilt retour neren. |

Het succes van deze techniek scharniert op de integriteit van de veld inhoud. Azure Search converteert geen teken reeksen of voert geen taal detectie uit. U moet ervoor zorgen dat velden de door u verwachte teken reeksen bevatten.

## <a name="define-fields-for-content-in-different-languages"></a>Velden definiëren voor inhoud in verschillende talen

In Azure Search worden query's gericht op één index. Ontwikkel aars die taalspecifieke teken reeksen willen opgeven in één zoek opdracht, definiëren meestal specifieke velden voor het opslaan van de waarden: Eén veld voor Engelse teken reeksen, een voor Frans, enzovoort. 

In onze voor beelden, met inbegrip van het voor [beeld](search-get-started-portal.md) van het onroerend goed, hebt u mogelijk de veld definities gezien die vergelijkbaar zijn met de volgende scherm afbeelding. In dit voor beeld ziet u de taal analyse toewijzingen voor de velden in deze index. Velden met teken reeksen worden beter in Zoek opdrachten in volledige tekst uitgevoerd wanneer ze zijn gekoppeld aan een Analyzer die is ontworpen om de taal kundige regels van de doel taal te verwerken.

  ![](./media/search-filters-language/lang-fields.png)

> [!Note]
> Zie [een index definiëren (.net)](https://docs.microsoft.com/azure/search/search-create-index-dotnet) en [een index definiëren (rest)](search-create-index-rest-api.md)voor code voorbeelden van veld definities met taal analysen.

## <a name="build-and-load-an-index"></a>Een index maken en laden

Een tussenliggende (en mogelijk duidelijke) stap is dat u [de index moet bouwen en vullen](https://docs.microsoft.com/azure/search/search-create-index-dotnet) voordat u een query kunt formuleren. Deze stap wordt hier vermeld voor volledigheid. Een manier om te bepalen of de index beschikbaar is, is door de lijst indexen te controleren in de [Portal](https://portal.azure.com).

## <a name="constrain-the-query-and-trim-results"></a>De resultaten van de query en het knippen beperken

De para meters voor de query worden gebruikt om de zoek opdracht te beperken tot specifieke velden en vervolgens de resultaten van velden die niet nuttig zijn voor uw scenario, te verkorten. Gezien het doel van het beperken van een zoek opdracht naar velden met Franse teken reeksen, gebruikt u **searchFields** om de query te richten op velden met teken reeksen in die taal. 

Een zoek opdracht retourneert standaard alle velden die zijn gemarkeerd als ophalen. Daarom wilt u mogelijk velden uitsluiten die niet voldoen aan de taalspecifieke Zoek ervaring die u wilt bieden. Als u de zoek opdracht beperkt tot een veld met Franse teken reeksen, wilt u waarschijnlijk velden met Engelse teken reeksen uitsluiten van uw resultaten. Met behulp van de para meter **$Select** query kunt u bepalen welke velden worden geretourneerd naar de aanroepende toepassing.

```csharp
parameters =
    new SearchParameters()
    {
        searchFields = "description_fr" 
        Select = new[] { "description_fr"  }
    };
```
> [!Note]
> Hoewel er geen $filter argument is voor de query, wordt deze use-case sterk gekoppeld aan filter concepten, zodat we deze als een filter scenario presen teren.

## <a name="see-also"></a>Zie ook

+ [Filters in Azure Search](search-filters.md)
+ [Taalanalyse](https://docs.microsoft.com/rest/api/searchservice/language-support)
+ [De manier waarop zoeken in volledige tekst werkt in Azure Search](search-lucene-query-architecture.md)
+ [Zoeken naar documenten REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)

