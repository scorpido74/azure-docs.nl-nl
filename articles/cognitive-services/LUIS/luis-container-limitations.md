---
title: Container beperkingen-LUIS
titleSuffix: Azure Cognitive Services
description: De LUIS-container talen die worden ondersteund.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 2f4751440d719a542d39f8d8e83260c8cb628de0
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659282"
---
# <a name="language-understanding-luis-container-limitations"></a>Beperkingen van de container Language Understanding (LUIS)

De LUIS-containers hebben enkele belang rijke beperkingen. Van niet-ondersteunde afhankelijkheden, naar een subset van talen die worden ondersteund in dit artikel worden deze beperkingen beschreven.

## <a name="supported-dependencies-for-latest-container"></a>Ondersteunde afhankelijkheden voor de `latest` container

De meest recente LUIS-container ondersteunt:

* [Nieuwe, vooraf gemaakte domeinen](luis-reference-prebuilt-domains.md): deze domeinen in de onderneming bevatten onder andere entiteiten, voor beeld uitingen en patronen. Breid deze domeinen uit voor eigen gebruik.

## <a name="unsupported-dependencies-for-latest-container"></a>Niet-ondersteunde afhankelijkheden voor de `latest` container

Als u de [container wilt exporteren](luis-container-howto.md#export-packaged-app-from-luis), moet u niet-ondersteunde afhankelijkheden verwijderen uit uw Luis-app. Wanneer u probeert te exporteren voor container, rapporteert de LUIS-Portal deze niet-ondersteunde functies die u wilt verwijderen.

U kunt een LUIS-toepassing gebruiken als deze geen van de volgende afhankelijkheden **bevat** :

Niet-ondersteunde app-configuraties|Details|
|--|--|
|Niet-ondersteunde container culturen| De talen Nederlands ( `nl-NL` ), Japans ( `ja-JP` ) en Duits ( `de-DE` ) worden alleen ondersteund in de [1.0.2-tokenizer](luis-language-support.md#custom-tokenizer-versions).|
|Niet-ondersteunde entiteiten voor alle cult uren|Vooraf samengestelde [woordgroepen](luis-reference-prebuilt-keyphrase.md) instantie voor alle cult uren|
|Niet-ondersteunde entiteiten voor Engels ( `en-US` )-cultuur|[GeographyV2](luis-reference-prebuilt-geographyV2.md) vooraf gemaakte entiteiten|
|Spraak gebeuren|Externe afhankelijkheden worden niet ondersteund in de container.|
|Sentimentanalyse|Externe afhankelijkheden worden niet ondersteund in de container.|
|Bing-spelling controle|Externe afhankelijkheden worden niet ondersteund in de container.|

## <a name="languages-supported"></a>Ondersteunde talen

LUIS-containers bieden ondersteuning voor een subset van de talen die worden [ondersteund](luis-language-support.md#languages-supported) door Luis-goed. De LUIS-containers zijn geschikt voor uitingen in de volgende talen:

| Taal | Landinstelling | Vooraf gebouwd domein | Vooraf gebouwde entiteit | Aanbevelingen voor woordgroepen lijst | **[Tekst analyse](../text-analytics/language-support.md)<br>(Sentiment en<br>Woord|
|--|--|:--:|:--:|:--:|:--:|
| Amerikaans-Engels | `en-US` | ✔️ | ✔️ | ✔️ | ✔️ |
| *[Chinees](#chinese-support-notes) |`zh-CN` | ✔️ | ✔️ | ✔️ | ❌ |
| Frans (Frankrijk) |`fr-FR` | ✔️ | ✔️ | ✔️ | ✔️ |
| Frans (Canada) |`fr-CA` | ❌ | ❌ | ❌ | ✔️ |
| Duits |`de-DE` | ✔️ | ✔️ | ✔️ | ✔️ |
| Hindi | `hi-IN`| ❌ | ❌ | ❌ | ❌ |
| Italiaans |`it-IT` | ✔️ | ✔️ | ✔️ | ✔️ |
| Koreaans |`ko-KR` | ✔️ | ❌ | ❌ | Alleen *sleutel woorden* |
| Portugees (Brazilië) |`pt-BR` | ✔️ | ✔️ | ✔️ | niet alle subcultuuren |
| Spaans (Spanje) |`es-ES` | ✔️ | ✔️ |✔️|✔️|
| Spaans (Mexico)|`es-MX` | ❌ | ❌ |✔️|✔️|
| Turks | `tr-TR` |✔️| ❌ | ❌ | Alleen *sentiment* |

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]