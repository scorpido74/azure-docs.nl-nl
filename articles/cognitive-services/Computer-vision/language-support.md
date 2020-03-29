---
title: Taalondersteuning - Computer Vision
titleSuffix: Azure Cognitive Services
description: Dit artikel bevat een lijst van natuurlijke talen die worden ondersteund door computervisiefuncties; OCR, Tekst herkennen en lezen.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: a834c68119340d796f87971912a07fc0524a6d21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220136"
---
# <a name="language-support-for-computer-vision"></a>Taalondersteuning voor Computer Vision

Sommige functies van Computer Vision ondersteunen meerdere talen; alle functies die hier niet worden genoemd, ondersteunen alleen Engels.

## <a name="text-recognition"></a>Tekstherkenning

Computer Vision kan tekst in vele talen herkennen. In het bijzonder ondersteunt de [OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) API verschillende talen, terwijl de [API lezen](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) en [Tekst herkennen](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) alleen Engels ondersteunen. Zie [Gedrukte en handgeschreven tekst herkennen](concept-recognizing-text.md) voor meer informatie over deze functionaliteit en de voordelen van elke API.

OCR detecteert automatisch de taal van het invoermateriaal, zodat er geen taalcode in de API-aanroep hoeft te worden opgegeven. Taalcodes worden echter altijd geretourneerd als `"language"` de waarde van het knooppunt in de JSON-respons.

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

## <a name="image-analysis"></a>Beeldanalyse

Sommige acties van de [ANALYSE - Image](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API kunnen `language` resultaten retourneren in andere talen, opgegeven met de queryparameter. Andere acties retourneren resultaten in het Engels, ongeacht welke taal is opgegeven, en anderen gooien een uitzondering voor niet-ondersteunde talen. Acties worden opgegeven `visualFeatures` `details` met de queryparameters en queryparameters; zie het [overzicht](home.md) voor een lijst met alle acties die u uitvoeren met beeldanalyse.

|Taal | Taalcode | Categorieën | Tags | Beschrijving | Volwassene | Merken | Kleur | Gezichten | ImageType | Objecten | Beroemdheden | Oriëntatiepunten |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Chinees | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Engels | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|Japans | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Portugees | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Spaans | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>Volgende stappen

Aan de slag met de computervision-functies die in deze handleiding worden genoemd.

* [Een lokale afbeelding analyseren (REST)](./quickstarts/csharp-analyze.md)
* [Afgedrukte tekst (REST) extraheren](./quickstarts/csharp-print-text.md)