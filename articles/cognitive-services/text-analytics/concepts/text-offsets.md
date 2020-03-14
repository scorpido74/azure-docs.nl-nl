---
title: Tekst verschuivingen in de Text Analytics-API
titleSuffix: Azure Cognitive Services
description: Meer informatie over verschuivingen die worden veroorzaakt door meertalige en Emoji-code ringen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 03/09/2020
ms.author: aahi
ms.reviewer: jdesousa
ms.openlocfilehash: 6e404c710a244f06676edf50c3f5c95a7d681e35
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79219234"
---
# <a name="text-offsets-in-the-text-analytics-api-output"></a>Tekst verschuivingen in de Text Analytics-API uitvoer

Meertalige en Emoji-ondersteuning heeft geleid tot Unicode-code ringen die meer dan één [code punt](https://wikipedia.org/wiki/Code_point) gebruiken om één weer gegeven teken te vertegenwoordigen, een grapheme genoemd. Bijvoorbeeld, emojis zoals 🌷 en 👍 kunnen verschillende tekens gebruiken om de vorm samen te stellen met extra tekens voor visuele kenmerken, zoals de weergave kleur. Op dezelfde manier wordt het Hindi woord `अनुच्छेद` als vijf letters gecodeerd en worden drie tekens gecombineerd.

Vanwege de verschillende lengtes van mogelijke meertalige en Emoji-code ringen, kan de Text Analytics-API retour neren in het antwoord.

## <a name="offsets-in-the-api-response"></a>Verschuivingen in de API-reactie. 

Houd rekening met het volgende wanneer verschuivingen het API-antwoord retourneert, zoals [named entity Recognition](../how-tos/text-analytics-how-to-entity-linking.md) of [sentimentanalyse](../how-tos/text-analytics-how-to-sentiment-analysis.md):

* Elementen in het antwoord zijn mogelijk specifiek voor het eind punt dat is aangeroepen. 
* HTTP POST/GET-nettoladingen worden gecodeerd in [UTF-8](https://www.w3schools.com/charsets/ref_html_utf8.asp), die al dan niet de standaard teken codering voor het compileren van de client of het besturings systeem kan zijn.
* Verschuivingen verwijzen naar grapheme counts op basis van de [Unicode 8.0.0](https://unicode.org/versions/Unicode8.0.0) -standaard, niet de teken aantallen.

## <a name="extracting-substrings-from-text-with-offsets"></a>Subtekenreeksen ophalen uit tekst met verschuivingen

Verschuivingen kunnen problemen veroorzaken bij het gebruik van methoden op basis van teken reeksen, bijvoorbeeld de methode .NET [subtekenreeks ()](https://docs.microsoft.com/dotnet/api/system.string.substring?view=netframework-4.8) . Eén probleem is dat een offset kan ertoe leiden dat een subtekenreeks-methode eindigt in het midden van een grapheme-code ring met meerdere tekens in plaats van het einde.

In .NET kunt u de [StringInfo](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8) -klasse gebruiken, waarmee u kunt werken met een teken reeks als een reeks tekstuele elementen, in plaats van afzonderlijke teken objecten. U kunt ook zoeken naar grapheme splitter-bibliotheken in uw favoriete software omgeving. 

De Text Analytics-API retourneert ook deze tekstuele elementen, voor het gemak.

## <a name="see-also"></a>Zie ook

* [Overzicht van Text Analytics](../overview.md)
* [Sentiment analyse](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Entiteit herkenning](../how-tos/text-analytics-how-to-entity-linking.md)
* [Taal detecteren](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Taal herkenning](../how-tos/text-analytics-how-to-language-detection.md)
