---
title: Taal ondersteuning-Text Analytics-API
titleSuffix: Azure Cognitive Services
description: Een lijst met natuurlijke talen die worden ondersteund door de Text Analytics-API. In dit artikel wordt uitgelegd welke talen voor elke bewerking worden ondersteund.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 08/26/2020
ms.author: aahi
ms.openlocfilehash: e2c6fc739fa81e6eb7c98073e3575e4143d317b2
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88932964"
---
# <a name="text-analytics-api-v3-language-support"></a>Ondersteuning voor Text Analytics-API v3-taal 

> [!IMPORTANT]
> Versie 3. x van de Text Analytics-API is momenteel niet beschikbaar in de volgende regio's: Centraal-India, UAE-noord, China-noord 2, China-oost.


#### <a name="sentiment-analysis"></a>[Sentimentanalyse](#tab/sentiment-analysis)

| Taal              | Taalcode | v2-ondersteuning | v3-ondersteuning | De versie van het v3-model wordt gestart: |              Notities |
|:----------------------|:-------------:|:----------:|:----------:|:--------------------------:|-------------------:|
| Chinees-vereenvoudigd    |   `zh-hans`   |     ✓      |     ✓      |         2019-10-01         | `zh` ook geaccepteerd |
| Chinees-traditioneel   |   `zh-hant`   |            |     ✓      |         2019-10-01         |                    |
| Deens               |     `da`      |     ✓      |            |                            |                    |
| Nederlands                 |     `nl`      |     ✓      |            |                            |                    |
| Engels               |     `en`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Fins               |     `fi`      |     ✓      |            |                            |                    |
| Frans                |     `fr`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Duits                |     `de`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Grieks                 |     `el`      |     ✓      |            |                            |                    |
| Italiaans               |     `it`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Japans              |     `ja`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Koreaans                |     `ko`      |            |     ✓      |         2019-10-01         |                    |
| Noors (Bokmål)   |     `no`      |     ✓      |     ✓       |        2020-07-01         |                    |
| Pools                |     `pl`      |     ✓      |            |                            |                    |
| Portugees (Portugal) |    `pt-PT`    |     ✓      |     ✓      |         2019-10-01         | `pt` ook geaccepteerd |
| Russisch               |     `ru`      |     ✓      |            |                            |                    |
| Spaans               |     `es`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Zweeds               |     `sv`      |     ✓      |            |                            |                    |
| Turks               |     `tr`      |     ✓      |     ✓       |         2020-07-01        |                    |

### <a name="opinion-mining-v31-preview-only"></a>Opinie-analyse (alleen v 3.1-Preview-versie)

| Taal              | Taalcode | Starten met versie van v3-model: |              Notities |
|:----------------------|:-------------:|:------------------------------------:|-------------------:|
| Engels               |     `en`      |              2020-04-01              |                    |


#### <a name="named-entity-recognition-ner"></a>[Herkenning van benoemde entiteiten (NER)](#tab/named-entity-recognition)

> [!NOTE]
> * NER v3 ondersteunt momenteel alleen Engelse en Spaanse talen. Als u NER v3 aanroept met een andere taal, retourneert de API v 2.1-resultaten, op voor waarde dat de taal wordt ondersteund in versie 2,1.
> * v 2.1 retourneert alleen de volledige set beschik bare entiteiten voor het Engels, vereenvoudigd Chinees, Frans, Duits en Spaans.  De entiteiten ' persoon ', ' locatie ' en ' organisatie ' worden geretourneerd voor de andere ondersteunde talen.

