---
title: Decoratiemarkeringen gebruiken om tekst te markeren - Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van tekstdecoraties en het markeren van markeringen in uw zoekresultaten met behulp van de Bing Web Search API.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 5365B568-EA55-4D97-8FBE-0AF60158D4D5
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: scottwhi
ms.openlocfilehash: a6d394fec6e7cf0a230f61ad05c236a1f84dad9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68854019"
---
# <a name="using-decoration-markers-to-highlight-text"></a>Decoratiemarkeringen gebruiken om tekst te markeren

Bing ondersteunt hithighlighting, die querytermen (of andere termen die Bing relevant vindt) markeert in de weergavetekenreeksen van sommige antwoorden. De `name`velden `displayUrl`en `snippet` velden van een webpagina kunnen bijvoorbeeld gemarkeerde querytermen bevatten. 

Bing bevat standaard geen markeringsmarkeringen in weergavetekenreeksen. Als u de markeringen `textDecorations` wilt inschakelen, neemt u `true`de queryparameter op in uw aanvraag en stelt u deze in op .

## <a name="hit-highlighting-example"></a>Voorbeeld van het markeren van treffers

In het volgende voorbeeld `Sailing Dinghy`wordt een webresultaat weergegeven voor . Bing heeft het begin en het einde van de queryterm gemarkeerd met de E000- en E001 Unicode-tekens.
  
![Hit Highlighting](./media/cognitive-services-bing-web-api/bing-hit-highlighting.png) 

Voordat u het resultaat weergeeft in uw gebruikersinterface, vervangt u de Unicode-tekens door de tekens die geschikt zijn voor uw weergave-indeling.

## <a name="marker-formatting"></a>Markeringsopmaak

Bing biedt de mogelijkheid om Unicode-tekens of HTML-tags als markeringen te gebruiken. Als u wilt opgeven welke markeringen u wilt gebruiken, neemt u de parameter [textFormat](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#textformat) query op: 

| Waarde             | Markering                       |
|-------------------|------------------------------|
| `textFormat=Raw`  | Unicode-tekens (standaard) |
| `textFormat=HTML` | HTML-tekens              |

## <a name="additional-text-decorations"></a>Extra tekstdecoraties

Bing kan verschillende tekstdecoraties retourneren. Een `Computation` antwoord kan bijvoorbeeld subscriptmarkeertekens bevatten `log(2)` voor `expression` de queryterm in het veld.

![berekeningsmarkeringen](./media/cognitive-services-bing-web-api/bing-markers-computation.png) 

Als het verzoek geen decoraties `expression` heeft `log10(2)`opgegeven, zou het veld . 

Als `textDecorations` `true`dat het wel is, kan Bing de volgende markeringen opnemen in de weergavetekenreeksen van antwoorden. Als er geen gelijkwaardige HTML-tag is, is de tabelcel leeg.

|Unicode|HTML|Beschrijving
|-|-|-
|U+E000|\<b>|Hiermee markeert u het begin van de queryterm (hithighlighting)
|U+E001|\</b>|Hiermee markeert u het einde van de queryterm
|U+E002|\<ik>|Markeert het begin van cursieve inhoud 
|U+E003|\</i>|Markeert het einde van cursieve inhoud
|U+E004|\<br/>|Markeert een regeleinde
|U+E005||Markeert het begin van een telefoonnummer
|U+E006||Markeert het einde van een telefoonnummer
|U+E007||Markeert het begin van een adres
|U+E008||Markeert het einde van een adres
|U+E009|\&nbsp;|Markeert een niet-brekende ruimte
|U+E00C|\<sterke>|Markeert het begin van vetgedrukte inhoud
|U+E00D|\</sterke>|Markeert het einde van vetgedrukte inhoud
|U+E00E||Hiermee markeert u het begin van de inhoud waarvan de achtergrond lichter moet zijn dan de omringende achtergrond
|U+E00F||Hiermee wordt het einde van de inhoud markeert waarvan de achtergrond lichter moet zijn dan de omringende achtergrond
|U+E010||Hiermee markeert u het begin van inhoud waarvan de achtergrond donkerder moet zijn dan de omringende achtergrond
|U+E011||Hiermee markeert u het einde van de inhoud waarvan de achtergrond donkerder moet zijn dan de omringende achtergrond
|U+E012|\<del>|Markeert het begin van de inhoud die moet worden doorgeslagen
|U+E013|\</del>|Markeert het einde van de inhoud die moet worden doorgeslagen
|U+E016|\<sub>|Markeert het begin van subscriptinhoud
|U+E017|\</sub>|Markeert het einde van subscriptinhoud
|U+E018|\<sup>|Markeert het begin van superscript-inhoud
|U+E019|\</sup>|Markeert het einde van superscript-inhoud

## <a name="next-steps"></a>Volgende stappen

* [Wat is de Bing Webzoekopdrachten-API?](overview.md) 
* [Het formaat van miniaturen wijzigen en miniaturen bijsnijden](resize-and-crop-thumbnails.md)