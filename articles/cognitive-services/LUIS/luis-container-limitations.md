---
title: Containerbeperkingen - LUIS
titleSuffix: Azure Cognitive Services
description: De LUIS-containertalen die worden ondersteund.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 7fe773b35c5aba31b2fea66bd2be7b2745eac3ee
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879238"
---
# <a name="language-understanding-luis-container-limitations"></a>Taalbegrip (LUIS) containerbeperkingen

De LUIS containers hebben een paar opmerkelijke beperkingen. Van niet-ondersteunde afhankelijkheden tot een subset van ondersteunde talen, in dit artikel worden deze beperkingen beschreven.

## <a name="supported-dependencies-for-latest-container"></a>Ondersteunde afhankelijkheden `latest` voor container

De nieuwste LUIS-container, uitgebracht op ['build/ 2019',](https://news.microsoft.com/build2019/)ondersteunt:

* [Nieuwe vooraf gebouwde domeinen:](luis-reference-prebuilt-domains.md)deze op ondernemingen gerichte domeinen omvatten entiteiten, voorbeelduitingen en patronen. Breid deze domeinen uit voor eigen gebruik.

## <a name="unsupported-dependencies-for-latest-container"></a>Niet-ondersteunde afhankelijkheden `latest` voor container

Als u [wilt exporteren voor containers,](luis-container-howto.md#export-packaged-app-from-luis)moet u niet-ondersteunde afhankelijkheden uit uw LUIS-app verwijderen. Wanneer u probeert te exporteren voor containers, rapporteert de LUIS-portal deze niet-ondersteunde functies die u moet verwijderen.

U een LUIS-toepassing gebruiken als deze geen van de volgende afhankelijkheden **bevat:**

Niet-ondersteunde app-configuraties|Details|
|--|--|
|Niet-ondersteunde containerculturen| Nederlands`nl-NL`( )<br>Japans`ja-JP`( )<br>Duits wordt alleen ondersteund met de [1.0.2 tokenizer](luis-language-support.md#custom-tokenizer-versions).|
|Niet-ondersteunde entiteiten voor alle culturen|[Sleutelzin](luis-reference-prebuilt-keyphrase.md) voorgebouwde entiteit voor alle culturen|
|Niet-ondersteunde entiteiten voor`en-US`Engelse () cultuur|[GeographyV2](luis-reference-prebuilt-geographyV2.md) vooraf gebouwde entiteiten|
|Het priming van de toespraak|Externe afhankelijkheden worden niet ondersteund in de container.|
|Sentimentanalyse|Externe afhankelijkheden worden niet ondersteund in de container.|
|Bing spellingcontrole|Externe afhankelijkheden worden niet ondersteund in de container.|

## <a name="languages-supported"></a>Ondersteunde talen

LUIS-containers ondersteunen een subset van de talen die door LUIS correct [worden ondersteund.](luis-language-support.md#languages-supported) De LUIS-containers kunnen uitingen in de volgende talen begrijpen:

| Taal | Landinstelling | Vooraf gebouwd domein | Vooraf gebouwde entiteit | Aanbevelingen voor woordgroepen | **[Tekstanalyse](../text-analytics/language-support.md)<br>(Sentiment en<br>Trefwoorden)|
|--|--|:--:|:--:|:--:|:--:|
| Amerikaans Engels | `en-US` | ✔️ | ✔️ | ✔️ | ✔️ |
| *[Chinees](#chinese-support-notes) |`zh-CN` | ✔️ | ✔️ | ✔️ | ❌ |
| Frans (Frankrijk) |`fr-FR` | ✔️ | ✔️ | ✔️ | ✔️ |
| Frans (Canada) |`fr-CA` | ❌ | ❌ | ❌ | ✔️ |
| Duits |`de-DE` | ✔️ | ✔️ | ✔️ | ✔️ |
| Hindi | `hi-IN`| ❌ | ❌ | ❌ | ❌ |
| Italiaans |`it-IT` | ✔️ | ✔️ | ✔️ | ✔️ |
| Koreaans |`ko-KR` | ✔️ | ❌ | ❌ | *Alleen sleutelzin* |
| Portugees (Brazilië) |`pt-BR` | ✔️ | ✔️ | ✔️ | niet alle subculturen |
| Spaans (Spanje) |`es-ES` | ✔️ | ✔️ |✔️|✔️|
| Spaans (Mexico)|`es-MX` | ❌ | ❌ |✔️|✔️|
| Turks | `tr-TR` |✔️| ❌ | ❌ | *Sentiment* alleen |

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]