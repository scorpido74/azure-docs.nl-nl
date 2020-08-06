---
title: Taal ondersteuning-Computer Vision
titleSuffix: Azure Cognitive Services
description: Dit artikel bevat een lijst met natuurlijke talen die worden ondersteund door Computer Vision-functies. OCR, afbeeldings analyse.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: 649f1e6cf79aa17d053cc547debf5232120bb4fe
ms.sourcegitcommit: 5a37753456bc2e152c3cb765b90dc7815c27a0a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/04/2020
ms.locfileid: "87760631"
---
# <a name="language-support-for-computer-vision"></a>Taal ondersteuning voor Computer Vision

Sommige functies van Computer Vision ondersteunen meerdere talen; alle functies die hier niet worden vermeld, bieden alleen ondersteuning voor Engels.

## <a name="optical-character-recognition-ocr"></a>Optische tekenherkenning (OCR)

De OCR-Api's van Computer Vision ondersteunen verschillende talen. U hoeft geen taal code op te geven. Zie [optische teken herkenning (OCR)](concept-recognizing-text.md) voor meer informatie.

|Taal| Taalcode | OCR-API | V 3.0 lezen | Lees de open bare preview van v 3.1 |
|:-----|:----:|:-----:|:---:|:---:|
|Arabisch | `ar`|✔ | | |
|Chinees (Vereenvoudigd) | `zh-Hans`|✔ | |✔ |
|Chinees (Traditioneel) | `zh-Hant`|✔ | | |
|Tsjechisch | `cs` |✔ | | |
|Deens | `da` |✔ | | |
|Nederlands | `nl` |✔ |✔ |✔ |
|Engels | `en` |✔ |✔ |✔ |
|Fins | `fi` |✔ | | |
|Frans | `fr` |✔ |✔ |✔ |
|Duits | `de` |✔ |✔ |✔ |
|Grieks | `el` |✔ | | |
|Hongaars | `hu` |✔ | | |
|Italiaans | `it` |✔ |✔ |✔ |
|Japans | `ja` |✔ | | |
|Koreaans | `ko` |✔ | | |
|Noors | `nb` |✔ | | |
|Pools | `pl` |✔ | | |
|Portugees | `pt` |✔ |✔ |✔ |
|Roemeens | `ro` |✔ | | |
|Russisch | `ru` |✔ | | |
|Servisch (Cyrillisch) | `sr-Cyrl` |✔ | | |
|Servisch (Latijns) | `sr-Latn` |✔ | | |
|Slowaaks | `sk` |✔ | | |
|Spaans | `es` |✔ |✔ |✔ |
|Zweeds | `sw` |✔ | | |
|Turks | `tr` |✔ | | |

## <a name="image-analysis"></a>Analyse van afbeeldingen

Sommige acties van de [analyse-image-](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API kunnen resultaten retour neren in andere talen, opgegeven met de `language` query-para meter. Andere acties retour neren resultaten in het Engels, ongeacht de taal die is opgegeven, en anderen genereren een uitzonde ring voor niet-ondersteunde talen. Acties zijn opgegeven met de `visualFeatures` `details` para meters en query; Zie het [overzicht](home.md) voor een lijst met alle acties die u kunt uitvoeren met afbeeldings analyse.

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
