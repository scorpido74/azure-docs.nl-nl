---
title: Taalondersteuning - Translator Text API
titleSuffix: Azure Cognitive Services
description: De Translator Text API ondersteunt de volgende talen voor tekst-naar-tekstvertaling met Neural Machine Translation (NMT).
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/10/2020
ms.author: swmachan
ms.openlocfilehash: 0ecde5acb7dc57ed9e5802c1589d5813a9206643
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684829"
---
# <a name="language-and-region-support-for-the-translator-text-api"></a>Ondersteuning voor taal en regio voor de Translator Text API

De Translator Text API ondersteunt de volgende talen voor tekst-naar-tekstvertaling. Neural Machine Translation (NMT) is de nieuwe standaard voor hoogwaardige AI-gestuurde machinevertalingen en is beschikbaar als standaard met V3 van de Translator Text API wanneer een neuraal systeem beschikbaar is.

[Meer informatie over hoe machinevertaling werkt](https://www.microsoft.com/translator/mt.aspx)

## <a name="translation"></a>Omzetting

**V2 Translator API**

> [!NOTE]
> V2 werd afgeschaft op 30 april 2018. Migreer uw applicaties naar V3 om te profiteren van nieuwe functionaliteit die exclusief beschikbaar is in V3.

* Alleen statistisch: Er is geen neuraal systeem beschikbaar voor deze taal.
* Neurale beschikbaar: Een neuraal systeem is beschikbaar. Gebruik de `category=generalnn` parameter om toegang te krijgen tot het neurale systeem.
* Neurale standaard: Neurale is het standaard vertaalsysteem. Gebruik de `category=smt` parameter om toegang te krijgen tot het statistische systeem voor gebruik met de Microsoft Translator Hub.
* Neurale alleen: Alleen neurale vertaling is beschikbaar.

**V3 Translator API** De V3 Translator API is standaard neurale en statistische systemen zijn alleen beschikbaar wanneer er geen neuraal systeem bestaat.

> [!NOTE]
> Momenteel is een subset van de neurale talen beschikbaar in Custom Translator en voegen we geleidelijk extra talen toe. [Bekijk talen die momenteel beschikbaar zijn in Aangepaste vertaler](#customization).

|Taal|  Taalcode|  V3-API|
|:-----|:-----:|:-----|
|Afrikaans| `af`|   Neurale|
|Arabisch|    `ar`    |   Neurale|
|Bangla|    `bn`    |   Neurale|
|Bosnisch (Latijns)|   `bs`    |   Neurale|
|Bulgaars| `bg`    |   Neurale|
|Kantonees (Traditioneel)|   `yue`|  Statistische|
|Catalaans|   `ca`    |   Statistische|
|Vereenvoudigd Chinees|    `zh-Hans`|Neurale|
|Traditioneel Chinees|   `zh-Hant`       |Neurale|
|Kroatisch|  `hr`    |Neurale|
|Tsjechisch| `cs`    |   Neurale|
|Deens|    `da`        |Neurale|
|Nederlands| `nl`|   Neurale|
|Engels|   `en`    |   Neurale|
|Ests|  `et`    |   Neurale|
|Fijian|    `fj`    |   Statistische|
|Filipino|  `fil`   |   Statistische|
|Fins|   `fi`    |   Neurale|
|Frans|    `fr`    |   Neurale|
|Duits|    `de`    |   Neurale|
|Grieks| `el`    |   Neurale|
|Gujarati|  `gu`    |   Neurale|
|Haïtiaans|    `ht`        |Statistische|
|Hebreeuws |`he`   |Neurale
|Hindi| `hi`    |   Neurale|
|Hmong Daw| `mww`   |   Statistische|
|Hongaars| `hu`    |   Neurale|
|IJslands| `is`    |   Neurale|
|Indonesisch|    `id`    |   Statistische|
|Iers | `ga`| Neurale
|Italiaans|   `it`    |   Neurale|
|Japans|  `ja`    |   Neurale|
|Kannada|`kn`| Neurale
|Kiswahili| `sw`    |   Statistische|
|Klingon|   `tlh`   |   Statistische|
|Klingon (plqaD)|   `tlh-Qaak`  |   Statistische|
|Koreaans |`ko`   |   Neurale|
|Lets|   `lv`    |   Neurale|
|Litouws|    `lt`    |   Neurale|
|Malagasi|  `mg`    |   Statistische|
|Maleisisch| `ms`        |Statistische|
|Malajalam| `ml` | Neurale
|Maltees|   `mt`    |   Statistische|
|Maori| `mi`  | Neurale|
|Marathi| `mr`  | Neurale|
|Norwegian| `nb`    |   Neurale|
|Perzisch|   `fa`    |   Neurale|
|Pools|    `pl`    |   Neurale|
|Portugees (Brazilië)|   `pt-br` |   Neurale|
|Portugees (Portugal)| `pt-pt` | Neurale
|Punjabi|`pa`|Neurale
|Queretaro Otomi|   `otq`   |   Statistische|
|Roemeens|  `ro`    |   Neurale|
|Russisch|   `ru`    |   Neurale|
|Samoan|    `sm`    |   Statistische|
|Servisch (Cyrillisch)|    `sr-Cyrl`|  Statistische|
|Servisch (Latijns)|   `sr-Latn`       |Statistische|
|Slowaaks|    `sk`    |   Neurale|
|Sloveens| `sl`    |   Neurale|
|Spaans|   `es`    |   Neurale|
|Zweeds|   `sv`    |Neurale|
|Tahitian|  `ty`    |Statistische|
|Tamil| `ta`    |   Neurale|
|Telugu|    `te`    |   Neurale|
|Thai|  `th`    |   Neurale|
|Tongan (Tongan)|    `to`    |   Statistische|
|Turks|   `tr`        |Neurale|
|Oekraïens| `uk`    |   Neurale|
|Urdu|  `ur`    |   Statistische|
|Vietnamees|    `vi`    |   Neurale|
|Welsh| `cy`    |   Neurale|
|Yucatec Maya|  `yua`   |   Statistische|

> [!NOTE]
> Taalcode `pt` wordt `pt-br`standaard ingesteld op Portugees (Brazilië).

## <a name="transliteration"></a>Transliteratie

De transliteratemethode ondersteunt de volgende talen. In de "Aan/Van", "<-->" geeft aan dat de taal kan worden getransliteerd van of naar een van de genoemde scripts. De "-->" geeft aan dat de taal alleen kan worden getranslitereerd van het ene script naar het andere.

| Taal    | Taalcode | Script | Naar/van | Script|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Arabisch | `ar` | Arabisch`Arab` | <--> | Latijnse`Latn` |
|Bangla  | `bn` | Bengaals`Beng` | <--> | Latijnse`Latn` |
| Chinees (Vereenvoudigd) | `zh-Hans` | Vereenvoudigd Chinees`Hans`| <--> | Latijnse`Latn` |
| Chinees (Vereenvoudigd) | `zh-Hans` | Vereenvoudigd Chinees`Hans`| <--> | Traditioneel Chinees`Hant`|
| Chinees (Traditioneel) | `zh-Hant` | Traditioneel Chinees`Hant`| <--> | Latijnse`Latn` |
| Chinees (Traditioneel) | `zh-Hant` | Traditioneel Chinees`Hant`| <--> | Vereenvoudigd Chinees`Hans` |
| Gujarati | `gu`  | Gujarati`Gujr` | --> | Latijnse`Latn` |
| Hebreeuws | `he` | Hebreeuws`Hebr` | <--> | Latijnse`Latn` |
| Hindi | `hi` | Devanagari`Deva` | <--> | Latijnse`Latn` |
| Japans | `ja` | Japans`Jpan` | <--> | Latijnse`Latn` |
| Kannada | `kn` | Kannada`Knda` | --> | Latijnse`Latn` |
| Malajalam | `ml` | Malajalam`Mlym` | --> | Latijnse`Latn` |
| Marathi | `mr` | Devanagari`Deva` | --> | Latijnse`Latn` |
| Oriya | `or` | Oriya`Orya` | <--> | Latijnse`Latn` |
| Punjabi | `pa` | Gurmukhi Gurmukhi`Guru`  | <--> | Latijnse`Latn`  |
| Servisch (Cyrillisch) | `sr-Cyrl` | Cyrillisch`Cyrl`  | --> | Latijnse`Latn` |
| Servisch (Latijns) | `sr-Latn` | Latijnse`Latn` | --> | Cyrillisch`Cyrl`|
| Tamil | `ta` | Tamil`Taml` | --> | Latijnse`Latn` |
| Telugu | `te` | Telugu`Telu` | --> | Latijnse`Latn` |
| Thai | `th` | Thais`Thai` | --> | Latijnse`Latn` |

## <a name="dictionary"></a>Woordenlijst

Het woordenboek ondersteunt de volgende talen van of naar het Engels met behulp van de methoden Opzoeken en Voorbeelden.

| Taal    | Taalcode |
|:----------- |:-------------:|
| Afrikaans      | `af`          |
| Arabisch       | `ar`          |
| Bangla      | `bn`          |
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
| Kiswahili      | `sw`          |
| Klingon      | `tlh`          |
| Koreaans      | `ko`          |
| Lets      | `lv`          |
| Litouws      | `lt`          |
| Maleisisch      | `ms`          |
| Maltees      | `mt`          |
| Norwegian      | `nb`          |
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

Translator Text API detecteert alle talen die beschikbaar zijn voor vertaling en transliteratie.


## <a name="access-the-translator-text-api-language-list-programmatically"></a>De taallijst voor de vertalertekst-API-taal programmatisch openen

U een lijst met ondersteunde talen ophalen voor de Translator Text API v3.0 met behulp van de methode Talen. U de lijst bekijken op functie, taalcode, evenals de taalnaam in het Engels of een andere ondersteunde taal. Deze lijst wordt automatisch bijgewerkt door de Microsoft Translator-service als er nieuwe talen beschikbaar worden gesteld.

[Referentiedocumentatie voor de bewerking Talen weergeven](reference/v3-0-languages.md)

## <a name="customization"></a>Aanpassing

De volgende talen zijn beschikbaar voor aanpassing aan of vanuit het Engels met behulp van [Aangepaste vertaler.](https://aka.ms/CustomTranslator)

| Taal    | Taalcode |
|:----------- |:-------------:|
| Arabisch       | `ar`          |
| Bangla      | `bn`          |
| Bosnisch (Latijns)      | `bs`          |
| Bulgaars      | `bg`          |
| Vereenvoudigd Chinees      | `zh-Hans`          |
|Traditioneel Chinees|   `zh-Hant`   |
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
| Indonesisch|   `id`    |
| Iers | `ga`  |
| Italiaans      | `it`          |
| Japans      | `ja`          |
| Kiswahili|    `sw`    |
| Koreaans      | `ko`          |
| Lets      | `lv`          |
| Litouws      | `lt`          |
| Malagasi| `mg`    |
| Maori| `mi`  |
| Norwegian      | `nb`          |
| Perzisch      | `fa`          |
| Pools      | `pl`          |
| Portugees (Brazilië) | `pt-br` |
| Roemeens      | `ro`          |
| Russisch      | `ru`          |
| Samoan|   `sm`    |
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

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Toegang tot de lijst op de website van Microsoft Translator

Voor een snelle blik op de talen toont de Website van Microsoft Translator alle talen die worden ondersteund door de gebruikerstekst- en spraak-API's. Deze lijst bevat geen informatie van ontwikkelaars, zoals taalcodes.

[Bekijk de lijst met talen](https://www.microsoft.com/translator/languages.aspx)
