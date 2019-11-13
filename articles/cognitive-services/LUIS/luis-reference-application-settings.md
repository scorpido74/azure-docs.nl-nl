---
title: Toepassings instellingen-LUIS
titleSuffix: Azure Cognitive Services
description: Toepassings instellingen voor Azure Cognitive Services Language-informatie over apps worden opgeslagen in de app en de portal.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: bae4f09b539e26ca8c0d4ce97999776dc0911601
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961778"
---
# <a name="application-settings"></a>Toepassingsinstellingen

Deze toepassings instellingen worden opgeslagen in de [geëxporteerde](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) app en [bijgewerkt](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) met de rest api's. Als u de instellingen van uw app-versie wijzigt, wordt uw app-trainings status opnieuw ingesteld op untraind.

|Instelling|Standaardwaarde|Opmerkingen|
|--|--|--|
|NormalizePunctuation|True|Hiermee verwijdert u interpunctie.|
|NormalizeDiacritics|True|Diakritische tekens worden verwijderd.|

## <a name="diacritics-normalization"></a>Normalisatie van diakritische tekens 

Schakel in de para meter `settings` utterance normalisatie voor diakritische tekens in voor het JSON-app-bestand van uw LUIS.

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

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>Braziliaans-Portugees `pt-br` diakritische tekens

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

#### <a name="dutch-nl-nl-diacritics"></a>Nederlandse `nl-nl` diakritische tekens

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

#### <a name="italian-it-it-diacritics"></a>Italiaans `it-it` diakritische tekens

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

#### <a name="spanish-es--diacritics"></a>Spaans `es-` diakritische tekens

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

Schakel utterance normalisatie in voor interpunctie naar het JSON-app-bestand van uw LUIS in de para meter `settings`.

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

De volgende interpunctie wordt verwijderd met `NormalizePunctuation` is ingesteld op waar.

|Leestekens|
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
