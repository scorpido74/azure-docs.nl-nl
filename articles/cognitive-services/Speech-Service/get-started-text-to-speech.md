---
title: Quickstart van spraaksynthese - Speech-service
titleSuffix: Azure Cognitive Services
description: Leer hoe u de Speech-SDK gebruikt om spraak-naar-tekst te converteren. In deze quickstart leert u meer over het bouwen van objecten en ontwerppatronen, ondersteunde audio-uitvoerindelingen, de spraak-CLI en aangepaste configuratieopties voor spraaksynthese.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/11/2020
ms.author: trbye
ms.custom: devx-track-python, devx-track-javascript
zone_pivot_groups: speech-full-stack
ms.openlocfilehash: e66da95bea0b03ddad1096b2b19751f73aaacbe0
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/13/2020
ms.locfileid: "88170120"
---
# <a name="get-started-with-speech-synthesis"></a>Aan de slag met spraaksynthese

In deze quickstart maakt u kennis met algemene ontwerppatronen voor het uitvoeren van een spraak-naar-tekstsynthese met behulp van de Speech-SDK. Eerst voert u een basisconfiguratie en -synthese uit en gaat u verder met geavanceerdere voorbeelden voor aangepaste toepassingsontwikkeling zoals:

* Antwoorden krijgen als stromen in het geheugen
* De uitgevoerde frequentie- en bitsnelheid aanpassen
* Synthese-aanvragen indienen met behulp van SSML (Speech Synthesis Markup Language)
* Neurale stemmen gebruiken

> [!TIP]
> Raadpleeg de [quickstart-voorbeelden](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart) op GitHub als u direct naar voorbeeldcode wilt gaan.

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-csharp.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [C++ Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cpp.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-javascript.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-python.md)]
::: zone-end

::: zone pivot="programming-language-spx"
[!INCLUDE [CLI include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cli.md)]
::: zone-end

## <a name="next-steps"></a>Volgende stappen

* [Aan de slag met Custom Voice](how-to-custom-voice.md)
* [Synthese verbeteren met SSML](speech-synthesis-markup.md)
* Meer informatie over het gebruik van de [Long Audio API](long-audio-api.md) voor grote tekstvoorbeelden zoals boeken en nieuwsartikelen
* Bekijk de [Quickstart-voorbeelden](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart) op GitHub
