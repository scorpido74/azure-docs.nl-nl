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
ms.date: 12/18/2019
ms.author: aahi
ms.openlocfilehash: c5a413a4fe8d9ac9b7aac59ca78cedc6d5a7a313
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2020
ms.locfileid: "78899438"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>Ondersteuning van talen en regio's voor de Text Analytics-API

In dit artikel wordt uitgelegd welke talen worden ondersteund voor elke bewerking: sentiment analyse, extractie van sleutel zinnen, taal detectie en benoemde entiteits herkenning.

## <a name="language-detection"></a>Taaldetectie

Het Text Analytics-API kan een breed scala aan talen, varianten, dialecten en bepaalde regionale/culturele talen detecteren.  Taaldetectie retourneert het ' script ' van een taal. Bijvoorbeeld: voor de woord groep ' Ik heb een hond ' wordt `en` in plaats van `en-US`geretourneerd. Het enige speciale geval is Chinees, waarbij de functie voor taal detectie `zh_CHS` of `zh_CHT` retourneert als het script de opgegeven tekst kan bepalen. In situaties waarin een specifiek script niet kan worden geïdentificeerd voor een Chinees document, wordt alleen `zh`geretourneerd.

De exacte lijst met talen voor deze functie wordt niet gepubliceerd, maar het kan een breed scala aan talen, varianten, dialecten en enkele regionale/culturele talen detecteren. 

Als er inhoud in een minder vaak gebruikte taal wordt weer gegeven, kunt u Taaldetectie proberen om te zien of er een code wordt geretourneerd. Het antwoord op talen dat niet kan worden gedetecteerd, is `unknown`.

## <a name="sentiment-analysis-key-phrase-extraction-and-named-entity-recognition"></a>Herkenning van Sentimentanalyse, Sleuteltermextractie en benoemde entiteit

Voor sentiment analyse, extractie van sleutel zinnen en entiteits herkenning is de lijst met ondersteunde talen meer selectief naarmate de analyseers worden verfijnd om te voldoen aan de taal kundige regels van andere talen. In benoemde entiteits herkenning v2 is de ondersteuning voor de volledige set [entiteits typen](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) momenteel beperkt tot de volgende talen: 
* Nederlands
* Chinees-vereenvoudigd
* Frans
* Duits
* Spaans

Alleen de `Person`, `Location` en `Organization` benoemde entiteiten worden geretourneerd voor de andere talen.

## <a name="language-list-and-status"></a>Taal lijst en status

Taal ondersteuning wordt in eerste instantie in de preview-fase geïmplementeerd, met een afronding van de algemeen beschik bare (GA) status, onafhankelijk van elkaar en van de Text Analytics service. Het is mogelijk dat de talen in de preview-fase blijven, zelfs als Text Analytics-API overgangen algemeen beschikbaar zijn.

> [!NOTE]
> Zie [benoemde entiteits typen](named-entity-types.md)voor gedetailleerde taal ondersteuning voor de ner (named entity Recognition) v3 open bare preview.

| Taal              | Taalcode | Stemming | Sleutel zinnen | Herkenning van benoemde entiteiten | Entiteit koppelen |       Opmerkingen        |
|:----------------------|:-------------:|:---------:|:-----------:|:------------------------:|:--------------:|:------------------:|
| Arabisch                |     `ar`      |           |             |           ✔ \*           |                |                    |
| Tsjechisch                 |     `cs`      |           |             |           ✔ \*           |                |                    |
| Chinees-vereenvoudigd    |   `zh-hans`   |  ✔ \*\*   |             |            ✔             |                | `zh` is ook geaccepteerd                   |
| Chinees-traditioneel   |   `zh-hant`   |  ✔ \*\*   |             |                          |                |                    |
| Deens                |     `da`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| Dutch                 |     `nl`      |   ✔ \**   |      ✔      |           ✔ \*           |                |                    |
| Nederlands               |     `en`      |   ✔ \**   |      ✔      |          ✔ \*\*          |     ✔ \**      |                    |
| Fins               |     `fi`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| Frans                |     `fr`      |   ✔ \**   |      ✔      |            ✔             |                |                    |
| Duits                |     `de`      |   ✔ \**   |      ✔      |            ✔             |                |                    |
| Grieks                 |     `el`      |   ✔ \*    |             |                          |                |                    |
| Hongaars             |     `hu`      |           |             |           ✔ \*           |                |                    |
| Italiaans               |     `it`      |   ✔ \**   |      ✔      |           ✔ \*           |                |                    |
| Japans              |     `ja`      |   ✔ \**   |      ✔      |           ✔ \*           |                |                    |
| Koreaans                |     `ko`      |   ✔ \*\*  |      ✔      |           ✔ \*           |                |                    |
| Noors (Bokmål)   |     `no`      |   ✔ \*    |      ✔      |           ✔ \*           |                | `nb` is ook geaccepteerd                   |
| Pools                |     `pl`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| Portugees (Portugal) |    `pt-PT`    |   ✔\**    |      ✔      |           ✔ \*           |                | `pt` is ook geaccepteerd |
| Portugees (Brazilië)   |    `pt-BR`    |           |      ✔      |           ✔ \*           |                |                    |
| Russisch               |     `ru`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| Spaans               |     `es`      |   ✔\**    |      ✔      |           ✔ \*           |     ✔ \**      |                    |
| Zweeds               |     `sv`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| Turks               |     `tr`      |   ✔ \*    |             |           ✔ \*           |                |                    |

\* taal ondersteuning is beschikbaar in de preview-versie

\** ook beschikbaar in de open bare voor beelden van de [sentimentanalyse v3](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis#sentiment-analysis-versions-and-features) en/of [entity Recognition v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) .

## <a name="see-also"></a>Zie ook

[Cognitive Services-documentatie pagina](https://docs.microsoft.com/azure/cognitive-services/)   
[Productpagina van Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)
