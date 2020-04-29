---
title: Taal ondersteuning-Computer Vision
titleSuffix: Azure Cognitive Services
description: Dit artikel bevat een lijst met natuurlijke talen die worden ondersteund door Computer Vision-functies. OCR, Tekst herkennen en lezen.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: a834c68119340d796f87971912a07fc0524a6d21
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79220136"
---
# <a name="language-support-for-computer-vision"></a>Taal ondersteuning voor Computer Vision

Sommige functies van Computer Vision ondersteunen meerdere talen; alle functies die hier niet worden vermeld, bieden alleen ondersteuning voor Engels.

## <a name="text-recognition"></a>Tekstherkenning

Computer Vision kunt tekst in veel talen herkennen. Met name de [OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) -API ondersteunt diverse talen, terwijl de [Lees](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) -API en de [tekst herkennen](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) -API alleen ondersteuning bieden voor Engels. Zie [gedrukte en handgeschreven tekst herkennen](concept-recognizing-text.md) voor meer informatie over deze functionaliteit en de voor delen van elke API.

OCR detecteert automatisch de taal van het invoer materiaal, dus het is niet nodig om een taal code op te geven in de API-aanroep. Taal codes worden echter altijd geretourneerd als de waarde van het `"language"` knoop punt in het JSON-antwoord.

|Taal| Taalcode | OCR-API |
|:-----|:----:|:-----:|
|Arabisch | `ar`|✔ |
|Chinees (Vereenvoudigd) | `zh-Hans`|✔ |
|Chinees (Traditioneel) | `zh-Hant`|✔ |
|Tsjechisch | `cs` |✔ |
|Deens | `da` |✔ |
|Nederlands | `nl` |✔ |
|Engels | `en` |✔ |
|Fins | `fi` |✔ |
|Frans | `fr` |✔ |
|Duits | `de` |✔ |
|Grieks | `el` |✔ |
|Hongaars | `hu` |✔ |
|Italiaans | `it` |✔ |
|Japans | `ja` |✔ |
|Koreaans | `ko` |✔ |
|Norwegian | `nb` |✔ |
|Pools | `pl` |✔ |
|Portugees | `pt` |✔ |
|Roemeens | `ro` |✔ |
|Russisch | `ru` |✔ |
|Servisch (Cyrillisch) | `sr-Cyrl` |✔ |
|Servisch (Latijns) | `sr-Latn` |✔ |
|Slowaaks | `sk` |✔ |
|Spaans | `es` |✔ |
|Zweeds | `sw` |✔ |
|Turks | `tr` |✔ |

## <a name="image-analysis"></a>Analyse van afbeeldingen

Sommige acties van de [analyse-image-](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API kunnen resultaten retour neren in andere talen, opgegeven `language` met de query-para meter. Andere acties retour neren resultaten in het Engels, ongeacht de taal die is opgegeven, en anderen genereren een uitzonde ring voor niet-ondersteunde talen. Acties worden opgegeven met de `visualFeatures` - `details` en-query parameters; Zie het [overzicht](home.md) voor een lijst met alle acties die u kunt uitvoeren met afbeeldings analyse.

|Taal | Taalcode | Categorieën | Tags | Beschrijving | Volwassene | Merken | Kleur | Gezichten | ImageType | Objecten | Beroemdheden | Oriëntatiepunten |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Chinees | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Engels | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|Japans | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Portugees | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Spaans | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>Volgende stappen

Ga aan de slag met de Computer Vision-functies die in deze hand leiding worden beschreven.

* [Een lokale installatie kopie analyseren (REST)](./quickstarts/csharp-analyze.md)
* [Afgedrukte tekst extra heren (REST)](./quickstarts/csharp-print-text.md)