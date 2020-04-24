---
title: Taal ondersteuning-spraak service
titleSuffix: Azure Cognitive Services
description: De spraak service ondersteunt talloze talen voor conversie van spraak naar tekst en tekst naar spraak, samen met spraak omzetting. In dit artikel vindt u een uitgebreide lijst met taal ondersteuning per service functie.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: trbye
ms.custom: seodec18
ms.openlocfilehash: 0b6fea381bd6b4aa8ad3e7061e6f632176c41033
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82113830"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Taal-en spraak ondersteuning voor de spraak service

Taal ondersteuning is afhankelijk van de functionaliteit van de spraak service. In de volgende tabellen vindt u een overzicht van taal ondersteuning voor service aanbiedingen voor [spraak naar tekst](#speech-to-text), [tekst naar spraak](#text-to-speech)en [spraak omzetting](#speech-translation) .

## <a name="speech-to-text"></a>Spraak naar tekst

Zowel de micro soft Speech SDK als de REST API ondersteunen de volgende talen (land instellingen). Om de nauw keurigheid te verbeteren, wordt aanpassing aangeboden voor een subset van de talen via het uploaden van audio en Transcripten met menselijke labels of gerelateerde tekst: zinnen. Het aanpassen van de uitspraak is momenteel alleen `en-US` beschikbaar `de-DE`voor en. Meer informatie over aanpassingen [vindt u hier](how-to-custom-speech.md).

<!--
To get the AM and ML bits:
https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20models%3A/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Landinstelling  | Taal                          | Ondersteund | Aanpassingen                                    |
|---------|-----------------------------------|-----------|---------------------------------------------------|
| `ar-AE` | Arabisch (VAE)                      | Ja       | Nee                                                |
| `ar-BH` | Arabisch (Bahrein), modern, standaard | Ja       | Taalmodel                                    |
| `ar-EG` | Arabisch (Egypte)                    | Ja       | Taalmodel                                    |
| `ar-KW` | Arabisch (Koeweit)                   | Ja       | Nee                                                |
| `ar-QA` | Arabisch (Qatar)                    | Ja       | Nee                                                |
| `ar-SA` | Arabisch (Saudi-Arabië)             | Ja       | Nee                                                |
| `ar-SY` | Arabisch (Syrië)                    | Ja       | Taalmodel                                    |
| `ca-ES` | Catalaans                           | Ja       | Taalmodel                                    |
| `da-DK` | Deens (Denemarken)                  | Ja       | Taalmodel                                    |
| `de-DE` | Duits (Duitsland)                  | Ja       | Akoestisch model<br>Taalmodel<br>Uitspraak van |
| `en-AU` | Engels (Australië)               | Ja       | Akoestisch model<br>Taalmodel                  |
| `en-CA` | Engels (Canada)                  | Ja       | Akoestisch model<br>Taalmodel                  |
| `en-GB` | Engels (Verenigd Koninkrijk)          | Ja       | Akoestisch model<br>Taalmodel<br>Uitspraak van |
| `en-IN` | Engels (India)                   | Ja       | Akoestisch model<br>Taalmodel                  |
| `en-NZ` | Engels (Nieuw-Zeeland)             | Ja       | Akoestisch model<br>Taalmodel                  |
| `en-US` | Engels (Verenigde Staten)           | Ja       | Akoestisch model<br>Taalmodel<br>Uitspraak van |
| `es-ES` | Spaans (Spanje)                   | Ja       | Akoestisch model<br>Taalmodel                  |
| `es-MX` | Spaans (Mexico)                  | Ja       | Akoestisch model<br>Taalmodel                  |
| `fi-FI` | Fins (Finland)                 | Ja       | Taalmodel                                    |
| `fr-CA` | Frans (Canada)                   | Ja       | Akoestisch model<br>Taalmodel                  |
| `fr-FR` | Frans (Frankrijk)                   | Ja       | Akoestisch model<br>Taalmodel<br>Uitspraak van |
| `gu-IN` | Gujarati (Indiase)                 | Ja       | Taalmodel                                    |
| `hi-IN` | Hindi (India)                     | Ja       | Akoestisch model<br>Taalmodel                  |
| `it-IT` | Italiaans (Italië)                   | Ja       | Akoestisch model<br>Taalmodel<br>Uitspraak van |
| `ja-JP` | Japans (Japan)                  | Ja       | Taalmodel                                    |
| `ko-KR` | Koreaans (Korea)                    | Ja       | Taalmodel                                    |
| `mr-IN` | Marathi (India)                   | Ja       | Taalmodel                                    |
| `nb-NO` | Noors (Bokmål) (Noor wegen)       | Ja       | Taalmodel                                    |
| `nl-NL` | Nederlands (Nederland)               | Ja       | Taalmodel                                    |
| `pl-PL` | Pools (Polen)                   | Ja       | Taalmodel                                    |
| `pt-BR` | Portugees (Brazilië)               | Ja       | Akoestisch model<br>Taalmodel<br>Uitspraak van |
| `pt-PT` | Portugees (Portugal)             | Ja       | Taalmodel                                    |
| `ru-RU` | Russisch (Rusland)                  | Ja       | Akoestisch model<br>Taalmodel                  |
| `sv-SE` | Zweeds (Zweden)                  | Ja       | Taalmodel                                    |
| `ta-IN` | Tamil (India)                     | Ja       | Taalmodel                                    |
| `te-IN` | Telugu (India)                    | Ja       | Nee                                                |
| `th-TH` | Thai (Thailand)                   | Ja       | Nee                                                |
| `tr-TR` | Turks (Turkije)                  | Ja       | Nee                                                |
| `zh-CN` | Chinees (Mandarijn, vereenvoudigd)    | Ja       | Akoestisch model<br>Taalmodel                  |
| `zh-HK` | Chinees (Kantonees, traditioneel)  | Ja       | Taalmodel                                    |
| `zh-TW` | Chinees (Taiwan Mandarijn)      | Ja       | Taalmodel                                    |

## <a name="text-to-speech"></a>Tekst naar spraak

Zowel de micro soft Speech SDK als REST Api's ondersteunen deze stemmen, die elk een specifieke taal en dialect ondersteunt, geïdentificeerd door land instellingen.

> [!IMPORTANT]
> De prijzen zijn afhankelijk van de standaard, aangepaste en Neural stemmen. Ga naar de pagina met [prijzen](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) voor meer informatie.

### <a name="neural-voices"></a>Neural stemmen

Neural text-to-speech is een nieuw type spraak synthese dat wordt aangedreven door diepe Neural-netwerken. Wanneer u een Neural-stem gebruikt, is de gesynthesizerde spraak bijna niet van de menselijke opnamen onderscheiden.

Neural stemmen kunnen worden gebruikt om interacties te maken met chat bots uitbreiden en spraak assistenten die natuurlijk en aantrekkelijker zijn, en om digitale teksten, zoals e-books, te converteren naar Audiobooks en in-car navigatie systemen te verbeteren. Met het menselijke net zoals natuurlijke prosody en heldere afbakening van woorden, verlaagt Neural stemmen aanzienlijk op het Luis teren van vermoeidheid wanneer gebruikers met AI-systemen communiceren.

Zie [regio's](regions.md#standard-and-neural-voices)voor meer informatie over regionale Beschik baarheid.

| Landinstelling  | Taal            | Geslacht | Volledige toewijzing van service naam                                               | Korte spraak naam        |
|---------|---------------------|--------|-------------------------------------------------------------------------|-------------------------|
| `de-DE` | Duits (Duitsland)    | Vrouw | "Micro soft server Speech Text to Speech Voice (de-DE, KatjaNeural)"     | "de-DE-KatjaNeural"     |
| `en-US` | Engels (VS)        | Vrouw | "Micro soft server Speech Text to Speech Voice (nl-nl, AriaNeural)"      | "en-US-AriaNeural"      |
| `en-US` | Engels (VS)        | Man   | "Micro soft server Speech Text to Speech Voice (nl-nl, GuyNeural)"       | "en-US-GuyNeural"       |
| `it-IT` | Italiaans (Italië)     | Vrouw | "Micro soft server Speech Text to Speech Voice (IT-IT, ElsaNeural)"      | ' it-IT-ElsaNeural '      |
| `pt-BR` | Portugees (Brazilië) | Vrouw | "Micro soft server Speech Text to Speech Voice (pt-BR, FranciscaNeural)" | "pt-BR-FranciscaNeural" |
| `zh-CN` | Chinees (Mandarijn, vereenvoudigd)  | Vrouw | "Micro soft server Speech Text to Speech Voice (zh-CN, XiaoxiaoNeural)"  | "zh-CN-XiaoxiaoNeural"  |

> [!IMPORTANT]
> De `en-US-JessaNeural` stem is gewijzigd in `en-US-AriaNeural`. Als u ' Jessa ' eerder gebruikt, converteer dan naar ' Aria '.

Zie voor meer informatie over het configureren en aanpassen van Neural stemmen de taal voor de [opmaak van spraak synthese](speech-synthesis-markup.md#adjust-speaking-styles).

> [!TIP]
> U kunt de volledige service naam toewijzing of de naam van de korte spraak gebruiken in uw verzoeken voor spraak synthese.

### <a name="standard-voices"></a>Standaard stemmen

Meer dan 75 standaard stemmen zijn beschikbaar in meer dan 45 talen en land instellingen, waarmee u tekst kunt converteren naar gesynthesizerde spraak. Zie [regio's](regions.md#standard-and-neural-voices)voor meer informatie over regionale Beschik baarheid.

| Landinstelling | Taal | Geslacht | Volledige toewijzing van service naam | Korte naam |
|--|--|--|--|--|
| <sup>i</sup>`ar-EG` | Arabisch (Egypte) | Vrouw | "Micro soft server Speech Text to Speech Voice (AR-BIJV. Hoda)" | "ar-EG-Hoda" |
| `ar-SA` | Arabisch (Saudi-Arabië) | Man | "Micro soft server Speech Text to Speech Voice (AR-SA, Naayf)" | "AR-SA-Naayf" |
| `bg-BG` | Bulgaars | Man | "Micro soft server Speech Text to Speech Voice (bg-BG, Ivan)" | "bg-BG-Ivan" |
| `ca-ES` | Catalaans (Spanje) | Vrouw | "Micro soft server Speech Text to Speech Voice (ca-ES, HerenaRUS)" | "ca-ES-HerenaRUS" |
| `cs-CZ` | Tsjechisch | Man | "Micro soft server Speech Text to Speech Voice (CS-CZ, Jakub)" | "CS-CZ-Jakub" |
| `da-DK` | Deens | Vrouw | "Micro soft server Speech Text to Speech Voice (da-DK, HelleRUS)" | "da-DK-HelleRUS" |
| `de-AT` | Duits (Oostenrijk) | Man | "Micro soft server Speech Text to Speech Voice (de-AT, Michael)" | "de-Michael" |
| `de-CH` | Duits (Zwitserland) | Man | "Micro soft server Speech Text to Speech Voice (de-CH, Karsten)" | "de-CH-Karsten" |
| `de-DE` | Duits (Duitsland) | Vrouw | "Micro soft server Speech Text to Speech Voice (de-DE, Hedda)" | "de-DE-Hedda" |
|  |  | Vrouw | "Micro soft server Speech Text to Speech Voice (de-DE, HeddaRUS)" | "de-DE-HeddaRUS" |
|  |  | Man | "Micro soft server Speech Text to Speech Voice (de-DE, Stefan, Apollo)" | "de-DE-Stefan-Apollo" |
| `el-GR` | Grieks | Man | "Micro soft server Speech Text to Speech Voice (El-GR, Stefanos)" | "El-GR-Stefanos" |
| `en-AU` | Engels (Australië) | Vrouw | "Micro soft server Speech Text to Speech Voice (en-AU, Catherine)" | "en-AU-Catherine" |
|  |  | Vrouw | "Micro soft server Speech Text to Speech Voice (en-AU, HayleyRUS)" | "en-AU-HayleyRUS" |
| `en-CA` | Engels (Canada) | Vrouw | "Micro soft server Speech Text to Speech Voice (en-CA, Linda)" | "en-CA-Linda" |
|  |  | Vrouw | "Micro soft server Speech Text to Speech Voice (en-CA, HeatherRUS)" | "en-CA-HeatherRUS" |
| `en-GB` | Engels (UK) | Vrouw | "Micro soft server Speech Text to Speech Voice (en-GB, Susan, Apollo)" | "en-GB-Susan-Apollo" |
|  |  | Vrouw | "Micro soft server Speech Text to Speech Voice (en-GB, HazelRUS)" | "en-GB-HazelRUS" |
|  |  | Man | "Micro soft server Speech Text to Speech Voice (en-GB, George, Apollo)" | "en-GB-George-Apollo" |
| `en-IE` | Engels (Ierland) | Man | "Micro soft server Speech Text to Speech Voice (en-IE, Johan)" | "en-IE-Johan" |
| `en-IN` | Engels (India) | Vrouw | "Micro soft server Speech Text to Speech Voice (en-IN, heera, Apollo)" | "en-IN-heera-Apollo" |
|  |  | Vrouw | "Micro soft server Speech Text to Speech Voice (en-IN, PriyaRUS)" | "en-IN-PriyaRUS" |
|  |  | Man | "Micro soft server Speech Text to Speech Voice (en-IN, Ravi, Apollo)" | "en-IN-Ravi-Apollo" |
| `en-US` | Engels (VS) | Vrouw | "Micro soft server Speech Text to Speech Voice (nl-nl, ZiraRUS)" | "en-US-ZiraRUS" |
|  |  | Vrouw | "Micro soft server Speech Text to Speech Voice (nl-nl, AriaRUS)" | "en-US-AriaRUS" |
|  |  | Man | "Micro soft server Speech Text to Speech Voice (nl-nl, BenjaminRUS)" | "en-US-BenjaminRUS" |
|  |  | Man | "Micro soft server Speech Text to Speech Voice (nl-nl, Guy24kRUS)" | "en-US-Guy24kRUS" |
| `es-ES` | Spaans (Spanje) | Vrouw | "Micro soft server Speech Text to Speech Voice (es-ES, Laura, Apollo)" | "es-ES-Laura-Apollo" |
|  |  | Vrouw | "Micro soft server Speech Text to Speech Voice (es-ES, HelenaRUS)" | "es-ES-HelenaRUS" |
|  |  | Man | "Micro soft server Speech Text to Speech Voice (es-ES, Pablo, Apollo)" | "es-ES-Pablo-Apollo" |
| `es-MX` | Spaans (Mexico) | Vrouw | "Micro soft server Speech Text to Speech Voice (es-MX, HildaRUS)" | "es-MX-HildaRUS" |
|  |  | Man | "Micro soft server Speech Text to Speech Voice (es-MX, Raul, Apollo)" | "es-MX-Raul-Apollo" |
| `fi-FI` | Fins | Vrouw | "Micro soft server Speech Text to Speech Voice (fi-FI, HeidiRUS)" | "fi-FI-HeidiRUS" |
| `fr-CA` | Frans (Canada) | Vrouw | "Micro soft server Speech Text to Speech Voice (FR-CA, Caroline)" | "FR-CA-Caroline" |
|  |  | Vrouw | "Micro soft server Speech Text to Speech Voice (FR-CA, HarmonieRUS)" | "FR-CA-HarmonieRUS" |
| `fr-CH` | Frans (Zwitserland) | Man | "Micro soft server Speech Text to Speech Voice (FR-CH, Guillaume)" | "FR-CH-Guillaume" |
| `fr-FR` | Frans (Frankrijk) | Vrouw | "Micro soft server Speech Text to Speech Voice (fr-FR, Julie, Apollo)" | "fr-FR-Pascaline-Apollo" |
|  |  | Vrouw | "Micro soft server Speech Text to Speech Voice (fr-FR, HortenseRUS)" | "fr-FR-HortenseRUS" |
|  |  | Man | "Micro soft server Speech Text to Speech Voice (fr-FR, Paul, Apollo)" | "fr-FR-Paul-Apollo" |
| `he-IL` | Hebreeuws (Israël) | Man | "Micro soft server Speech Text to Speech Voice (ik-IL, Asaf)" | "he IL-Asaf" |
| `hi-IN` | Hindi (India) | Vrouw | "Micro soft server Speech Text to Speech Voice (Hi-IN, Kalpana, Apollo)" | "Hi-IN-Kalpana-Apollo" |
|  |  | Vrouw | "Micro soft server Speech Text to Speech Voice (Hi-IN, Kalpana)" | "Hi-IN-Kalpana" |
|  |  | Man | "Micro soft server Speech Text to Speech Voice (Hi-IN, Hemant)" | "Hi-IN-Hemant" |
| `hr-HR` | Kroatisch | Man | "Micro soft server Speech Text to Speech Voice (HR-HR, Matej)" | "HR-HR-Matej" |
| `hu-HU` | Hongaars | Man | "Micro soft server Speech Text to Speech Voice (hu-HU, Szabolcs)" | "hu-HU-Szabolcs" |
| `id-ID` | Indonesisch | Man | "Micro soft server Speech Text to Speech Voice (id-ID, andika)" | "id-ID-andika" |
| `it-IT` | Italiaans | Man | "Micro soft server Speech Text to Speech Voice (IT-IT, Cosimo, Apollo)" | "it-IT-Cosimo-Apollo" |
|  |  | Vrouw | "Micro soft server Speech Text to Speech Voice (IT-IT, LuciaRUS)" | ' it-IT-LuciaRUS ' |
| `ja-JP` | Japans | Vrouw | "Micro soft server Speech Text to Speech Voice (ja-JP, Ayumi, Apollo)" | "ja-JP-Ayumi-Apollo" |
|  |  | Man | "Micro soft server Speech Text to Speech Voice (ja-JP, Ichiro, Apollo)" | "ja-JP-Ichiro-Apollo" |
|  |  | Vrouw | "Micro soft server Speech Text to Speech Voice (ja-JP, HarukaRUS)" | "ja-JP-HarukaRUS" |
| `ko-KR` | Koreaans | Vrouw | "Micro soft server Speech Text to Speech Voice (ko-KR, HeamiRUS)" | "ko-KR-HeamiRUS" |
| `ms-MY` | Maleisisch | Man | "Micro soft server Speech Text to Speech Voice (MS-MY, Rizwan)" | ' MS-MY-Rizwan ' |
| `nb-NO` | Norwegian | Vrouw | "Micro soft server Speech Text to Speech Voice (nb-NO, HuldaRUS)" | "nb-NO-HuldaRUS" |
| `nl-NL` | Nederlands | Vrouw | "Micro soft server Speech Text to Speech Voice (nl-NL, HannaRUS)" | "nl-NL-HannaRUS" |
| `pl-PL` | Pools | Vrouw | "Micro soft server Speech Text to Speech Voice (pl-PL, PaulinaRUS)" | "pl-PL-PaulinaRUS" |
| `pt-BR` | Portugees (Brazilië) | Vrouw | "Micro soft server Speech Text to Speech Voice (pt-BR, HeloisaRUS)" | "pt-BR-HeloisaRUS" |
|  |  | Man | "Micro soft server Speech Text to Speech Voice (pt-BR, Apollo)" | "pt-BR-Apollo" |
| `pt-PT` | Portugees (Portugal) | Vrouw | "Micro soft server Speech Text to Speech Voice (pt-PT, HeliaRUS)" | "pt-PT-HeliaRUS" |
| `ro-RO` | Roemeens | Man | "Micro soft server Speech Text to Speech Voice (RO-RO, Andrei)" | "ro-RO-Andrei" |
| `ru-RU` | Russisch | Vrouw | "Micro soft server Speech Text to Speech Voice (ru-RU, Irina, Apollo)" | "ru-RU-Irina-Apollo" |
|  |  | Man | "Micro soft server Speech Text to Speech Voice (ru-RU, Pavel, Apollo)" | "ru-RU-Pavel-Apollo" |
|  |  | Vrouw | "Micro soft server Speech Text to Speech Voice (ru-RU, EkaterinaRUS)" | ru-RU-EkaterinaRUS |
| `sk-SK` | Slowaaks | Man | "Micro soft server Speech Text to Speech Voice (sk-SK, Filip)" | "sk-SK-Filip" |
| `sl-SI` | Sloveens | Man | "Micro soft server Speech Text to Speech Voice (sl-SI, lado)" | "sl-SI-lado" |
| `sv-SE` | Zweeds | Vrouw | "Micro soft server Speech Text to Speech Voice (sv-SE, HedvigRUS)" | "sv-SE-HedvigRUS" |
| `ta-IN` | Tamil (India) | Man | "Micro soft server Speech Text to Speech Voice (TA-IN, Valluvar)" | "TA-IN-Valluvar" |
| `te-IN` | Telugu (India) | Vrouw | "Micro soft server Speech Text to Speech Voice (Chitra)" | "te-IN-Chitra" |
| `th-TH` | Thai | Man | "Micro soft server Speech Text to Speech Voice (th-TH, Pattara)" | ' th-do-Pattara ' |
| `tr-TR` | Turks (Turkije) | Vrouw | "Micro soft server Speech Text to Speech Voice (tr-TR, SedaRUS)" | "tr-TR-SedaRUS" |
| `vi-VN` | Vietnamees | Man | "Micro soft server Speech Text to Speech Voice (VI-VN, a)" | "VI-VN-a" |
| `zh-CN` | Chinees (Mandarijn, vereenvoudigd) | Vrouw | "Micro soft server Speech Text to Speech Voice (zh-CN, HuihuiRUS)" | "zh-CN-HuihuiRUS" |
|  |  | Vrouw | "Micro soft server Speech Text to Speech Voice (zh-CN, Yaoyao, Apollo)" | "zh-CN-Yaoyao-Apollo" |
|  |  | Man | "Micro soft server Speech Text to Speech Voice (zh-CN, Kangkang, Apollo)" | "zh-CN-Kangkang-Apollo" |
| `zh-HK` | Chinees (Kantonees, traditioneel) | Vrouw | "Micro soft server Speech Text to Speech Voice (zh-HK, Tracy, Apollo)" | "zh-HK-Tracy-Apollo" |
|  |  | Vrouw | "Micro soft server Speech Text to Speech Voice (zh-HK, TracyRUS)" | "zh-HK-TracyRUS" |
|  |  | Man | "Micro soft server Speech Text to Speech Voice (zh-HK, Danny, Apollo)" | "zh-HK-Danny-Apollo" |
| `zh-TW` | Chinees (Taiwan Mandarijn) | Vrouw | "Micro soft server Speech Text to Speech Voice (zh-TW, Yating, Apollo)" | "zh-TW-Yating-Apollo" |
|  |  | Vrouw | "Micro soft server Speech Text to Speech Voice (zh-TW, HanHanRUS)" | "zh-TW-HanHanRUS" |
|  |  | Man | "Micro soft server Speech Text to Speech Voice (zh-TW, Zhiwei, Apollo)" | "zh-TW-Zhiwei-Apollo" |

**1** *AR-voor beeld: ondersteuning voor modern Standard-Arabisch (MSA).*

> [!IMPORTANT]
> De `en-US-Jessa` stem is gewijzigd in `en-US-Aria`. Als u ' Jessa ' eerder gebruikt, converteer dan naar ' Aria '.

> [!TIP]
> U kunt de volledige service naam toewijzing of de naam van de korte spraak gebruiken in uw verzoeken voor spraak synthese.

### <a name="customization"></a>Aanpassing

Stem aanpassing is beschikbaar voor `de-DE`, `en-GB`, `en-IN` `en-US` `es-MX` `fr-FR`,,,, `it-IT`, `pt-BR`en `zh-CN`. Selecteer de juiste land instelling die overeenkomt met de trainings gegevens die u nodig hebt om een aangepast spraak model te trainen. Als de opname gegevens die u hebt gesp roken in het Engels met een Britse accent, selecteert `en-GB`u bijvoorbeeld.

> [!NOTE]
> We bieden geen ondersteuning voor bidirectionele model trainingen in aangepaste spraak, met uitzonde ring van het Chinese Engelse bi-taal gebruik. Selecteer ' Chinees-Engels-tweetalig ' als u een Chinese stem wilt trainen die ook Engels kan spreken. Spraak training in alle land instellingen begint met een gegevensset van 2000 + uitingen, met uitzonde `en-US` ring `zh-CN` van de en waar u kunt beginnen met elke grootte van de trainings gegevens.

## <a name="speech-translation"></a>Spraakomzetting

De API voor **spraak omzetting** ondersteunt verschillende talen voor conversie van spraak naar spraak en spraak naar tekst. De bron taal moet altijd afkomstig zijn uit de tabel met spraak-naar-tekst taal. De beschik bare doel talen zijn afhankelijk van het feit of het Vertaal doel spraak of tekst is. U kunt binnenkomende spraak omzetten in meer dan [60 talen](https://www.microsoft.com/translator/business/languages/). Er is een subset van talen beschikbaar voor [spraak synthese](language-support.md#text-languages).

### <a name="text-languages"></a>Tekst talen

| Tekst taal           | Taalcode |
|:------------------------|:-------------:|
| Afrikaans               | `af`          |
| Arabisch                  | `ar`          |
| Bengalese                  | `bn`          |
| Bosnisch (Latijns)         | `bs`          |
| Bulgaars               | `bg`          |
| Kantonees (traditioneel) | `yue`         |
| Catalaans                 | `ca`          |
| Vereenvoudigd Chinees      | `zh-Hans`     |
| Traditioneel Chinees     | `zh-Hant`     |
| Kroatisch                | `hr`          |
| Tsjechisch                   | `cs`          |
| Deens                  | `da`          |
| Nederlands                   | `nl`          |
| Engels                 | `en`          |
| Ests                | `et`          |
| Fijian                  | `fj`          |
| Filipino                | `fil`         |
| Fins                 | `fi`          |
| Frans                  | `fr`          |
| Duits                  | `de`          |
| Grieks                   | `el`          |
| Gujarati                | `gu`          |
| Haïtiaans          | `ht`          |
| Hebreeuws                  | `he`          |
| Hindi                   | `hi`          |
| Hmong Daw               | `mww`         |
| Hongaars               | `hu`          |
| Indonesisch              | `id`          |
| Iers                   | `ga`          |
| Italiaans                 | `it`          |
| Japans                | `ja`          |
| Kannada                 | `kn`          |
| Swahili               | `sw`          |
| Klingon                 | `tlh`         |
| Klingon (plqaD)         | `tlh-Qaak`    |
| Koreaans                  | `ko`          |
| Lets                 | `lv`          |
| Litouws              | `lt`          |
| Malagassische                | `mg`          |
| Maleisisch                   | `ms`          |
| Malajalam               | `ml`          |
| Maltees                 | `mt`          |
| Maori                   | `mi`          |
| Marathi                 | `mr`          |
| Norwegian               | `nb`          |
| Perzisch                 | `fa`          |
| Pools                  | `pl`          |
| Portugees (Brazilië)     | `pt-br`       |
| Portugees (Portugal)   | `pt-pt`       |
| Punjabi                 | `pa`          |
| Queretaro Otomi         | `otq`         |
| Roemeens                | `ro`          |
| Russisch                 | `ru`          |
| Samoan                  | `sm`          |
| Servisch (Cyrillisch)      | `sr-Cyrl`     |
| Servisch (Latijns)         | `sr-Latn`     |
| Slowaaks                  | `sk`          |
| Sloveens               | `sl`          |
| Spaans                 | `es`          |
| Zweeds                 | `sv`          |
| Tahitian                | `ty`          |
| Tamil                   | `ta`          |
| Telugu                  | `te`          |
| Thai                    | `th`          |
| Tongaanse                  | `to`          |
| Turks                 | `tr`          |
| Oekraïens               | `uk`          |
| Urdu                    | `ur`          |
| Vietnamees              | `vi`          |
| Welsh                   | `cy`          |
| Yucatec Maya            | `yua`         |

## <a name="next-steps"></a>Volgende stappen

* [Uw proef abonnement voor spraak services ophalen](https://azure.microsoft.com/try/cognitive-services/)
* [Zie spraak herkennen in C #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
