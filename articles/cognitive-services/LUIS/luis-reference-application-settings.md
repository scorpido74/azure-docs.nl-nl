---
title: Toepassingsinstellingen - LUIS
description: De instellingen voor toepassingen voor Azure Cognitive Services-apps voor taalbegrip worden opgeslagen in de app en portal.
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 9e17736cd6ff5074a6eab76a6cf5bdb8acedc185
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382209"
---
# <a name="application-settings"></a>Toepassingsinstellingen

Deze toepassingsinstellingen worden opgeslagen in de [geëxporteerde](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) app en [bijgewerkt](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) met de REST API's. Als u de instellingen van de app-versie wijzigt, wordt de trainingsstatus van uw app opnieuw ingesteld op ongetraind.

Leer [concepten](luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) van diacritics en interpunctie.

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

## <a name="next-steps"></a>Volgende stappen

* Leer [concepten](luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) van diacritics en interpunctie.
