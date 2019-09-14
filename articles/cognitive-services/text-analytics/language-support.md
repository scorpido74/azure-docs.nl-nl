---
title: Taal ondersteuning-Text Analytics-API
titleSuffix: Azure Cognitive Services
description: 'Een lijst met natuurlijke talen die worden ondersteund door de Text Analytics-API. In dit artikel wordt uitgelegd welke talen worden ondersteund voor elke bewerking: sentiment analyse, extractie van sleutel zinnen, taal detectie en entiteits herkenning.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: aahi
ms.openlocfilehash: e14cb56db6e45fe86ecd5e73766c47d29dd23fa1
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70984232"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>Ondersteuning van talen en regio's voor de Text Analytics-API

In dit artikel wordt uitgelegd welke talen worden ondersteund voor elke bewerking: sentiment analyse, extractie van sleutel zinnen, taal detectie en benoemde entiteits herkenning.

## <a name="language-detection"></a>Taaldetectie

Het Text Analytics-API kan een breed scala aan talen, varianten, dialecten en bepaalde regionale/culturele talen detecteren.  Taaldetectie retourneert het ' script ' van een taal. Bijvoorbeeld, voor de woord groep ' Ik heb een hond ', wordt geretourneerd `en` in plaats `en-US`van. Het enige speciale geval is Chinees, waarbij de mogelijkheid om de taal te `zh_CHS` detecteren `zh_CHT` wordt geretourneerd of waarmee het script kan worden bepaald op basis van de opgegeven tekst. In situaties waarin een specifiek script niet kan worden geïdentificeerd voor een Chinees document, wordt dit gewoon `zh`geretourneerd.

De exacte lijst met talen voor deze functie wordt niet gepubliceerd, maar het kan een breed scala aan talen, varianten, dialecten en enkele regionale/culturele talen detecteren. 

Als er inhoud in een minder vaak gebruikte taal wordt weer gegeven, kunt u Taaldetectie proberen om te zien of er een code wordt geretourneerd. Het antwoord op talen dat niet kan worden gedetecteerd `unknown`is.

## <a name="sentiment-analysis-key-phrase-extraction-and-named-entity-recognition"></a>Herkenning van Sentimentanalyse, Sleuteltermextractie en benoemde entiteit

Voor sentiment analyse, extractie van sleutel zinnen en entiteits herkenning is de lijst met ondersteunde talen meer selectief naarmate de analyseers worden verfijnd om te voldoen aan de taal kundige regels van andere talen. Ondersteuning voor de volledige set met [entiteits typen](how-tos/text-analytics-how-to-entity-linking.md#supported-types-for-named-entity-recognition) is momenteel beperkt tot de volgende talen: 
* Engels
* Chinees-vereenvoudigd
* Frans
* Duits
* Spaans

Alleen de `Person` `Location` en`Organization` benoemde entiteiten worden geretourneerd voor de andere talen.

## <a name="language-list-and-status"></a>Taal lijst en status

Taal ondersteuning wordt in eerste instantie in de preview-fase geïmplementeerd, met een afronding van de algemeen beschik bare (GA) status, onafhankelijk van elkaar en van de Text Analytics service. Het is mogelijk dat de talen in de preview-fase blijven, zelfs als Text Analytics-API overgangen algemeen beschikbaar zijn.

| Taal    | Taalcode | Sentiment | Sleutelfrasen | Herkenning van benoemde entiteiten |   Opmerkingen  |
|:----------- |:-------------:|:---------:|:-----------:|:-----------:|:-----------:
| Arabisch      | `ar`          |           |             | ✔ \*                     | |
| Tsjechisch       | `cs`          |           |             | ✔ \*                     | |
| Chinees-vereenvoudigd | `zh-CN`| ✔ \***     |             | ✔         |    |
| Deens      | `da`          | ✔ \*     | ✔           | ✔ \*            |     |
| Nederlands       | `nl`          | ✔ \*     | ✔          |  ✔ \*           |     |
| Engels     | `en`          | ✔        | ✔           |  ✔ \*\*     |      |
| Fins     | `fi`          | ✔ \*     | ✔           |  ✔ \*           |     |
| Frans      | `fr`          | ✔        | ✔           |  ✔            |     |
| Duits      | `de`          | ✔ \*     | ✔           |  ✔           |     |
| Grieks       | `el`          | ✔ \*     |             |            |     |
| Hongaars   | `hu`          |           |             |  ✔ \*          |     | 
| Italiaans     | `it`          | ✔ \*     | ✔           |  ✔ \*           |     |
| Japans    | `ja`          | ✔ \***         | ✔           |  ✔ \*          |     |
| Koreaans      | `ko`          |          | ✔           |  ✔ \*          |     |
| Noors (Bokmål) | `no`  | ✔ \*     |  ✔          | ✔ \*            |     |
| Pools      | `pl`          | ✔ \*     |  ✔          |  ✔ \*           |     |
| Portugees (Portugal) | `pt-PT`| ✔        |  ✔          | ✔ \*      |`pt`ook geaccepteerd|
| Portugees (Brazilië)   | `pt-BR`|          |  ✔   |  ✔ \*       |     |
| Russisch     | `ru`          | ✔ \*     | ✔           |  ✔ \*           |     |
| Spaans     | `es`          | ✔        | ✔           |   ✔ \*\*      |     | 
| Zweeds     | `sv`          | ✔ \*     | ✔           |   ✔ \*          |     |
| Turks     | `tr`          | ✔ \*     |             |   ✔ \*          |  |

\*Taal ondersteuning is beschikbaar als preview-versie

\*\*[Herkenning van benoemde entiteiten](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-ner) en [entiteits koppelingen](how-tos/text-analytics-how-to-entity-linking.md#entity-linking) zijn beide beschikbaar voor deze taal.  

\** * Beschikbaar in de [open bare preview van sentimentanalyse v3](https://docs.microsoft.com/en-us/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis#sentiment-analysis-v3-public-preview)

## <a name="see-also"></a>Zie ook

[Documentatie pagina van Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/)   
[Productpagina van Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)
