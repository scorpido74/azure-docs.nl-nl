---
title: Toepassingsinstellingen - LUIS
titleSuffix: Azure Cognitive Services
description: De instellingen voor toepassingen voor Azure Cognitive Services-apps voor taalbegrip worden opgeslagen in de app en portal.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: d1ead09f6248a6ad14646371aa70b42b57cf8e3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270802"
---
# <a name="application-settings"></a>Toepassingsinstellingen

Deze toepassingsinstellingen worden opgeslagen in de [geëxporteerde](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) app en [bijgewerkt](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) met de REST API's. Als u de instellingen van de app-versie wijzigt, wordt de trainingsstatus van uw app opnieuw ingesteld op ongetraind.

|Instelling|Standaardwaarde|Opmerkingen|
|--|--|--|
|Punctuatie normaliseren|True|Verwijdert interpunctie.|
|NormalizeDiacritics|True|Verwijdert diacritici.|

## <a name="diacritics-normalization"></a>Diacritics normalisatie

Schakel utterancenormalisatie voor diacritics in op uw LUIS `settings` JSON-appbestand in de parameter.

```JSON
"settings": [
    {"name": "NormalizeDiacritics", "value": "true"}
]
```

De volgende uitingen laten zien hoe diacritics normalisatie uitingen beïnvloedt:

|Met diacritics ingesteld op valse|Met diacritics ingesteld op ware|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>Taalondersteuning voor diacritics

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>Braziliaanse `pt-br` Portugese diacritics

|Diacritics ingesteld op valse|Diacritics ingesteld op ware|
|-|-|
|`á`|`a`|
|`â`|`a`|
|`ã`|`a`|
|`à`|`a`|
|`ç`|`c`|
|`é`|`e`|
|`ê`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ô`|`o`|
|`õ`|`o`|
|`ú`|`u`|
|||

#### <a name="dutch-nl-nl-diacritics"></a>Nederlandse `nl-nl` diacritics

|Diacritics ingesteld op valse|Diacritics ingesteld op ware|
|-|-|
|`á`|`a`|
|`à`|`a`|
|`é`|`e`|
|`ë`|`e`|
|`è`|`e`|
|`ï`|`i`|
|`í`|`i`|
|`ó`|`o`|
|`ö`|`o`|
|`ú`|`u`|
|`ü`|`u`|
|||

#### <a name="french-fr--diacritics"></a>Franse `fr-` diacritics

Dit omvat zowel Franse als Canadese subculturen.

|Diacritics ingesteld op valse|Diacritics ingesteld op ware|
|--|--|
|`é`|`e`|
|`à`|`a`|
|`è`|`e`|
|`ù`|`u`|
|`â`|`a`|
|`ê`|`e`|
|`î`|`i`|
|`ô`|`o`|
|`û`|`u`|
|`ç`|`c`|
|`ë`|`e`|
|`ï`|`i`|
|`ü`|`u`|
|`ÿ`|`y`|

#### <a name="german-de-de-diacritics"></a>Duitse `de-de` diacritics

|Diacritics ingesteld op valse|Diacritics ingesteld op ware|
|--|--|
|`ä`|`a`|
|`ö`|`o`|
|`ü`|`u`|

#### <a name="italian-it-it-diacritics"></a>Italiaanse `it-it` diacritics

|Diacritics ingesteld op valse|Diacritics ingesteld op ware|
|--|--|
|`à`|`a`|
|`è`|`e`|
|`é`|`e`|
|`ì`|`i`|
|`í`|`i`|
|`î`|`i`|
|`ò`|`o`|
|`ó`|`o`|
|`ù`|`u`|
|`ú`|`u`|

#### <a name="spanish-es--diacritics"></a>Spaanse `es-` diacritics

Dit geldt zowel voor de Spaanse als de Canadese Mexicaan.

|Diacritics ingesteld op valse|Diacritics ingesteld op ware|
|-|-|
|`á`|`a`|
|`é`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ú`|`u`|
|`ü`|`u`|
|`ñ`|`u`|


## <a name="punctuation-normalization"></a>Leestekensnormalisatie

Schakel utterancenormalisatie in voor interpunctie in uw LUIS `settings` JSON-appbestand in de parameter.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"}
]
```

De volgende uitingen laten zien hoe interpunctie uitingen beïnvloedt:

|Met interpunctie ingesteld op False|Met interpunctie ingesteld op True|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>Interpunctie verwijderd

De volgende interpunctie wordt `NormalizePunctuation` verwijderd met is ingesteld op true.

|Interpunctie|
|--|
|`-`|
|`.`|
|`'`|
|`"`|
|`\`|
|`/`|
|`?`|
|`!`|
|`_`|
|`,`|
|`;`|
|`:`|
|`(`|
|`)`|
|`[`|
|`]`|
|`{`|
|`}`|
|`+`|
|`¡`|
