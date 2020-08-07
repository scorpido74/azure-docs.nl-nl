---
title: 'Basisbeginselen van spraaksynthese: de Speech-service'
titleSuffix: Azure Cognitive Services
description: Leer hoe u de Speech-SDK gebruikt om spraak-naar-tekst te converteren. In dit artikel leert u meer over het bouwen van objecten, ondersteunde audio-uitvoerindelingen en aangepaste configuratieopties voor spraaksynthese.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: trbye
ms.custom: tracking-python, devx-track-javascript
zone_pivot_groups: programming-languages-set-two-with-js
ms.openlocfilehash: b571c5ee64e12021acf699f979d85d37af7bac7d
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407627"
---
# <a name="learn-the-basics-of-speech-synthesis"></a>Informatie over de basisbeginselen van spraaksynthese

In dit artikel maakt u kennis met algemene ontwerppatronen voor het uitvoeren van een spraak-naar-tekstsynthese met behulp van de Speech-SDK. Eerst voert u een basisconfiguratie en -synthese uit en gaat u verder met geavanceerdere voorbeelden voor aangepaste toepassingsontwikkeling zoals:

* Antwoorden krijgen als stromen in het geheugen
* De uitgevoerde frequentie- en bitsnelheid aanpassen
* Synthese-aanvragen indienen met behulp van SSML (Speech Synthesis Markup Language)
* Neurale stemmen gebruiken

> [!TIP]
> Als u nog niet een van onze quickstarts hebt voltooid, raden we u aan om aan de slag te gaan en tekst-naar-spraak zelf te proberen.
> * [Spraaksynthese naar een luidspreker](quickstarts/text-to-speech.md)

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

::: zone pivot="programming-language-more"
[!INCLUDE [More languages include](./includes/how-to/speech-to-text-basics/more.md)]
::: zone-end

## <a name="next-steps"></a>Volgende stappen

* [Aan de slag met Custom Voice](how-to-custom-voice.md)
* [Synthese verbeteren met SSML](speech-synthesis-markup.md)