| Taal               | Taalcode | v 2.1-ondersteuning | v3-ondersteuning | Starten met versie van v3-model: |       Notities        |
|:-----------------------|:-------------:|:----------:|:----------:|:-------------------------------:|:------------------:|
| Arabisch                |     `ar`      |     ✓      |            |                                 |                    |
| Tsjechisch                 |     `cs`      |     ✓      |            |                                 |                    |
| Chinees-vereenvoudigd     |   `zh-hans`   |     ✓      |            |                                 | `zh` ook geaccepteerd |
| Chinees-traditioneel   |   `zh-hant`   |     ✓      |            |                                 |                    |
| Deens                |     `da`      |     ✓      |            |                                 |                    |
| Nederlands                 |     `nl`      |     ✓      |            |                                 |                    |
| Engels                |     `en`      |     ✓      |     ✓      |           2019-10-01            |                    |
| Fins               |     `fi`      |     ✓      |            |                                 |                    |
| Frans                 |     `fr`      |     ✓      |            |                                 |                    |
| Duits                 |     `de`      |     ✓      |            |                                 |                    |
| Hebreeuws                |     `he`      |     ✓      |            |                                 |                    |
| Hongaars             |     `hu`      |     ✓      |            |                                 |                    |
| Italiaans               |     `it`      |     ✓      |            |                                 |                    |
| Japans              |     `ja`      |     ✓      |            |                                 |                    |
| Koreaans                |     `ko`      |     ✓      |            |                                 |                    |
| Noors (Bokmål)   |     `no`      |     ✓      |            |                                 | `nb` ook geaccepteerd |
| Pools                |     `pl`      |     ✓      |            |                                 |                    |
| Portugees (Portugal) |    `pt-PT`    |     ✓      |            |                                 | `pt` ook geaccepteerd |
| Portugees (Brazilië)   |    `pt-BR`    |     ✓      |            |                                 |                    |
| Russisch              |     `ru`      |     ✓      |            |                                 |                    |
| Spaans               |     `es`      |     ✓      |     ✓       |              2020-04-01                   |                    |
| Zweeds               |     `sv`      |     ✓      |            |                                 |                    |
| Turks               |     `tr`      |     ✓      |            |                                 |                    |

#### <a name="key-phrase-extraction"></a>[Sleuteltermextractie](#tab/key-phrase-extraction)

| Taal              | Taalcode | v2-ondersteuning | v3-ondersteuning | Beschikbaar vanaf versie van v3-model: |       Notities        |
|:----------------------|:-------------:|:----------:|:----------:|:-----------------------------------------:|:------------------:|
| Nederlands                 |     `nl`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Engels               |     `en`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Fins               |     `fi`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Frans                |     `fr`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Duits                |     `de`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Italiaans               |     `it`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Japans              |     `ja`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Koreaans                |     `ko`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Noors (Bokmål)   |     `no`      |     ✓      |     ✓      |                2019-10-01                 | `nb` ook geaccepteerd |
| Pools                |     `pl`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Portugees (Portugal) |    `pt-PT`    |     ✓      |     ✓      |                2019-10-01                 | `pt` ook geaccepteerd |
| Portugees (Brazilië)   |    `pt-BR`    |     ✓      |     ✓      |                2019-10-01                 |                    |
| Russisch               |     `ru`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Spaans               |     `es`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Zweeds               |     `sv`      |     ✓      |     ✓      |                2019-10-01                 |                    |

#### <a name="entity-linking"></a>[Entiteiten koppelen](#tab/entity-linking)

| Taal | Taalcode | v2-ondersteuning | v3-ondersteuning | Beschikbaar vanaf versie van v3-model: | Notities |
|:---------|:-------------:|:----------:|:----------:|:-----------------------------------------:|:-----:|
| Engels  |     `en`      |     ✓      |     ✓      |                2019-10-01                 |       |
| Spaans  |     `es`      |     ✓      |     ✓      |                2019-10-01                 |       |

#### <a name="language-detection"></a>[Taaldetectie](#tab/language-detection)

Het Text Analytics-API kan een breed scala aan talen, varianten, dialecten en bepaalde regionale/culturele talen detecteren.  Taaldetectie retourneert het ' script ' van een taal. Bijvoorbeeld, voor de woord groep ' Ik heb een hond ', wordt geretourneerd  `en` in plaats van  `en-US` . Het enige speciale geval is Chinees, waarbij de mogelijkheid om de taal te detecteren wordt geretourneerd `zh_CHS` of waarmee `zh_CHT` het script kan worden bepaald op basis van de opgegeven tekst. In situaties waarin een specifiek script niet kan worden geïdentificeerd voor een Chinees document, wordt dit gewoon geretourneerd `zh` .

De exacte lijst met talen voor deze functie wordt niet gepubliceerd, maar het kan een breed scala aan talen, varianten, dialecten en enkele regionale/culturele talen detecteren. 

Als er inhoud in een minder vaak gebruikte taal wordt weer gegeven, kunt u Taaldetectie proberen om te zien of er een code wordt geretourneerd. Het antwoord op talen dat niet kan worden gedetecteerd is `unknown` .

---

## <a name="see-also"></a>Zie ook

* [Wat is de Text Analytics-API?](overview.md)   
