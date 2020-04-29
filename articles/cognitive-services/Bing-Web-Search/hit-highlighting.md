---
title: Decoratie markeringen gebruiken om tekst te markeren Bing Webzoekopdrachten-API
titleSuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van tekst decoratie en het markeren van markeringen in uw zoek resultaten met behulp van de Bing Webzoekopdrachten-API.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "68854019"
---
# <a name="using-decoration-markers-to-highlight-text"></a>Decoratie markeringen gebruiken om tekst te markeren

Bing ondersteunt treffer markeringen, waarmee query termen (of andere termen die Bing relevant vindt) worden gemarkeerd in de weer gave-teken reeksen van een aantal antwoorden. Een voor beeld van `name`een webpagina resultaat, `displayUrl`en `snippet` velden kunnen gemarkeerde query termen bevatten. 

Standaard bevat Bing geen markerings markeringen in weergave teken reeksen. Als u de markeringen wilt inschakelen, neemt `textDecorations` u de query parameter op in uw aanvraag en `true`stelt u deze in op.

## <a name="hit-highlighting-example"></a>Voor beeld van een markeren van treffers

In het volgende voor beeld wordt een webresultaat weer gegeven voor `Sailing Dinghy`. Bing heeft het begin en het einde van de query term gemarkeerd met de Unicode-tekens E000 en E001.
  
![Treffers markeren](./media/cognitive-services-bing-web-api/bing-hit-highlighting.png) 

Voordat het resultaat in uw gebruikers interface wordt weer gegeven, vervangt u de Unicode-tekens door de letters die geschikt zijn voor uw weer gave-indeling.

## <a name="marker-formatting"></a>Markerings opmaak

Bing biedt de mogelijkheid om Unicode-tekens of HTML-tags als markeringen te gebruiken. Als u wilt opgeven welke markeringen moeten worden gebruikt, neemt u de query parameter [TextFormat](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#textformat) op: 

| Waarde             | Tijdlijnmarkeerteken                       |
|-------------------|------------------------------|
| `textFormat=Raw`  | Unicode-tekens (standaard) |
| `textFormat=HTML` | HTML-tekens              |

## <a name="additional-text-decorations"></a>Extra tekst decoratie

Bing kan verschillende tekst decors retour neren. Een `Computation` antwoord kan bijvoorbeeld subscript markeringen bevatten voor de query term `log(2)` in het `expression` veld.

![Reken markeringen](./media/cognitive-services-bing-web-api/bing-markers-computation.png) 

Als de aanvraag geen decoratie heeft opgegeven, bevat `expression` `log10(2)`het veld. 

Als `textDecorations` dat `true`het geval is, kan Bing de volgende markeringen bevatten in de weergave teken reeksen van antwoorden. Als er geen equivalente HTML-code is, is de tabelcel leeg.

|Unicode|HTML|Beschrijving
|-|-|-
|U + E000|\<b>|Markeert het begin van de query term (treffers markeren)
|U + E001|\</b>|Markeert het einde van de query term
|U + E002|\<Ik>|Markeert het begin van cursieve inhoud 
|U + E003|\</i>|Markeert het einde van cursieve inhoud
|U + E004|\<BR/>|Markeert een regel einde
|U + E005||Markeert het begin van een telefoon nummer
|U + E006||Markeert het einde van een telefoon nummer
|U + E007||Markeert het begin van een adres
|U + E008||Markeert het einde van een adres
|U + E009|\&nbsp;|Hiermee wordt een vaste spatie gemarkeerd
|U + E00C|\<sterke>|Markeert het begin van vetgedrukte inhoud
|U + E00D|\</Strong>|Markeert het einde van vetgedrukte inhoud
|U + E00E||Markeert het begin van de inhoud waarvan de achtergrond lichter moet zijn dan de omringende achtergrond
|U + E00F||Markeert het einde van de inhoud waarvan de achtergrond lichter moet zijn dan de omringende achtergrond
|U + E010||Markeert het begin van de inhoud waarvan de achtergrond donkerder moet zijn dan de omringende achtergrond
|U + E011||Markeert het einde van de inhoud waarvan de achtergrond donkerder moet zijn dan de omringende achtergrond
|U + E012|\<del>|Markeert het begin van de inhoud die moet worden doorgehaald
|U + E013|\</del>|Markeert het einde van de inhoud die moet worden doorgehaald
|U + E016|\<sub->|Markeert het begin van de inhoud van het subscript
|U + E017|\</sub>|Markeert het einde van de inhoud van het subscript
|U + E018|\<sup>|Markeert het begin van de inhoud van Super script
|U + E019|\</sup>|Markeert het einde van de inhoud van Super script

## <a name="next-steps"></a>Volgende stappen

* [Wat is de Bing Webzoekopdrachten-API?](overview.md) 
* [Het formaat van miniaturen wijzigen en miniaturen bijsnijden](resize-and-crop-thumbnails.md)