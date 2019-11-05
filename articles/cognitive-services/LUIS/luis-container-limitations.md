---
title: Container beperkingen-LUIS
titleSuffix: Azure Cognitive Services
description: De LUIS-container talen die worden ondersteund.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: bd8a7a63113bcf4e972ab08655aa58b35ddff03d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507856"
---
# <a name="language-understanding-luis-container-limitations"></a>Beperkingen van de container Language Understanding (LUIS)

De LUIS-containers hebben enkele belang rijke beperkingen. Van niet-ondersteunde afhankelijkheden, naar een subset van talen die worden ondersteund in dit artikel worden deze beperkingen beschreven.

## <a name="supported-dependencies-for-latest-container"></a>Ondersteunde afhankelijkheden voor `latest` container

De meest recente LUIS-container, uitgebracht op [//build/2019](https://news.microsoft.com/build2019/), biedt ondersteuning voor:

* [Nieuwe, vooraf gemaakte domeinen](luis-reference-prebuilt-domains.md): deze domeinen in de onderneming bevatten onder andere entiteiten, voor beeld uitingen en patronen. Breid deze domeinen uit voor eigen gebruik.

## <a name="unsupported-dependencies-for-latest-container"></a>Niet-ondersteunde afhankelijkheden voor `latest` container

Als u de [container wilt exporteren](luis-container-howto.md#export-packaged-app-from-luis), moet u niet-ondersteunde afhankelijkheden verwijderen uit uw Luis-app. Wanneer u probeert te exporteren voor container, rapporteert de LUIS-Portal deze niet-ondersteunde functies die u wilt verwijderen.

U kunt een LUIS-toepassing gebruiken als deze geen van de volgende afhankelijkheden **bevat** :

Niet-ondersteunde app-configuraties|Details|
|--|--|
|Niet-ondersteunde container culturen| Nederlands (`nl-NL`)<br>Japans (`ja-JP`)<br>Duits wordt alleen ondersteund met de [1.0.2 tokenizer](luis-language-support.md#custom-tokenizer-versions).|
|Niet-ondersteunde entiteiten voor alle cult uren|Vooraf samengestelde [woordgroepen](luis-reference-prebuilt-keyphrase.md) instantie voor alle cult uren|
|Niet-ondersteunde entiteiten voor de Engelse (`en-US`) cultuur|[GeographyV2](luis-reference-prebuilt-geographyV2.md) vooraf gemaakte entiteiten|
|Spraak gebeuren|Externe afhankelijkheden worden niet ondersteund in de container.|
|Sentimentanalyse|Externe afhankelijkheden worden niet ondersteund in de container.|
|Bing-spelling controle|Externe afhankelijkheden worden niet ondersteund in de container.|

## <a name="languages-supported"></a>Ondersteunde talen

LUIS-containers bieden ondersteuning voor een subset van de talen die worden [ondersteund](luis-language-support.md#languages-supported) door Luis-goed. De LUIS-containers zijn geschikt voor uitingen in de volgende talen:

| Taal | Landinstelling | Vooraf gebouwd domein | Vooraf gebouwde entiteit | Aanbevelingen voor woordgroepen lijst | **[tekst analyse](../text-analytics/language-support.md)<br>(Sentiment en<br>Woord|
|--|--|:--:|:--:|:--:|:--:|
| Amerikaans-Engels | `en-US` | ✔️ | ✔️ | ✔️ | ✔️ |
| *[Chinees](#chinese-support-notes) |`zh-CN` | ✔️ | ✔️ | ✔️ | ❌ |
| Frans (Frank rijk) |`fr-FR` | ✔️ | ✔️ | ✔️ | ✔️ |
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