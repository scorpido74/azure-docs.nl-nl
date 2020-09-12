---
title: Taal ondersteuning-Translator
titleSuffix: Azure Cognitive Services
description: Cognitive Services Translator ondersteunt de volgende talen voor tekst omzetting met Neural machine translation (NMT).
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 06/10/2020
ms.author: swmachan
ms.openlocfilehash: 9c745395026b8b7e8c58fcb4b7cc67971d971a7c
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89300218"
---
# <a name="language-and-region-support-for-text-and-speech-translation"></a>Ondersteuning van talen en regio's voor tekst-en spraak omzetting

Gebruik Translator voor het vertalen van en naar een van de talen voor 70 en tekst vertalingen. Neural machine translation (NMT) is de nieuwe standaard voor automatische vertalingen van een hoge kwaliteit en is beschikbaar als de standaard waarde met behulp van v3 van Translator wanneer er een Neural-systeem beschikbaar is.

U kunt Translator ook gebruiken in combi natie met aangepaste vertaler voor het bouwen van Neural-Vertaal systemen die inzicht hebben in de terminologie die wordt gebruikt in uw eigen bedrijf en branche, en met micro soft Speech Service om spraak omzetting toe te voegen aan uw app.

[Meer informatie over hoe automatische vertaling werkt](https://www.microsoft.com/translator/mt.aspx)

## <a name="text-translation"></a>Tekstomzetting
Tekst omzetting is beschikbaar via de Vertaal bewerking naar of vanuit een van de beschik bare talen in Translator. De API biedt ook taal detectie met behulp van de detectie bewerking, vele met behulp van de trans actie, en tweetalige woorden lijsten met behulp van de voor beelden van dictionary lookup en Dictionary. De beschik bare talen voor elk van deze bewerkingen worden hieronder weer gegeven. 

### <a name="translate"></a>Translator

Translator ondersteunt de volgende talen voor tekst vertaling. 

[Naslag documentatie voor de Vertaal bewerking weer geven](reference/v3-0-translate.md)

|Taal|  Taalcode|
|:-----|:-----:|
|Afrikaans| `af`|
|Arabisch|    `ar`    |
|Bengalese|    `bn`    |
|Bosnisch (Latijns)|   `bs`    |
|Bulgaars| `bg`    |
|Kantonees (traditioneel)|   `yue`|
|Catalaans|   `ca`    |
|Vereenvoudigd Chinees|    `zh-Hans`|
|Traditioneel Chinees|   `zh-Hant`       |
|Kroatisch|  `hr`    |
|Tsjechisch| `cs`    |
|Dari|  `prs`   |
|Deens|    `da`        |
|Nederlands| `nl`|
|Engels|   `en`    |
|Ests|  `et`    |
|Fijian|    `fj`    |
|Filipino|  `fil`   |
|Fins|   `fi`    |
|Frans|    `fr`    |
|Duits|    `de`    |
|Grieks| `el`    |
|Gujarati|  `gu`    |
|Haïtiaans|    `ht`        |
|Hebreeuws |`he`   |
|Hindi| `hi`    |
|Hmong Daw| `mww`   |
|Hongaars| `hu`    |
|IJslands| `is`    |
|Indonesisch|    `id`    |
|Iers | `ga`|
|Italiaans|   `it`    |
|Japans|  `ja`    |
|Kannada|`kn`|
|Kazachs|`kk`|
|Swahili| `sw`    |
|Klingon|   `tlh-Latn`  |
|Klingon (plqaD)|   `tlh-Piqd`  |
|Koreaans |`ko`   |
|Koerdisch (centraal)  |`ku`   |
|Koerdisch (noordelijk) |`kmr`  |
|Lets|   `lv`    |
|Litouws|    `lt`    |
|Malagassische|  `mg`    |
|Maleisisch| `ms`        |
|Malajalam| `ml` |
|Maltees|   `mt`    |
|Maori| `mi`  |
|Marathi| `mr`  |
|Noors| `nb`    |
|Odia|  `or`    |
|Pashto|    `ps`    |
|Perzisch|   `fa`    |
|Pools|    `pl`    |
|Portugees (Brazilië)|   `pt-br` |
|Portugees (Portugal)| `pt-pt` |
|Punjabi|`pa`|
|Queretaro Otomi|   `otq`   |
|Roemeens|  `ro`    |
|Russisch|   `ru`    |
|Samoan|    `sm`    |
|Servisch (Cyrillisch)|    `sr-Cyrl`|
|Servisch (Latijns)|   `sr-Latn`       |
|Slowaaks|    `sk`    |
|Sloveens| `sl`    |
|Spaans|   `es`    |
|Zweeds|   `sv`    |
|Tahitian|  `ty`    |
|Tamil| `ta`    |
|Telugu|    `te`    |
|Thai|  `th`    |
|Tongaanse|    `to`    |
|Turks|   `tr`        |
|Oekraïens| `uk`    |
|Urdu|  `ur`    |
|Vietnamees|    `vi`    |
|Welsh| `cy`    |
|Yucatec Maya|  `yua`   |

> [!NOTE]
> Taal code `pt` wordt standaard ingesteld op `pt-br` Portugees (Brazilië).

### <a name="detect"></a>Detecteren

Translator detecteert de volgende talen voor vertaal-en vele.

[Referentie documentatie voor detectie bewerkingen weer geven](reference/v3-0-detect.md)

|Taal|  Taalcode|
|:-----|:-----:|
|Afrikaans| `af`|
|Arabisch|    `ar`    |
|Bulgaars| `bg`    |
|Catalaans|   `ca`    |
|Vereenvoudigd Chinees|    `zh-Hans`|
|Traditioneel Chinees|   `zh-Hant`       |
|Kroatisch|  `hr`    |
|Tsjechisch| `cs`    |
|Deens|    `da`        |
|Nederlands| `nl`|
|Engels|   `en`    |
|Ests|  `et`    |
|Fins|   `fi`    |
|Frans|    `fr`    |
|Duits|    `de`    |
|Grieks| `el`    |
|Gujarati|  `gu`    |
|Haïtiaans|    `ht`        |
|Hebreeuws |`he`   |
|Hindi| `hi`    |
|Hongaars| `hu`    |
|IJslands| `is`    |
|Indonesisch|    `id`    |
|Iers | `ga`|
|Italiaans|   `it`    |
|Japans|  `ja`    |
|Swahili| `sw`    |
|Klingon|   `tlh-Latn`  |
|Koreaans |`ko`   |
|Koerdisch (centraal)  |`ku-Arab`  |
|Lets|   `lv`    |
|Litouws|    `lt`    |
|Maleisisch| `ms`        |
|Maltees|   `mt`    |
|Noors| `nb`    |
|Pashto|    `ps`    |
|Perzisch|   `fa`    |
|Pools|    `pl`    |
|Portugees (Brazilië)|   `pt-br` |
|Portugees (Portugal)| `pt-pt` |
|Roemeens|  `ro`    |
|Russisch|   `ru`    |
|Servisch (Cyrillisch)|    `sr-Cyrl`|
|Servisch (Latijns)|   `sr-Latn`       |
|Slowaaks|    `sk`    |
|Sloveens| `sl`    |
|Spaans|   `es`    |
|Zweeds|   `sv`    |
|Tahitian|  `ty`    |
|Thai|  `th`    |
|Turks|   `tr`        |
|Oekraïens| `uk`    |
|Urdu|  `ur`    |
|Vietnamees|    `vi`    |
|Welsh| `cy`    |
|Yucatec Maya|  `yua`   |

### <a name="transliterate"></a>Transcriberen

De methode transtranscrib ondersteunt de volgende talen. In de ' aan/van ', ' <-> ' geeft aan dat de taal kan worden getranscribeerd van of naar een van de vermelde scripts. De '--> ' geeft aan dat de taal alleen kan worden getranscribeerd van het ene script naar het andere.

[Referentie documentatie voor trans-isatie bewerking weer geven](reference/v3-0-translate.md)


| Taal    | Taalcode | Script | Naar/van | Script|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Arabisch | `ar` | Arabisch `Arab` | <--> | Latijnse `Latn` |
| Bengalese  | `bn` | Bengaals `Beng` | <--> | Latijnse `Latn` |
| Chinees (Vereenvoudigd) | `zh-Hans` | Vereenvoudigd Chinees `Hans`| <--> | Latijnse `Latn` |
| Chinees (Vereenvoudigd) | `zh-Hans` | Vereenvoudigd Chinees `Hans`| <--> | Traditioneel Chinees `Hant`|
| Chinees (Traditioneel) | `zh-Hant` | Traditioneel Chinees `Hant`| <--> | Latijnse `Latn` |
| Chinees (Traditioneel) | `zh-Hant` | Traditioneel Chinees `Hant`| <--> | Vereenvoudigd Chinees `Hans` |
| Gujarati | `gu`  | Gujarati `Gujr` | <--> | Latijnse `Latn` |
| Hebreeuws | `he` | Hebreeuws `Hebr` | <--> | Latijnse `Latn` |
| Hindi | `hi` | Devanagari `Deva` | <--> | Latijnse `Latn` |
| Japans | `ja` | Japans `Jpan` | <--> | Latijnse `Latn` |
| Kannada | `kn` | Kannada `Knda` | <--> | Latijnse `Latn` |
| Malajalam | `ml` | Malajalam `Mlym` | <--> | Latijnse `Latn` |
| Marathi | `mr` | Devanagari `Deva` | <--> | Latijnse `Latn` |
| Odia | `or` | Odia `Orya` | <--> | Latijnse `Latn` |
| Punjabi | `pa` | Gurmukhi `Guru`  | <--> | Latijnse `Latn`  |
| Servisch (Cyrillisch) | `sr-Cyrl` | Cyrillisch `Cyrl`  | --> | Latijnse `Latn` |
| Servisch (Latijns) | `sr-Latn` | Latijnse `Latn` | --> | Cyrillisch `Cyrl`|
| Tamil | `ta` | Tamil `Taml` | <--> | Latijnse `Latn` |
| Telugu | `te` | Telugu `Telu` | <--> | Latijnse `Latn` |
| Thai | `th` | Thais `Thai` | --> | Latijnse `Latn` |

### <a name="dictionary"></a>Woordenlijst

De woorden lijst ondersteunt de volgende talen in of vanuit het Engels met behulp van de methoden voor zoeken en voor beelden.

Referentie documentatie weer geven voor de bewerkingen voor het [opzoeken van woorden lijsten](reference/v3-0-dictionary-lookup.md) en voor [beelden van woorden lijsten](reference/v3-0-dictionary-examples.md) .

| Taal    | Taalcode |
|:----------- |:-------------:|
| Afrikaans      | `af`          |
| Arabisch       | `ar`          |
| Bengalese      | `bn`          |
| Bosnisch (Latijns)      | `bs`          |
| Bulgaars      | `bg`          |
| Catalaans      | `ca`          |
| Vereenvoudigd Chinees      | `zh-Hans`          |
| Kroatisch      | `hr`          |
| Tsjechisch      | `cs`          |
| Deens      | `da`          |
| Nederlands      | `nl`          |
| Ests      | `et`          |
| Fins      | `fi`          |
| Frans      | `fr`          |
| Duits      | `de`          |
| Grieks      | `el`          |
| Haïtiaans      | `ht`          |
| Hebreeuws      | `he`          |
| Hindi      | `hi`          |
| Hmong Daw      | `mww`          |
| Hongaars      | `hu`          |
| IJslands    | `is`  |
| Indonesisch      | `id`          |
| Italiaans      | `it`          |
| Japans      | `ja`          |
| Swahili      | `sw`          |
| Klingon      | `tlh`          |
| Koreaans      | `ko`          |
| Lets      | `lv`          |
| Litouws      | `lt`          |
| Maleisisch      | `ms`          |
| Maltees      | `mt`          |
| Noors      | `nb`          |
| Perzisch      | `fa`          |
| Pools      | `pl`          |
| Portugees (Brazilië)     | `pt-br`          |
| Roemeens      | `ro`          |
| Russisch      | `ru`          |
| Servisch (Latijns)      | `sr-Latn`          |
| Slowaaks     | `sk`          |
| Sloveens      | `sl`          |
| Spaans      | `es`          |
| Zweeds      | `sv`          |
| Tamil      | `ta`          |
| Thai      | `th`          |
| Turks      | `tr`          |
| Oekraïens      | `uk`          |
| Urdu      | `ur`          |
| Vietnamees      | `vi`          |
| Welsh      | `cy`          |

### <a name="access-the-translator-language-list-programmatically"></a>Programmatisch toegang tot de lijst met Vertaal talen

U kunt een lijst met ondersteunde talen voor Translator ophalen met behulp van de talen methode. U kunt de lijst weer geven op functie, taal code en de taal naam in het Engels of een andere ondersteunde taal. Deze lijst wordt automatisch bijgewerkt door de micro soft Translator-service wanneer er nieuwe talen beschikbaar worden gesteld.

[Documentatie voor het bewerkings overzicht van talen weer geven](reference/v3-0-languages.md)

## <a name="customization"></a>Aanpassing

De volgende talen zijn beschikbaar voor aanpassing in of vanuit het Engels met [aangepaste vertaler](https://aka.ms/CustomTranslator).

| Taal    | Taalcode |
|:----------- |:-------------:|
|Afrikaans| `af`|
| Arabisch       | `ar`          |
| Bengalese      | `bn`          |
| Bosnisch (Latijns)      | `bs`          |
| Bulgaars      | `bg`          |
|Catalaans|   `ca`    |
| Vereenvoudigd Chinees      | `zh-Hans`          |
|Traditioneel Chinees|   `zh-Hant`   |
| Kroatisch      | `hr`          |
| Tsjechisch      | `cs`          |
| Deens      | `da`          |
| Nederlands      | `nl`          |
| Engels    | `en`     |
| Ests      | `et`          |
|Fijian|    `fj`    |
|Filipino|  `fil`   |
| Fins      | `fi`          |
| Frans      | `fr`          |
| Duits      | `de`          |
| Grieks      | `el`          |
| Gujarati| `gu`    |
| Hebreeuws      | `he`          |
| Hindi      | `hi`          |
| Hongaars      | `hu`          |
| IJslands | `is` |
| Indonesisch|   `id`    |
| Iers | `ga`  |
| Italiaans      | `it`          |
| Japans      | `ja`          |
|Kannada|`kn`|
| Swahili|    `sw`    |
| Koreaans      | `ko`          |
| Lets      | `lv`          |
| Litouws      | `lt`          |
| Malagassische| `mg`    |
| Maleisisch|    `ms`        |
|Maltees|   `mt`    |
| Maori| `mi`  |
| Marathi| `mr`  |
| Noors      | `nb`          |
| Perzisch      | `fa`          |
| Pools      | `pl`          |
| Portugees (Brazilië) | `pt-br` |
| Punjabi|`pa`|
| Roemeens      | `ro`          |
| Russisch      | `ru`          |
| Samoan|   `sm`    |
| Servisch (Latijns)      | `sr-Latn`          |
| Slowaaks     | `sk`          |
| Sloveens      | `sl`          |
| Spaans      | `es`          |
| Zweeds      | `sv`          |
|Tahitian|  `ty`    |
| Thai      | `th`          |
|Tongaanse|    `to`    |
| Turks      | `tr`          |
| Oekraïens      | `uk`          |
| Urdu| `ur`    |
| Vietnamees      | `vi`          |
| Welsh | `cy` |

## <a name="speech-translation"></a>Speech Translation
Spraak omzetting is beschikbaar via Translator met Cognitive Services Speech Service. Raadpleeg de documentatie van de [Speech-Service](https://docs.microsoft.com/azure/cognitive-services/speech-service/) voor meer informatie over het gebruik van spraak omzetting en voor het weer geven van alle [beschik bare taal opties](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support).

### <a name="speech-to-text"></a>Spraak naar tekst
Converteer spraak naar tekst om om te zetten naar de tekst taal van uw keuze. Spraak-naar-tekst wordt gebruikt voor spraak naar tekst omzetting, of voor conversie van spraak naar spraak wanneer deze wordt gebruikt in combi natie met spraak synthese.

| Taal    |
|:----------- |
|Arabisch|
|Kantonees (traditioneel)|
|Catalaans|
|Vereenvoudigd Chinees|
|Traditioneel Chinees|
|Deens|
|Nederlands|
|Engels|
|Fins|
|Frans|
|Duits|
|Gujarati|
|Hindi|
|Italiaans|
|Japans|
|Koreaans|
|Marathi|
|Noors|
|Pools|
|Portugees (Brazilië)|
|Portugees (Portugal)|
|Russisch|
|Spaans|
|Zweeds|
|Tamil|
|Telugu|
|Thai|
|Turks|

### <a name="text-to-speech"></a>Tekst naar spraak
Zet tekst om in spraak. Tekst-naar-spraak wordt gebruikt om hoorbare uitvoer van Vertaal resultaten toe te voegen, of voor conversie van spraak naar spraak bij gebruik met spraak-naar-tekst. 

| Taal    |
|:----------- |
|Arabisch|
|Bulgaars|
|Kantonees (traditioneel)|
|Catalaans|
|Vereenvoudigd Chinees|
|Traditioneel Chinees|
|Kroatisch|
|Tsjechisch|
|Deens|
|Nederlands|
|Engels|
|Fins|
|Frans|
|Duits|
|Grieks|
|Hebreeuws|
|Hindi|
|Hongaars|
|Indonesisch|
|Italiaans|
|Japans|
|Koreaans|
|Maleisisch|
|Noors|
|Pools|
|Portugees (Brazilië)|
|Portugees (Portugal)|
|Roemeens|
|Russisch|
|Slowaaks|
|Sloveens|
|Spaans|
|Zweeds|
|Tamil|
|Telugu|
|Thai|
|Turks|
|Vietnamees|

## <a name="view-the-language-list-on-the-microsoft-translator-website"></a>De taal lijst op de micro soft Translator-website weer geven

Voor een beknopt overzicht van de talen bevat de micro soft Translator-website alle talen die door Translator worden ondersteund voor tekst vertalingen en spraak service voor spraak omzetting. Deze lijst bevat geen informatie die specifiek is voor de ontwikkelaar, zoals taal codes.

[Bekijk de lijst met talen](https://www.microsoft.com/translator/languages.aspx)
