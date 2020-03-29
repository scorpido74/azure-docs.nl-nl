---
title: Taalondersteuning - Text Analytics API
titleSuffix: Azure Cognitive Services
description: 'Een lijst met natuurlijke talen die worden ondersteund door de Text Analytics API. In dit artikel wordt uitgelegd welke talen voor elke bewerking worden ondersteund: sentimentanalyse, sleutelzinextractie, taaldetectie en entiteitsherkenning.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: aahi
ms.openlocfilehash: c5a413a4fe8d9ac9b7aac59ca78cedc6d5a7a313
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219278"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>Ondersteuning voor taal en regio voor de Text Analytics API

In dit artikel wordt uitgelegd welke talen voor elke bewerking worden ondersteund: sentimentanalyse, sleutelzinextractie, taaldetectie en benoemde entiteitsherkenning.

## <a name="language-detection"></a>Taaldetectie

De Text Analytics API kan een breed scala aan talen, varianten, dialecten en sommige regionale/culturele talen detecteren.  Taaldetectie retourneert het 'script' van een taal. Bijvoorbeeld, voor de zinsnede "Ik heb een `en` hond" het zal terugkeren in plaats van `en-US`. Het enige speciale geval is Chinees, waar `zh_CHS` de `zh_CHT` taaldetectiecapaciteit zal terugkeren of als het het manuscript kan bepalen gegeven de verstrekte tekst. In situaties waarin een specifiek script niet kan worden geïdentificeerd `zh`voor een Chinees document, zal het eenvoudig terugkeren.

We publiceren niet de exacte lijst met talen voor deze functie, maar het kan een breed scala aan talen, varianten, dialecten en sommige regionale/culturele talen detecteren. 

Als u inhoud hebt uitgedrukt in een minder vaak gebruikte taal, u Taaldetectie proberen om te zien of een code wordt geretourneerd. Het antwoord voor talen die `unknown`niet kunnen worden gedetecteerd is .

## <a name="sentiment-analysis-key-phrase-extraction-and-named-entity-recognition"></a>Sentimentanalyse, sleutelzinextractie en benoemde entiteitsherkenning

Voor sentimentanalyse, sleutelzinextractie en entiteitsherkenning is de lijst met ondersteunde talen selectiever omdat de analysatoren worden verfijnd om de taalkundige regels van extra talen te aanpassen. In Named Entity Recognition v2 is de ondersteuning voor de volledige reeks [entiteitstypen](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) momenteel beperkt tot de volgende talen: 
* Engels
* Chinees vereenvoudigd
* Frans
* Duits
* Spaans

Alleen `Person`de `Location` `Organization` entiteiten met de naam en de naam worden geretourneerd voor de andere talen.

## <a name="language-list-and-status"></a>Taallijst en -status

Taalondersteuning wordt in eerste instantie uitgerold in preview, waarbij ze onafhankelijk van elkaar en de Text Analytics-service in het algemeen afstuderen naar de algemeen beschikbare (GA)-status. Het is mogelijk dat talen in preview blijven, zelfs terwijl Text Analytics API overgaat naar algemeen beschikbaar.

> [!NOTE]
> Zie [Benoemde entiteitstypen](named-entity-types.md)voor gedetailleerde taalondersteuning voor het openbare voorbeeld van Named Entity Recognition(NER).

| Taal              | Taalcode | Sentiment | Sleutelzinnen | Herkenning van benoemde entiteiten | Koppeling van entiteiten |       Opmerkingen        |
|:----------------------|:-------------:|:---------:|:-----------:|:------------------------:|:--------------:|:------------------:|
| Arabisch                |     `ar`      |           |             |           ✔\*           |                |                    |
| Tsjechisch                 |     `cs`      |           |             |           ✔\*           |                |                    |
| Chinees vereenvoudigd    |   `zh-hans`   |  ✔\*\*   |             |            ✔             |                | `zh`ook aanvaard                   |
| Chinees-traditioneel   |   `zh-hant`   |  ✔\*\*   |             |                          |                |                    |
| Deens                |     `da`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Nederlands                 |     `nl`      |   ✔\**   |      ✔      |           ✔\*           |                |                    |
| Engels               |     `en`      |   ✔\**   |      ✔      |          ✔\*\*          |     ✔\**      |                    |
| Fins               |     `fi`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Frans                |     `fr`      |   ✔\**   |      ✔      |            ✔             |                |                    |
| Duits                |     `de`      |   ✔\**   |      ✔      |            ✔             |                |                    |
| Grieks                 |     `el`      |   ✔\*    |             |                          |                |                    |
| Hongaars             |     `hu`      |           |             |           ✔\*           |                |                    |
| Italiaans               |     `it`      |   ✔\**   |      ✔      |           ✔\*           |                |                    |
| Japans              |     `ja`      |   ✔\**   |      ✔      |           ✔\*           |                |                    |
| Koreaans                |     `ko`      |   ✔\*\*  |      ✔      |           ✔\*           |                |                    |
| Noors (Bokmål)   |     `no`      |   ✔\*    |      ✔      |           ✔\*           |                | `nb`ook aanvaard                   |
| Pools                |     `pl`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Portugees (Portugal) |    `pt-PT`    |   ✔\**    |      ✔      |           ✔\*           |                | `pt`ook aanvaard |
| Portugees (Brazilië)   |    `pt-BR`    |           |      ✔      |           ✔\*           |                |                    |
| Russisch               |     `ru`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Spaans               |     `es`      |   ✔\**    |      ✔      |           ✔\*           |     ✔\**      |                    |
| Zweeds               |     `sv`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Turks               |     `tr`      |   ✔\*    |             |           ✔\*           |                |                    |

\*Taalondersteuning is in preview

\** Ook beschikbaar in de [Sentiment Analysis v3](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis#sentiment-analysis-versions-and-features) en/ of [Named Entity Recognition v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) openbare previews.

## <a name="see-also"></a>Zie ook

[Pagina Documentatie van Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/)   
[Productpagina van Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)
