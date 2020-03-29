---
title: Tekstverschuivingen in de API Text Analytics
titleSuffix: Azure Cognitive Services
description: Meer informatie over verschuivingen veroorzaakt door meertalige en emoji-coderingen.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219234"
---
# <a name="text-offsets-in-the-text-analytics-api-output"></a>Tekstverschuivingen in de API-uitvoer van Text Analytics

Ondersteuning voor meertalige en emoji's heeft geleid tot Unicode-coderingen die meer dan één [codepunt](https://wikipedia.org/wiki/Code_point) gebruiken om één weergegeven teken weer te geven, een grafeme genoemd. Emoji's zoals 🌷 👍 en kunnen bijvoorbeeld meerdere tekens gebruiken om de vorm samen te stellen met extra tekens voor visuele kenmerken, zoals huidskleur. Op dezelfde manier wordt `अनुच्छेद` het Hindi-woord gecodeerd als vijf letters en drie combinerende markeringen.

Vanwege de verschillende lengtes van mogelijke meertalige en emoji-coderingen, kan de Text Analytics API verschuivingen in het antwoord retourneren.

## <a name="offsets-in-the-api-response"></a>Verschuivingen in de API-respons. 

Wanneer verschuivingen worden geretourneerd, onthoudt [Named Entity Recognition](../how-tos/text-analytics-how-to-entity-linking.md) u het [Sentiment Analysis](../how-tos/text-analytics-how-to-sentiment-analysis.md)volgende:

* Elementen in het antwoord kunnen specifiek zijn voor het eindpunt dat is aangeroepen. 
* HTTP POST/GET payloads zijn gecodeerd in [UTF-8](https://www.w3schools.com/charsets/ref_html_utf8.asp), wat al dan niet de standaard tekencodering is op uw client-side compiler of besturingssysteem.
* Verschuivingen verwijzen naar grafemetellingen op basis van de [Unicode 8.0.0-standaard,](https://unicode.org/versions/Unicode8.0.0) niet naar tekentellingen.

## <a name="extracting-substrings-from-text-with-offsets"></a>Subtekenreeksen extraheren uit tekst met verschuivingen

Verschuivingen kunnen problemen veroorzaken bij het gebruik van substringmethoden op basis van tekens, bijvoorbeeld de methode [.NET-substring().](https://docs.microsoft.com/dotnet/api/system.string.substring?view=netframework-4.8) Een probleem is dat een verschuiving kan leiden tot een substring methode te eindigen in het midden van een multi-teken grafeme codering in plaats van het einde.

Overweeg in .NET de klasse [StringInfo](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8) te gebruiken, waarmee u met een tekenreeks werken als een reeks tekstuele elementen, in plaats van afzonderlijke tekenobjecten. U ook zoeken naar grapheme splitter bibliotheken in uw gewenste software omgeving. 

De Text Analytics API retourneert deze tekstuele elementen ook, voor het gemak.

## <a name="see-also"></a>Zie ook

* [Overzicht van Text Analytics](../overview.md)
* [Sentimentanalyse](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Herkenning van entiteiten](../how-tos/text-analytics-how-to-entity-linking.md)
* [Taal detecteren](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Taalherkenning](../how-tos/text-analytics-how-to-language-detection.md)
