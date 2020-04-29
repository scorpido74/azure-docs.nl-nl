---
title: Toepassings instellingen-LUIS
description: Toepassings instellingen voor Azure Cognitive Services Language-informatie over apps worden opgeslagen in de app en de portal.
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 9e17736cd6ff5074a6eab76a6cf5bdb8acedc185
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81382209"
---
# <a name="application-settings"></a>Toepassingsinstellingen

Deze toepassings instellingen worden opgeslagen in de [geëxporteerde](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) app en [bijgewerkt](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) met de rest api's. Als u de instellingen van uw app-versie wijzigt, wordt uw app-trainings status opnieuw ingesteld op untraind.

Leer [concepten](luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) van diakritische tekens en lees tekens.

|Instelling|Standaardwaarde|Opmerkingen|
|--|--|--|
|NormalizePunctuation|True|Hiermee verwijdert u interpunctie.|
|NormalizeDiacritics|True|Diakritische tekens worden verwijderd.|

## <a name="diacritics-normalization"></a>Normalisatie van diakritische tekens

Schakel utterance normalisatie voor diakritische tekens in voor het JSON-app-bestand van uw `settings` Luis in de para meter.

```JSON
"settings": [
    {"name": "NormalizeDiacritics", "value": "true"}
]
```

In de volgende uitingen ziet u hoe diakritische tekens worden genormaliseerd uitingen:

|Waarbij diakritische tekens zijn ingesteld op False|Als diakritische tekens zijn ingesteld op True|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>Taal ondersteuning voor diakritische tekens

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>Braziliaanse Portugees `pt-br` -diakritische tekens

|Diakritische tekens ingesteld op ONWAAR|Diakritische tekens ingesteld op True|
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

#### <a name="dutch-nl-nl-diacritics"></a>Nederlandstalige `nl-nl` diakritische tekens

|Diakritische tekens ingesteld op ONWAAR|Diakritische tekens ingesteld op True|
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

#### <a name="french-fr--diacritics"></a>Franse `fr-` diakritische tekens

Dit omvat zowel Franse als Canadese subcultuuren.

|Diakritische tekens ingesteld op ONWAAR|Diakritische tekens ingesteld op True|
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

#### <a name="german-de-de-diacritics"></a>Duitse `de-de` diakritische tekens

|Diakritische tekens ingesteld op ONWAAR|Diakritische tekens ingesteld op True|
|--|--|
|`ä`|`a`|
|`ö`|`o`|
|`ü`|`u`|

#### <a name="italian-it-it-diacritics"></a>Italiaanse `it-it` diakritische tekens

|Diakritische tekens ingesteld op ONWAAR|Diakritische tekens ingesteld op True|
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

#### <a name="spanish-es--diacritics"></a>Spaanse `es-` diakritische tekens

Dit omvat zowel de Spaanse als de Canadese Mexicaanse.

|Diakritische tekens ingesteld op ONWAAR|Diakritische tekens ingesteld op True|
|-|-|
|`á`|`a`|
|`é`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ú`|`u`|
|`ü`|`u`|
|`ñ`|`u`|


## <a name="punctuation-normalization"></a>Normalisatie van interpunctie

Schakel utterance normalisatie in voor interpunctie naar het JSON-app-bestand van `settings` uw Luis in de para meter.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"}
]
```

In de volgende uitingen ziet u hoe interpunctie gevolgen heeft voor uitingen:

|Met interpunctie ingesteld op ONWAAR|Met interpunctie ingesteld op True|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>Verwijderde interpunctie

De volgende interpunctie is verwijderd `NormalizePunctuation` met is ingesteld op waar.

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

* Leer [concepten](luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) van diakritische tekens en lees tekens.
