---
title: Taal ondersteuning-Translator Text-API
titleSuffix: Azure Cognitive Services
description: De Translator Text-API ondersteunt de volgende talen voor tekst omzetting met Neural machine translation (NMT).
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 12/02/2019
ms.author: swmachan
ms.openlocfilehash: 2ec8d389c99ad96e59bf49d4345855fa44d6c7aa
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901996"
---
# <a name="language-and-region-support-for-the-translator-text-api"></a>Ondersteuning van talen en regio's voor de Translator Text-API

De Translator Text-API ondersteunt de volgende talen voor tekst vertaling. Neural machine translation (NMT) is de nieuwe standaard voor automatische vertalingen van een hoge kwaliteit en is beschikbaar als de standaard waarde met behulp van v3 van de Translator Text-API wanneer er een Neural-systeem beschikbaar is.

[Meer informatie over hoe automatische vertaling werkt](https://www.microsoft.com/translator/mt.aspx)

## <a name="translation"></a>Omzetting

**V2 Translator-API**

> [!NOTE]
> V2 is op 30 april 2018 afgeschaft. Migreer uw toepassingen naar v3 om te kunnen profiteren van de nieuwe functionaliteit die alleen beschikbaar is in v3.

* Alleen statistisch: er is geen Neural systeem beschikbaar voor deze taal.
* Neural beschikbaar: er is een Neural-systeem beschikbaar. Gebruik de para meter `category=generalnn` om toegang te krijgen tot het Neural-systeem.
* Neural standaard: Neural is het standaard Vertaal systeem. Gebruik de para meter `category=smt` om toegang te krijgen tot het statistische systeem voor gebruik met de micro soft Translator-hub.
* Alleen Neural: alleen Neural-omzetting is beschikbaar.

**V3 Translator-API** De V3 Translator-API is Neural standaard en statistische systemen zijn alleen beschikbaar als er geen Neural-systeem bestaat.

> [!NOTE]
> Momenteel is een subset van de Neural-talen beschikbaar in het aangepaste conversie programma en worden er geleidelijk extra toegevoegd. [Talen weer geven die momenteel beschikbaar zijn in het aangepaste conversie programma](#customization).

|Taal|  Taalcode|  V3 API|
|:-----|:-----:|:-----|
|Afrikaans| `af`|   Neuraal|
|Arabisch|    `ar`    |   Neuraal|
|Bengalees|    `bn`    |   Neuraal|
|Bosnisch (Latijns)|   `bs`    |   Neuraal|
|Bulgaars| `bg`    |   Neuraal|
|Kantonees (Traditioneel)|   `yue`|  Statische|
|Catalaans|   `ca`    |   Statische|
|Vereenvoudigd Chinees|    `zh-Hans`|Neuraal|
|Traditioneel Chinees|   `zh-Hant`       |Neuraal|
|Kroatisch|  `hr`    |Neuraal|
|Tsjechisch| `cs`    |   Neuraal|
|Deens|    `da`        |Neuraal|
|Nederlands| `nl`|   Neuraal|
|Nederlands|   `en`    |   Neuraal|
|Estisch|  `et`    |   Neuraal|
|Fiji|    `fj`    |   Statische|
|Filipijns|  `fil`   |   Statische|
|Fins|   `fi`    |   Neuraal|
|Frans|    `fr`    |   Neuraal|
|Duits|    `de`    |   Neuraal|
|Grieks| `el`    |   Neuraal|
|Haïtiaans|    `ht`        |Statische|
|Hebreeuws |`he`   |Neuraal
|Hindi| `hi`    |   Neuraal|
|Hmong Daw| `mww`   |   Statische|
|Hongaars| `hu`    |   Neuraal|
|IJslands| `is`    |   Neuraal|
|Indonesisch|    `id`    |   Statische|
|Iers | `ga`| Neuraal
|Italiaans|   `it`    |   Neuraal|
|Japans|  `ja`    |   Neuraal|
|Kanarees|`kn`| Neuraal
|Swahili| `sw`    |   Statische|
|Klingon|   `tlh`   |   Statische|
|Klingon (plqaD)|   `tlh-Qaak`  |   Statische|
|Koreaans |`ko`   |   Neuraal|
|Lets|   `lv`    |   Neuraal|
|Litouws|    `lt`    |   Neuraal|
|Malagassische|  `mg`    |   Statische|
|Maleis| `ms`        |Statische|
|Malayalam| `ml` | Neuraal
|Maltees|   `mt`    |   Statische|
|Maori| `mi`  | Neuraal|
|Noors| `nb`    |   Neuraal|
|Perzisch|   `fa`    |   Neuraal|
|Pools|    `pl`    |   Neuraal|
|Portugees (Brazilië)|   `pt-br` |   Neuraal|
|Portugees (Portugal)| `pt-pt` | Neuraal
|Punjabi|`pa`|Neuraal
|Queretaro Otomi|   `otq`   |   Statische|
|Roemeens|  `ro`    |   Neuraal|
|Russisch|   `ru`    |   Neuraal|
|Samoaanse|    `sm`    |   Statische|
|Servisch (Cyrillisch)|    `sr-Cyrl`|  Statische|
|Servisch (Latijns)|   `sr-Latn`       |Statische|
|Slowaaks|    `sk`    |   Neuraal|
|Sloveens| `sl`    |   Neuraal|
|Spaans|   `es`    |   Neuraal|
|Zweeds|   `sv`    |Neuraal|
|Tahitian|  `ty`    |Statische|
|Tamil| `ta`    |   Neuraal|
|Telugu|    `te`    |   Neuraal|
|Thais|  `th`    |   Neuraal|
|Tongan|    `to`    |   Statische|
|Turks|   `tr`        |Neuraal|
|Oekraïens| `uk`    |   Neuraal|
|Urdu|  `ur`    |   Statische|
|Vietnamees|    `vi`    |   Neuraal|
|Welsh| `cy`    |   Neuraal|
|Yucatec Maya|  `yua`   |   Statische|

> [!NOTE]
> De taal code `pt` wordt standaard ingesteld op `pt-br`, Portugees (Brazilië).

## <a name="transliteration"></a>Transliteratie

De methode transtranscrib ondersteunt de volgende talen. In de ' aan/van ', ' <-> ' geeft aan dat de taal kan worden getranscribeerd van of naar een van de vermelde scripts. De '--> ' geeft aan dat de taal alleen kan worden getranscribeerd van het ene script naar het andere.

| Taal    | Taalcode | Script | Naar/van | Script|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Arabisch | `ar` | Arabische `Arab` | <--> | Latijns `Latn` |
|Bengalees  | `bn` | Bengali `Beng` | <--> | Latijns `Latn` |
| Chinees (vereenvoudigd) | `zh-Hans` | Vereenvoudigd Chinees `Hans`| <--> | Latijns `Latn` |
| Chinees (vereenvoudigd) | `zh-Hans` | Vereenvoudigd Chinees `Hans`| <--> | Traditioneel Chinees `Hant`|
| Chinees (traditioneel) | `zh-Hant` | Traditioneel Chinees `Hant`| <--> | Latijns `Latn` |
| Chinees (traditioneel) | `zh-Hant` | Traditioneel Chinees `Hant`| <--> | Vereenvoudigd Chinees `Hans` |
| Gujarati | `gu`  | Gujarati `Gujr` | --> | Latijns `Latn` |
| Hebreeuws | `he` | Hebreeuws `Hebr` | <--> | Latijns `Latn` |
| Hindi | `hi` | Devanagari `Deva` | <--> | Latijns `Latn` |
| Japans | `ja` | Japanse `Jpan` | <--> | Latijns `Latn` |
| Kanarees | `kn` | Kannada `Knda` | --> | Latijns `Latn` |
| Malayalam | `ml` | Malayalam `Mlym` | --> | Latijns `Latn` |
| Marathi | `mr` | Devanagari `Deva` | --> | Latijns `Latn` |
| Odia | `or` | Oriya `Orya` | <--> | Latijns `Latn` |
| Punjabi | `pa` | Gurmukhi `Guru`  | <--> | Latijns `Latn`  |
| Servisch (Cyrillisch) | `sr-Cyrl` | Cyrillische `Cyrl`  | --> | Latijns `Latn` |
| Servisch (Latijns) | `sr-Latn` | Latijns `Latn` | --> | Cyrillische `Cyrl`|
| Tamil | `ta` | Tamil `Taml` | --> | Latijns `Latn` |
| Telugu | `te` | Telugu `Telu` | --> | Latijns `Latn` |
| Thais | `th` | Thais `Thai` | <--> | Latijns `Latn` |

## <a name="dictionary"></a>Woordenlijst

De woorden lijst ondersteunt de volgende talen in of vanuit het Engels met behulp van de methoden voor zoeken en voor beelden.

| Taal    | Taalcode |
|:----------- |:-------------:|
| Afrikaans      | `af`          |
| Arabisch       | `ar`          |
| Bengalees      | `bn`          |
| Bosnisch (Latijns)      | `bs`          |
| Bulgaars      | `bg`          |
| Catalaans      | `ca`          |
| Vereenvoudigd Chinees      | `zh-Hans`          |
| Kroatisch      | `hr`          |
| Tsjechisch      | `cs`          |
| Deens      | `da`          |
| Nederlands      | `nl`          |
| Estisch      | `et`          |
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
| Maleis      | `ms`          |
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
| Thais      | `th`          |
| Turks      | `tr`          |
| Oekraïens      | `uk`          |
| Urdu      | `ur`          |
| Vietnamees      | `vi`          |
| Welsh      | `cy`          |

## <a name="detect"></a>Detecteren

Translator Text-API detecteert alle talen die beschikbaar zijn voor vertaal-en vele.


## <a name="access-the-translator-text-api-language-list-programmatically"></a>Programmatisch toegang tot de lijst met Translator Text-API talen

U kunt een lijst met ondersteunde talen voor de Translator Text-API v 3.0 ophalen met behulp van de talen methode. U kunt de lijst weer geven op functie, taal code en de taal naam in het Engels of een andere ondersteunde taal. Deze lijst wordt automatisch bijgewerkt door de micro soft Translator-service wanneer er nieuwe talen beschikbaar worden gesteld.

[Documentatie voor het bewerkings overzicht van talen weer geven](reference/v3-0-languages.md)

## <a name="customization"></a>Aanpassing

De volgende talen zijn beschikbaar voor aanpassing in of vanuit het Engels met [aangepaste vertaler](https://aka.ms/CustomTranslator).

| Taal    | Taalcode |
|:----------- |:-------------:|
| Arabisch       | `ar`          |
| Bengalees      | `bn`          |
| Bosnisch (Latijns)      | `bs`          |
| Bulgaars      | `bg`          |
| Vereenvoudigd Chinees      | `zh-Hans`          |
|Traditioneel Chinees|   `zh-Hant`   |
| Kroatisch      | `hr`          |
| Tsjechisch      | `cs`          |
| Deens      | `da`          |
| Nederlands      | `nl`          |
| Nederlands    | `en`     |
| Estisch      | `et`          |
| Fins      | `fi`          |
| Frans      | `fr`          |
| Duits      | `de`          |
| Grieks      | `el`          |
| Hebreeuws      | `he`          |
| Hindi      | `hi`          |
| Hongaars      | `hu`          |
| IJslands | `is` |
| Indonesisch|   `id`    |
| Iers | `ga`  |
| Italiaans      | `it`          |
| Japans      | `ja`          |
| Swahili|    `sw`    |
| Koreaans      | `ko`          |
| Lets      | `lv`          |
| Litouws      | `lt`          |
| Malagassische| `mg`    |
| Maori| `mi`  |
| Noors      | `nb`          |
| Perzisch      | `fa`          |
| Pools      | `pl`          |
| Portugees (Brazilië) | `pt-br` |
| Roemeens      | `ro`          |
| Russisch      | `ru`          |
| Samoaanse|   `sm`    |
| Servisch (Latijns)      | `sr-Latn`          |
| Slowaaks     | `sk`          |
| Sloveens      | `sl`          |
| Spaans      | `es`          |
| Zweeds      | `sv`          |
| Thais      | `th`          |
| Turks      | `tr`          |
| Oekraïens      | `uk`          |
| Vietnamees      | `vi`          |
| Welsh | `cy` |

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Toegang tot de lijst op de website van micro soft Translator

Voor een beknopt overzicht van de talen bevat de micro soft Translator-website alle talen die worden ondersteund door de Translator Text-en spraak-Api's. Deze lijst bevat geen informatie die specifiek is voor de ontwikkelaar, zoals taal codes.

[Bekijk de lijst met talen](https://www.microsoft.com/translator/languages.aspx)
