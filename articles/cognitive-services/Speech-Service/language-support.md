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
ms.openlocfilehash: 437dc18dc16e879e95ff4ec7c1a9ab7ec3f17bef
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/01/2020
ms.locfileid: "84265995"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Taal-en spraak ondersteuning voor de spraak service

Taal ondersteuning is afhankelijk van de functionaliteit van de spraak service. In de volgende tabellen vindt u een overzicht van taal ondersteuning voor service aanbiedingen voor [spraak naar tekst](#speech-to-text), [tekst naar spraak](#text-to-speech)en [spraak omzetting](#speech-translation) .

## <a name="speech-to-text"></a>Spraak naar tekst

Zowel de micro soft Speech SDK als de REST API ondersteunen de volgende talen (land instellingen). 

Om de nauw keurigheid te verbeteren, wordt aanpassing aangeboden voor een subset van de talen via het uploaden van **Audio en Transcripten met menselijke labels** of **gerelateerde tekst: zinnen**. Zie aan de [slag met Custom speech](how-to-custom-speech.md)voor meer informatie over aanpassingen.

Zie [een model verbeteren voor Custom speech](how-to-custom-speech-improve-accuracy.md#add-new-words-with-pronunciation)voor meer informatie over hoe u de uitspraak kunt verbeteren.

<!--
To get the AM and ML bits:
https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20models%3A/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Landinstelling  | Taal                          | Ondersteund | Aanpassingen                                    |
|---------|-----------------------------------|-----------|---------------------------------------------------|
| `ar-AE` | Arabisch (VAE)                      | Ja       | Nee                                                |
| `ar-BH` | Arabisch (Bahrein), modern, standaard | Yes       | Taalmodel                                    |
| `ar-EG` | Arabisch (Egypte)                    | Yes       | Taalmodel                                    |
| `ar-IL` | Arabisch (Israël)                   | Ja       | Nee                                                |
| `ar-KW` | Arabisch (Koeweit)                   | Ja       | Nee                                                |
| `ar-PS` | Arabisch (Palestijnse)                | Ja       | Nee                                                |
| `ar-QA` | Arabisch (Qatar)                    | Ja       | Nee                                                |
| `ar-SA` | Arabisch (Saoedi-Arabië)             | Ja       | Nee                                                |
| `ar-SY` | Arabisch (Syrië)                    | Yes       | Taalmodel                                    |
| `ca-ES` | Catalaans                           | Yes       | Taalmodel                                    |
| `da-DK` | Deens (Denemarken)                  | Yes       | Taalmodel                                    |
| `de-DE` | Duits (Duitsland)                  | Yes       | Akoestisch model<br>Taalmodel<br>Uitspraak van |
| `en-AU` | Engels (Australië)               | Yes       | Akoestisch model<br>Taalmodel                  |
| `en-CA` | Engels (Canada)                  | Yes       | Akoestisch model<br>Taalmodel                  |
| `en-GB` | Engels (Verenigd Koninkrijk)          | Yes       | Akoestisch model<br>Taalmodel<br>Uitspraak van |
| `en-IN` | Engels (India)                   | Yes       | Akoestisch model<br>Taalmodel                  |
| `en-NZ` | Engels (Nieuw-Zeeland)             | Yes       | Akoestisch model<br>Taalmodel                  |
| `en-US` | Engels (Verenigde Staten)           | Yes       | Akoestisch model<br>Taalmodel<br>Uitspraak van |
| `es-ES` | Spaans (Spanje)                   | Yes       | Akoestisch model<br>Taalmodel                  |
| `es-MX` | Spaans (Mexico)                  | Yes       | Akoestisch model<br>Taalmodel                  |
| `fi-FI` | Fins (Finland)                 | Yes       | Taalmodel                                    |
| `fr-CA` | Frans (Canada)                   | Yes       | Akoestisch model<br>Taalmodel                  |
| `fr-FR` | Frans (Frankrijk)                   | Yes       | Akoestisch model<br>Taalmodel<br>Uitspraak van |
| `gu-IN` | Gujarati (Indiase)                 | Yes       | Taalmodel                                    |
| `hi-IN` | Hindi (India)                     | Yes       | Akoestisch model<br>Taalmodel                  |
| `it-IT` | Italiaans (Italië)                   | Yes       | Akoestisch model<br>Taalmodel<br>Uitspraak van |
| `ja-JP` | Japans (Japan)                  | Yes       | Taalmodel                                    |
| `ko-KR` | Koreaans (Korea)                    | Yes       | Taalmodel                                    |
| `mr-IN` | Marathi (India)                   | Yes       | Taalmodel                                    |
| `nb-NO` | Noors (Bokmål) (Noor wegen)       | Yes       | Taalmodel                                    |
| `nl-NL` | Nederlands (Nederland)               | Yes       | Taalmodel                                    |
| `pl-PL` | Pools (Polen)                   | Yes       | Taalmodel                                    |
| `pt-BR` | Portugees (Brazilië)               | Yes       | Akoestisch model<br>Taalmodel<br>Uitspraak van |
| `pt-PT` | Portugees (Portugal)             | Yes       | Taalmodel                                    |
| `ru-RU` | Russisch (Rusland)                  | Yes       | Akoestisch model<br>Taalmodel                  |
| `sv-SE` | Zweeds (Zweden)                  | Yes       | Taalmodel                                    |
| `ta-IN` | Tamil (India)                     | Yes       | Taalmodel                                    |
| `te-IN` | Telugu (India)                    | Ja       | Nee                                                |
| `th-TH` | Thai (Thailand)                   | Ja       | Nee                                                |
| `tr-TR` | Turks (Turkije)                  | Yes       | Taalmodel                                    |
| `zh-CN` | Chinees (Mandarijn, vereenvoudigd)    | Yes       | Akoestisch model<br>Taalmodel                  |
| `zh-HK` | Chinees (Kantonees, traditioneel)  | Yes       | Taalmodel                                    |
| `zh-TW` | Chinees (Taiwan Mandarijn)      | Yes       | Taalmodel                                    |

## <a name="text-to-speech"></a>Tekst naar spraak

Zowel de micro soft Speech SDK als REST Api's ondersteunen deze stemmen, die elk een specifieke taal en dialect ondersteunt, geïdentificeerd door land instellingen. U kunt ook een volledige lijst met talen en stemmen verkrijgen die worden ondersteund voor elke specifieke regio/elk eind punt via de [stemmen/List-API](rest-text-to-speech.md#get-a-list-of-voices). 

> [!IMPORTANT]
> De prijzen zijn afhankelijk van de standaard, aangepaste en Neural stemmen. Ga naar de pagina met [prijzen](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) voor meer informatie.

### <a name="neural-voices"></a>Neural stemmen

Neural text-to-speech is een nieuw type spraak synthese dat wordt aangedreven door diepe Neural-netwerken. Wanneer u een Neural-stem gebruikt, is de gesynthesizerde spraak bijna niet van de menselijke opnamen onderscheiden.

Neural stemmen kunnen worden gebruikt om interacties te maken met chat bots uitbreiden en spraak assistenten die natuurlijk en aantrekkelijker zijn, en om digitale teksten, zoals e-books, te converteren naar Audiobooks en in-car navigatie systemen te verbeteren. Met het menselijke net zoals natuurlijke prosody en heldere afbakening van woorden, verlaagt Neural stemmen aanzienlijk op het Luis teren van vermoeidheid wanneer gebruikers met AI-systemen communiceren.

Zie [regio's](regions.md#standard-and-neural-voices)voor meer informatie over regionale Beschik baarheid.

|Landinstelling  | Taal            | Geslacht | Spraak naam | Stijl ondersteuning |
|--|--|--|--|--|
| `de-DE` | Duits (Duitsland)                | `Female` | "de-DE-KatjaNeural"      | Algemeen |
| `en-AU` | Engels (Australië)             | `Female` | "en-AU-NatashaNeural"    | Algemeen |
| `en-CA` | Engels (Canada)                | `Female` | "en-CA-ClaraNeural"      | Algemeen |
| `en-GB` | Engels (UK)                    | `Female` | "en-GB-LibbyNeural"      | Algemeen |
|         |                                 | `Female` | "en-GB-MiaNeural"        | Algemeen |
| `en-US` | Engels (VS)                    | `Female` | "en-US-AriaNeural"       | Algemeen, meerdere spraak stijlen beschikbaar |
|         |                                 | `Male`   | "en-US-GuyNeural"        | Algemeen |
| `es-ES` | Spaans (Spanje)                 | `Female` | "es-ES-ElviraNeural"     | Algemeen |
| `es-MX` | Spaans (Mexico)                | `Female` | "es-MX-DaliaNeural"      | Algemeen |
| `fr-CA` | Frans (Canada)                 | `Female` | "FR-CA-SylvieNeural"     | Algemeen |
| `fr-FR` | Frans (Frankrijk)                 | `Female` | "fr-FR-DeniseNeural"     | Algemeen |
| `it-IT` | Italiaans (Italië)                 | `Female` | ' it-IT-ElsaNeural '       | Algemeen |
| `ja-JP` | Japans                        | `Female` | "ja-JP-NanamiNeural"     | Algemeen |
| `ko-KR` | Koreaans                          | `Female` | "ko-KR-SunHiNeural"      | Algemeen |
| `nb-NO` | Noors                       | `Female` | "nb-NO-IselinNeural"     | Algemeen |
| `pt-BR` | Portugees (Brazilië)             | `Female` | "pt-BR-FranciscaNeural"  | Algemeen |
| `tr-TR` | Turks                         | `Female` | "tr-TR-EmelNeural"       | Algemeen |
| `zh-CN` | Chinees (Mandarijn, vereenvoudigd)  | `Female` | "zh-CN-XiaoxiaoNeural"   | Algemeen, meerdere spraak stijlen beschikbaar |
|         |                                 | `Female` | "zh-CN-XiaoyouNeural"    | Kid Voice, geoptimaliseerd voor het opnemen van tekst in een verhaal |
|         |                                 | `Male`   | "zh-CN-YunyangNeural"    | Geoptimaliseerd voor nieuws lezen, meerdere spraak stijlen beschikbaar |
|         |                                 | `Male`   | "zh-CN-YunyeNeural"      | Geoptimaliseerd voor het opnemen van tekst in een verhaal |

> [!IMPORTANT]
> De `en-US-JessaNeural` stem is gewijzigd in `en-US-AriaNeural` . Als u ' Jessa ' eerder gebruikt, converteer dan naar ' Aria '.

Zie voor meer informatie over het configureren en aanpassen van Neural stemmen de taal voor de [opmaak van spraak synthese](speech-synthesis-markup.md#adjust-speaking-styles).

> [!TIP]
> U kunt de volledige toewijzing van de service naam blijven gebruiken als ' micro soft server Speech Text to Speech Voice (nl-nl, AriaNeural) ' in uw spraakherkennings aanvragen.

### <a name="standard-voices"></a>Standaard stemmen

Meer dan 75 standaard stemmen zijn beschikbaar in meer dan 45 talen en land instellingen, waarmee u tekst kunt converteren naar gesynthesizerde spraak. Zie [regio's](regions.md#standard-and-neural-voices)voor meer informatie over regionale Beschik baarheid.

| Landinstelling | Taal | Geslacht | Spraak naam |
|--|--|--|--|
| <sup>i</sup>`ar-EG` | Arabisch (Egypte) | `Female` | "ar-EG-Hoda" |
| `ar-SA` | Arabisch (Saoedi-Arabië) | `Male` | "AR-SA-Naayf" |
| `bg-BG` | Bulgaars | `Male` |  "bg-BG-Ivan" |
| `ca-ES` | Catalaans | `Female` |  "ca-ES-HerenaRUS" |
| `cs-CZ` | Tsjechisch | `Male` | "CS-CZ-Jakub" |
| `da-DK` | Deens | `Female` |  "da-DK-HelleRUS" |
| `de-AT` | Duits (Oostenrijk) | `Male` | "de-Michael" |
| `de-CH` | Duits (Zwitserland) | `Male` |  "de-CH-Karsten" |
| `de-DE` | Duits (Duitsland) | `Female` |  "de-DE-Hedda" |
|  |  | `Female` | "de-DE-HeddaRUS" |
|  |  | `Male` |  "de-DE-Stefan-Apollo" |
| `el-GR` | Grieks | `Male` | "El-GR-Stefanos" |
| `en-AU` | Engels (Australië) | `Female` |  "en-AU-Catherine" |
|  |  | `Female` |  "en-AU-HayleyRUS" |
| `en-CA` | Engels (Canada) | `Female` |  "en-CA-Linda" |
|  |  | `Female` |  "en-CA-HeatherRUS" |
| `en-GB` | Engels (UK) | `Female` |  "en-GB-Susan-Apollo" |
|  |  | `Female` |  "en-GB-HazelRUS" |
|  |  | `Male` |  "en-GB-George-Apollo" |
| `en-IE` | Engels (Ierland) | `Male` | "en-IE-Johan" |
| `en-IN` | Engels (India) | `Female` | "en-IN-heera-Apollo" |
|  |  | `Female` |  "en-IN-PriyaRUS" |
|  |  | `Male` |  "en-IN-Ravi-Apollo" |
| `en-US` | Engels (VS) | `Female` |  "en-US-ZiraRUS" |
|  |  | `Female` | "en-US-AriaRUS" |
|  |  | `Male` | "en-US-BenjaminRUS" |
|  |  | `Male` |  "en-US-Guy24kRUS" |
| `es-ES` | Spaans (Spanje) | `Female` |  "es-ES-Laura-Apollo" |
|  |  | `Female` | "es-ES-HelenaRUS" |
|  |  | `Male` | "es-ES-Pablo-Apollo" |
| `es-MX` | Spaans (Mexico) | `Female` |  "es-MX-HildaRUS" |
|  |  | `Male` | "es-MX-Raul-Apollo" |
| `fi-FI` | Fins | `Female` | "fi-FI-HeidiRUS" |
| `fr-CA` | Frans (Canada) | `Female` | "FR-CA-Caroline" |
|  |  | `Female` | "FR-CA-HarmonieRUS" |
| `fr-CH` | Frans (Zwitserland) | `Male` | "FR-CH-Guillaume" |
| `fr-FR` | Frans (Frankrijk) | `Female` |  "fr-FR-Pascaline-Apollo" |
|  |  | `Female` |"fr-FR-HortenseRUS" |
|  |  | `Male` |  "fr-FR-Paul-Apollo" |
| `he-IL` | Hebreeuws (Israël) | `Male` |  "he IL-Asaf" |
| `hi-IN` | Hindi (India) | `Female` | "Hi-IN-Kalpana-Apollo" |
|  |  | `Female` |  "Hi-IN-Kalpana" |
|  |  | `Male` |  "Hi-IN-Hemant" |
| `hr-HR` | Kroatisch | `Male` | "HR-HR-Matej" |
| `hu-HU` | Hongaars | `Male` |  "hu-HU-Szabolcs" |
| `id-ID` | Indonesisch | `Male` | "id-ID-andika" |
| `it-IT` | Italiaans | `Male` |  "it-IT-Cosimo-Apollo" |
|  |  | `Female` |  ' it-IT-LuciaRUS ' |
| `ja-JP` | Japans | `Female` |  "ja-JP-Ayumi-Apollo" |
|  |  | `Male` | "ja-JP-Ichiro-Apollo" |
|  |  | `Female` |  "ja-JP-HarukaRUS" |
| `ko-KR` | Koreaans | `Female` | "ko-KR-HeamiRUS" |
| `ms-MY` | Maleisisch | `Male` |  ' MS-MY-Rizwan ' |
| `nb-NO` | Noors | `Female` |  "nb-NO-HuldaRUS" |
| `nl-NL` | Nederlands | `Female` |  "nl-NL-HannaRUS" |
| `pl-PL` | Pools | `Female` |  "pl-PL-PaulinaRUS" |
| `pt-BR` | Portugees (Brazilië) | `Female` | "pt-BR-HeloisaRUS" |
|  |  | `Male` |  "pt-BR-Apollo" |
| `pt-PT` | Portugees (Portugal) | `Female` | "pt-PT-HeliaRUS" |
| `ro-RO` | Roemeens | `Male` | "ro-RO-Andrei" |
| `ru-RU` | Russisch | `Female` |  "ru-RU-Irina-Apollo" |
|  |  | `Male` | "ru-RU-Pavel-Apollo" |
|  |  | `Female` |  ru-RU-EkaterinaRUS |
| `sk-SK` | Slowaaks | `Male` | "sk-SK-Filip" |
| `sl-SI` | Sloveens | `Male` |  "sl-SI-lado" |
| `sv-SE` | Zweeds | `Female` | "sv-SE-HedvigRUS" |
| `ta-IN` | Tamil (India) | `Male` |  "TA-IN-Valluvar" |
| `te-IN` | Telugu (India) | `Female` |  "te-IN-Chitra" |
| `th-TH` | Thai | `Male` |  ' th-do-Pattara ' |
| `tr-TR` | Turks (Turkije) | `Female` | "tr-TR-SedaRUS" |
| `vi-VN` | Vietnamees | `Male` |  "VI-VN-a" |
| `zh-CN` | Chinees (Mandarijn, vereenvoudigd) | `Female` |  "zh-CN-HuihuiRUS" |
|  |  | `Female` | "zh-CN-Yaoyao-Apollo" |
|  |  | `Male` | "zh-CN-Kangkang-Apollo" |
| `zh-HK` | Chinees (Kantonees, traditioneel) | `Female` |  "zh-HK-Tracy-Apollo" |
|  |  | `Female` | "zh-HK-TracyRUS" |
|  |  | `Male` |  "zh-HK-Danny-Apollo" |
| `zh-TW` | Chinees (Taiwan Mandarijn) | `Female` |  "zh-TW-Yating-Apollo" |
|  |  | `Female` | "zh-TW-HanHanRUS" |
|  |  | `Male` |  "zh-TW-Zhiwei-Apollo" |

**1** *AR-voor beeld: ondersteuning voor modern Standard-Arabisch (MSA).*

> [!IMPORTANT]
> De `en-US-Jessa` stem is gewijzigd in `en-US-Aria` . Als u ' Jessa ' eerder gebruikt, converteer dan naar ' Aria '.

> [!TIP]
> U kunt de volledige toewijzing van de service naam blijven gebruiken als ' micro soft server Speech Text to Speech Voice (nl-nl, AriaRUS) ' in uw spraakherkennings aanvragen.

### <a name="customization"></a>Aanpassing

Stem aanpassing is beschikbaar voor,,,,,, `de-DE` `en-GB` , en `en-IN` `en-US` `es-MX` `fr-FR` `it-IT` `pt-BR` `zh-CN` . Selecteer de juiste land instelling die overeenkomt met de trainings gegevens die u nodig hebt om een aangepast spraak model te trainen. Als de opname gegevens die u hebt gesp roken in het Engels met een Britse accent, selecteert u bijvoorbeeld `en-GB` .

> [!NOTE]
> We bieden geen ondersteuning voor bidirectionele model trainingen in aangepaste spraak, met uitzonde ring van het Chinese Engelse bi-taal gebruik. Selecteer ' Chinees-Engels-tweetalig ' als u een Chinese stem wilt trainen die ook Engels kan spreken. Spraak training in alle land instellingen begint met een gegevensset van 2000 + uitingen, met uitzonde ring van de `en-US` en `zh-CN` waar u kunt beginnen met elke grootte van de trainings gegevens.

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
| Noors               | `nb`          |
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
