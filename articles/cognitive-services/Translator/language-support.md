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
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 6339b86166aff008bf17b5096d42629daf6e3401
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/04/2020
ms.locfileid: "84434283"
---
# <a name="language-and-region-support-for-translator"></a>Taal-en regio ondersteuning voor Translator

Het conversie programma ondersteunt de volgende talen voor tekst vertaling. Neural machine translation (NMT) is de nieuwe standaard voor automatische vertalingen van een hoge kwaliteit en is beschikbaar als de standaard waarde met behulp van v3 van Translator wanneer er een Neural-systeem beschikbaar is.

[Meer informatie over hoe automatische vertaling werkt](https://www.microsoft.com/translator/mt.aspx)

## <a name="translation"></a>Omzetting

**V2-vertaler**

> [!NOTE]
> V2 is op 30 april 2018 afgeschaft. Migreer uw toepassingen naar v3 om te kunnen profiteren van de nieuwe functionaliteit die alleen beschikbaar is in v3.

* Alleen statistisch: er is geen Neural systeem beschikbaar voor deze taal.
* Neural beschikbaar: er is een Neural-systeem beschikbaar. Gebruik de para meter `category=generalnn` om toegang te krijgen tot het Neural-systeem.
* Neural standaard: Neural is het standaard Vertaal systeem. Gebruik de para meter `category=smt` om toegang te krijgen tot het statistische systeem voor gebruik met de micro soft Translator-hub.
* Alleen Neural: alleen Neural-omzetting is beschikbaar.

**V3-vertaler** V3 Translator is Neural standaard en statistische systemen zijn alleen beschikbaar als er geen Neural-systeem bestaat.

> [!NOTE]
> Momenteel is een subset van de Neural-talen beschikbaar in het aangepaste conversie programma en worden er geleidelijk extra toegevoegd. [Talen weer geven die momenteel beschikbaar zijn in het aangepaste conversie programma](#customization).

|Taal|    Taalcode|    V3-API|
|:-----|:-----:|:-----|
|Afrikaans|    `af`|    Neural|
|Arabisch|    `ar`    |    Neural|
|Bengalese|    `bn`    |    Neural|
|Bosnisch (Latijns)|    `bs`    |    Neural|
|Bulgaars|    `bg`    |    Neural|
|Kantonees (traditioneel)|    `yue`|    Statische|
|Catalaans|    `ca`    |    Statische|
|Vereenvoudigd Chinees|    `zh-Hans`|Neural|
|Traditioneel Chinees|    `zh-Hant`        |Neural|
|Kroatisch|    `hr`    |Neural|
|Tsjechisch|    `cs`    |    Neural|
|Deens|    `da`        |Neural|
|Nederlands|    `nl`|    Neural|
|Engels|    `en`    |    Neural|
|Ests|    `et`    |    Neural|
|Fijian|    `fj`    |    Statische|
|Filipino|    `fil`    |    Statische|
|Fins|    `fi`    |    Neural|
|Frans|    `fr`    |    Neural|
|Duits|    `de`    |    Neural|
|Grieks|    `el`    |    Neural|
|Gujarati|    `gu`    |    Neural|
|Haïtiaans|    `ht`        |Statische|
|Hebreeuws    |`he`    |Neural
|Hindi|    `hi`    |    Neural|
|Hmong Daw|    `mww`    |    Statische|
|Hongaars|    `hu`    |    Neural|
|IJslands|    `is`    |    Neural|
|Indonesisch|    `id`    |    Statische|
|Iers | `ga`| Neural
|Italiaans|    `it`    |    Neural|
|Japans|    `ja`    |    Neural|
|Kannada|`kn`| Neural|
|Kazachs |`kk`| Neural|
|Swahili|    `sw`    |    Statische|
|Klingon|    `tlh-Latn`    |    Statische|
|Klingon (plqaD)|    `tlh-Piqd`    |    Statische|
|Koreaans    |`ko`    |    Neural|
|Lets|    `lv`    |    Neural|
|Litouws|    `lt`    |    Neural|
|Malagassische|    `mg`    |    Statische|
|Maleisisch|    `ms`        |Statische|
|Malajalam| `ml` | Neural
|Maltees|    `mt`    |    Statische|
|Maori| `mi`  | Neural|
|Marathi| `mr`  | Neural|
|Noors|    `nb`    |    Neural|
|Perzisch|    `fa`    |    Neural|
|Pools|    `pl`    |    Neural|
|Portugees (Brazilië)|    `pt-br`    |    Neural|
|Portugees (Portugal)| `pt-pt` | Neural
|Punjabi|`pa`|Neural
|Queretaro Otomi|    `otq`    |    Statische|
|Roemeens|    `ro`    |    Neural|
|Russisch|    `ru`    |    Neural|
|Samoan|    `sm`    |    Statische|
|Servisch (Cyrillisch)|    `sr-Cyrl`|    Statische|
|Servisch (Latijns)|    `sr-Latn`        |Statische|
|Slowaaks|    `sk`    |    Neural|
|Sloveens|    `sl`    |    Neural|
|Spaans|    `es`    |    Neural|
|Zweeds|    `sv`    |Neural|
|Tahitian|    `ty`    |Statische|
|Tamil|    `ta`    |    Neural|
|Telugu|    `te`    |    Neural|
|Thai|    `th`    |    Neural|
|Tongaanse|    `to`    |    Statische|
|Turks|    `tr`        |Neural|
|Oekraïens|    `uk`    |    Neural|
|Urdu|    `ur`    |    Statische|
|Vietnamees|    `vi`    |    Neural|
|Welsh|    `cy`    |    Neural|
|Yucatec Maya|    `yua`    |    Statische|

> [!NOTE]
> Taal code `pt` wordt standaard ingesteld op `pt-br` Portugees (Brazilië).

## <a name="transliteration"></a>Vele

De methode transtranscrib ondersteunt de volgende talen. In de ' aan/van ', ' <-> ' geeft aan dat de taal kan worden getranscribeerd van of naar een van de vermelde scripts. De '--> ' geeft aan dat de taal alleen kan worden getranscribeerd van het ene script naar het andere.

| Taal    | Taalcode | Script | Naar/van | Script|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Arabisch | `ar` | Arabisch`Arab` | <--> | Latijnse`Latn` |
|Bengalese  | `bn` | Bengaals`Beng` | <--> | Latijnse`Latn` |
| Chinees (Vereenvoudigd) | `zh-Hans` | Vereenvoudigd Chinees`Hans`| <--> | Latijnse`Latn` |
| Chinees (Vereenvoudigd) | `zh-Hans` | Vereenvoudigd Chinees`Hans`| <--> | Traditioneel Chinees`Hant`|
| Chinees (Traditioneel) | `zh-Hant` | Traditioneel Chinees`Hant`| <--> | Latijnse`Latn` |
| Chinees (Traditioneel) | `zh-Hant` | Traditioneel Chinees`Hant`| <--> | Vereenvoudigd Chinees`Hans` |
| Gujarati | `gu`  | Gujarati`Gujr` | <--> | Latijnse`Latn` |
| Hebreeuws | `he` | Hebreeuws`Hebr` | <--> | Latijnse`Latn` |
| Hindi | `hi` | Devanagari`Deva` | <--> | Latijnse`Latn` |
| Japans | `ja` | Japans`Jpan` | <--> | Latijnse`Latn` |
| Kannada | `kn` | Kannada`Knda` | <--> | Latijnse`Latn` |
| Malajalam | `ml` | Malajalam`Mlym` | <--> | Latijnse`Latn` |
| Marathi | `mr` | Devanagari`Deva` | <--> | Latijnse`Latn` |
| Odia | `or` | Odia`Orya` | <--> | Latijnse`Latn` |
| Punjabi | `pa` | Gurmukhi`Guru`  | <--> | Latijnse`Latn`  |
| Servisch (Cyrillisch) | `sr-Cyrl` | Cyrillisch`Cyrl`  | --> | Latijnse`Latn` |
| Servisch (Latijns) | `sr-Latn` | Latijnse`Latn` | --> | Cyrillisch`Cyrl`|
| Tamil | `ta` | Tamil`Taml` | <--> | Latijnse`Latn` |
| Telugu | `te` | Telugu`Telu` | <--> | Latijnse`Latn` |
| Thai | `th` | Thais`Thai` | <--> | Latijnse`Latn` |

## <a name="dictionary"></a>Woordenlijst

De woorden lijst ondersteunt de volgende talen in of vanuit het Engels met behulp van de methoden voor zoeken en voor beelden.

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
| Klingon      | `tlh-Latn`          |
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

## <a name="detect"></a>Detecteren

Translator detecteert alle talen die beschikbaar zijn voor vertaal-en vele.


## <a name="access-the-translator-language-list-programmatically"></a>Programmatisch toegang tot de lijst met Vertaal talen

U kunt een lijst met ondersteunde talen voor Translator v 3.0 ophalen met behulp van de talen methode. U kunt de lijst weer geven op functie, taal code en de taal naam in het Engels of een andere ondersteunde taal. Deze lijst wordt automatisch bijgewerkt door de micro soft Translator-service wanneer er nieuwe talen beschikbaar worden gesteld.

[Documentatie voor het bewerkings overzicht van talen weer geven](reference/v3-0-languages.md)

## <a name="customization"></a>Aanpassing

De volgende talen zijn beschikbaar voor aanpassing in of vanuit het Engels met [aangepaste vertaler](https://aka.ms/CustomTranslator).

| Taal    | Taalcode |
|:----------- |:-------------:|
| Arabisch       | `ar`          |
| Bengalese      | `bn`          |
| Bosnisch (Latijns)      | `bs`          |
| Bulgaars      | `bg`          |
| Vereenvoudigd Chinees      | `zh-Hans`          |
|Traditioneel Chinees|    `zh-Hant`    |
| Kroatisch      | `hr`          |
| Tsjechisch      | `cs`          |
| Deens      | `da`          |
| Nederlands      | `nl`          |
| Engels    | `en`     |
| Ests      | `et`          |
| Fins      | `fi`          |
| Frans      | `fr`          |
| Duits      | `de`          |
| Grieks      | `el`          |
| Hebreeuws      | `he`          |
| Hindi      | `hi`          |
| Hongaars      | `hu`          |
| IJslands | `is` |
| Indonesisch|    `id`    |
| Iers | `ga`    |
| Italiaans      | `it`          |
| Japans      | `ja`          |
| Swahili|    `sw`    |
| Koreaans      | `ko`          |
| Lets      | `lv`          |
| Litouws      | `lt`          |
| Malagassische|    `mg`    |
| Maori| `mi`  |
| Noors      | `nb`          |
| Perzisch      | `fa`          |
| Pools      | `pl`          |
| Portugees (Brazilië) | `pt-br` |
| Roemeens      | `ro`          |
| Russisch      | `ru`          |
| Samoan|    `sm`    |
| Servisch (Latijns)      | `sr-Latn`          |
| Slowaaks     | `sk`          |
| Sloveens      | `sl`          |
| Spaans      | `es`          |
| Zweeds      | `sv`          |
| Thai      | `th`          |
| Turks      | `tr`          |
| Oekraïens      | `uk`          |
| Vietnamees      | `vi`          |
| Welsh | `cy` |

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Toegang tot de lijst op de website van micro soft Translator

Voor een beknopt overzicht van de talen bevat de micro soft Translator-website alle talen die worden ondersteund door de conversie-en spraak-Api's. Deze lijst bevat geen informatie die specifiek is voor de ontwikkelaar, zoals taal codes.

[Bekijk de lijst met talen](https://www.microsoft.com/translator/languages.aspx)
