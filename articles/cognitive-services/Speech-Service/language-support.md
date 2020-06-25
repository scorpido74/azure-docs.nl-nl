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
ms.openlocfilehash: 23ecfc96c8f75dbae4cfb02cc16f5e5de359c4f6
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/25/2020
ms.locfileid: "85362173"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Taal-en spraak ondersteuning voor de spraak service

Taal ondersteuning is afhankelijk van de functionaliteit van de spraak service. In de volgende tabellen vindt u een overzicht van taal ondersteuning voor service aanbiedingen voor [spraak naar tekst](#speech-to-text), [tekst naar spraak](#text-to-speech)en [spraak omzetting](#speech-translation) .

## <a name="speech-to-text"></a>Spraak naar tekst

Zowel de micro soft Speech SDK als de REST API ondersteunen de volgende talen (land instellingen). 

Om de nauw keurigheid te verbeteren, wordt aanpassing aangeboden voor een subset van de talen via het uploaden van **Audio en Transcripten met menselijke labels** of **gerelateerde tekst: zinnen**. Zie aan de [slag met Custom speech](how-to-custom-speech.md)voor meer informatie over aanpassingen.

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
| `ar-IL` | Arabisch (Israël)                   | Ja       | Nee                                                |
| `ar-JO` | Arabisch (Jordanië)                   | Ja       | Nee                                                |
| `ar-KW` | Arabisch (Koeweit)                   | Ja       | Nee                                                |
| `ar-LB` | Arabisch (Libanon)                  | Ja       | Nee                                                |
| `ar-PS` | Arabisch (Palestijnse)                | Ja       | Nee                                                |
| `ar-QA` | Arabisch (Qatar)                    | Ja       | Nee                                                |
| `ar-SA` | Arabisch (Saoedi-Arabië)             | Ja       | Nee                                                |
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
| `te-IN` | Telugu (India)                    | Ja       | Taalmodel                                    |
| `th-TH` | Thai (Thailand)                   | Ja       | Nee                                                |
| `tr-TR` | Turks (Turkije)                  | Ja       | Taalmodel                                    |
| `zh-CN` | Chinees (Mandarijn, vereenvoudigd)    | Ja       | Akoestisch model<br>Taalmodel                  |
| `zh-HK` | Chinees (Kantonees, traditioneel)  | Ja       | Taalmodel                                    |
| `zh-TW` | Chinees (Taiwan Mandarijn)      | Ja       | Taalmodel                                    |

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
| `de-DE` | Duits (Duitsland)                | Vrouw | "de-DE-KatjaNeural"      | Algemeen |
| `en-AU` | Engels (Australië)             | Vrouw | "en-AU-NatashaNeural"    | Algemeen |
| `en-CA` | Engels (Canada)                | Vrouw | "en-CA-ClaraNeural"      | Algemeen |
| `en-GB` | Engels (UK)                    | Vrouw | "en-GB-LibbyNeural"      | Algemeen |
|         |                                 | Vrouw | "en-GB-MiaNeural"        | Algemeen |
| `en-US` | Engels (VS)                    | Vrouw | "en-US-AriaNeural"       | Algemeen, meerdere spraak stijlen beschikbaar |
|         |                                 | Man   | "en-US-GuyNeural"        | Algemeen |
| `es-ES` | Spaans (Spanje)                 | Vrouw | "es-ES-ElviraNeural"     | Algemeen |
| `es-MX` | Spaans (Mexico)                | Vrouw | "es-MX-DaliaNeural"      | Algemeen |
| `fr-CA` | Frans (Canada)                 | Vrouw | "FR-CA-SylvieNeural"     | Algemeen |
| `fr-FR` | Frans (Frankrijk)                 | Vrouw | "fr-FR-DeniseNeural"     | Algemeen |
| `it-IT` | Italiaans (Italië)                 | Vrouw | ' it-IT-ElsaNeural '       | Algemeen |
| `ja-JP` | Japans                        | Vrouw | "ja-JP-NanamiNeural"     | Algemeen |
| `ko-KR` | Koreaans                          | Vrouw | "ko-KR-SunHiNeural"      | Algemeen |
| `nb-NO` | Noors                       | Vrouw | "nb-NO-IselinNeural"     | Algemeen |
| `pt-BR` | Portugees (Brazilië)             | Vrouw | "pt-BR-FranciscaNeural"  | Algemeen |
| `tr-TR` | Turks                         | Vrouw | "tr-TR-EmelNeural"       | Algemeen |
| `zh-CN` | Chinees (Mandarijn, vereenvoudigd)  | Vrouw | "zh-CN-XiaoxiaoNeural"   | Algemeen, meerdere spraak stijlen beschikbaar |
|         |                                 | Vrouw | "zh-CN-XiaoyouNeural"    | Kid Voice, geoptimaliseerd voor het opnemen van tekst in een verhaal |
|         |                                 | Man   | "zh-CN-YunyangNeural"    | Geoptimaliseerd voor nieuws lezen, meerdere spraak stijlen beschikbaar |
|         |                                 | Man   | "zh-CN-YunyeNeural"      | Geoptimaliseerd voor het opnemen van tekst in een verhaal |

> [!IMPORTANT]
> De `en-US-JessaNeural` stem is gewijzigd in `en-US-AriaNeural` . Als u ' Jessa ' eerder gebruikt, converteer dan naar ' Aria '.

Zie voor meer informatie over het configureren en aanpassen van Neural stemmen de taal voor de [opmaak van spraak synthese](speech-synthesis-markup.md#adjust-speaking-styles).

> [!TIP]
> U kunt de volledige toewijzing van de service naam blijven gebruiken als ' micro soft server Speech Text to Speech Voice (nl-nl, AriaNeural) ' in uw spraakherkennings aanvragen.

### <a name="standard-voices"></a>Standaard stemmen

Meer dan 75 standaard stemmen zijn beschikbaar in meer dan 45 talen en land instellingen, waarmee u tekst kunt converteren naar gesynthesizerde spraak. Zie [regio's](regions.md#standard-and-neural-voices)voor meer informatie over regionale Beschik baarheid.

| Landinstelling | Taal | Geslacht | Spraak naam |
|--|--|--|--|
| <sup>i</sup>`ar-EG` | Arabisch (Egypte) | Vrouw | "ar-EG-Hoda" |
| `ar-SA` | Arabisch (Saoedi-Arabië) | Man | "AR-SA-Naayf" |
| `bg-BG` | Bulgaars | Man |  "bg-BG-Ivan" |
| `ca-ES` | Catalaans | Vrouw |  "ca-ES-HerenaRUS" |
| `cs-CZ` | Tsjechisch | Man | "CS-CZ-Jakub" |
| `da-DK` | Deens | Vrouw |  "da-DK-HelleRUS" |
| `de-AT` | Duits (Oostenrijk) | Man | "de-Michael" |
| `de-CH` | Duits (Zwitserland) | Man |  "de-CH-Karsten" |
| `de-DE` | Duits (Duitsland) | Vrouw |  "de-DE-Hedda" |
|  |  | Vrouw | "de-DE-HeddaRUS" |
|  |  | Man |  "de-DE-Stefan-Apollo" |
| `el-GR` | Grieks | Man | "El-GR-Stefanos" |
| `en-AU` | Engels (Australië) | Vrouw |  "en-AU-Catherine" |
|  |  | Vrouw |  "en-AU-HayleyRUS" |
| `en-CA` | Engels (Canada) | Vrouw |  "en-CA-Linda" |
|  |  | Vrouw |  "en-CA-HeatherRUS" |
| `en-GB` | Engels (UK) | Vrouw |  "en-GB-Susan-Apollo" |
|  |  | Vrouw |  "en-GB-HazelRUS" |
|  |  | Man |  "en-GB-George-Apollo" |
| `en-IE` | Engels (Ierland) | Man | "en-IE-Johan" |
| `en-IN` | Engels (India) | Vrouw | "en-IN-heera-Apollo" |
|  |  | Vrouw |  "en-IN-PriyaRUS" |
|  |  | Man |  "en-IN-Ravi-Apollo" |
| `en-US` | Engels (VS) | Vrouw |  "en-US-ZiraRUS" |
|  |  | Vrouw | "en-US-AriaRUS" |
|  |  | Man | "en-US-BenjaminRUS" |
|  |  | Man |  "en-US-Guy24kRUS" |
| `es-ES` | Spaans (Spanje) | Vrouw |  "es-ES-Laura-Apollo" |
|  |  | Vrouw | "es-ES-HelenaRUS" |
|  |  | Man | "es-ES-Pablo-Apollo" |
| `es-MX` | Spaans (Mexico) | Vrouw |  "es-MX-HildaRUS" |
|  |  | Man | "es-MX-Raul-Apollo" |
| `fi-FI` | Fins | Vrouw | "fi-FI-HeidiRUS" |
| `fr-CA` | Frans (Canada) | Vrouw | "FR-CA-Caroline" |
|  |  | Vrouw | "FR-CA-HarmonieRUS" |
| `fr-CH` | Frans (Zwitserland) | Man | "FR-CH-Guillaume" |
| `fr-FR` | Frans (Frankrijk) | Vrouw |  "fr-FR-Pascaline-Apollo" |
|  |  | Vrouw |"fr-FR-HortenseRUS" |
|  |  | Man |  "fr-FR-Paul-Apollo" |
| `he-IL` | Hebreeuws (Israël) | Man |  "he IL-Asaf" |
| `hi-IN` | Hindi (India) | Vrouw | "Hi-IN-Kalpana-Apollo" |
|  |  | Vrouw |  "Hi-IN-Kalpana" |
|  |  | Man |  "Hi-IN-Hemant" |
| `hr-HR` | Kroatisch | Man | "HR-HR-Matej" |
| `hu-HU` | Hongaars | Man |  "hu-HU-Szabolcs" |
| `id-ID` | Indonesisch | Man | "id-ID-andika" |
| `it-IT` | Italiaans | Man |  "it-IT-Cosimo-Apollo" |
|  |  | Vrouw |  ' it-IT-LuciaRUS ' |
| `ja-JP` | Japans | Vrouw |  "ja-JP-Ayumi-Apollo" |
|  |  | Man | "ja-JP-Ichiro-Apollo" |
|  |  | Vrouw |  "ja-JP-HarukaRUS" |
| `ko-KR` | Koreaans | Vrouw | "ko-KR-HeamiRUS" |
| `ms-MY` | Maleisisch | Man |  ' MS-MY-Rizwan ' |
| `nb-NO` | Noors | Vrouw |  "nb-NO-HuldaRUS" |
| `nl-NL` | Nederlands | Vrouw |  "nl-NL-HannaRUS" |
| `pl-PL` | Pools | Vrouw |  "pl-PL-PaulinaRUS" |
| `pt-BR` | Portugees (Brazilië) | Vrouw | "pt-BR-HeloisaRUS" |
|  |  | Man |  "pt-BR-Apollo" |
| `pt-PT` | Portugees (Portugal) | Vrouw | "pt-PT-HeliaRUS" |
| `ro-RO` | Roemeens | Man | "ro-RO-Andrei" |
| `ru-RU` | Russisch | Vrouw |  "ru-RU-Irina-Apollo" |
|  |  | Man | "ru-RU-Pavel-Apollo" |
|  |  | Vrouw |  ru-RU-EkaterinaRUS |
| `sk-SK` | Slowaaks | Man | "sk-SK-Filip" |
| `sl-SI` | Sloveens | Man |  "sl-SI-lado" |
| `sv-SE` | Zweeds | Vrouw | "sv-SE-HedvigRUS" |
| `ta-IN` | Tamil (India) | Man |  "TA-IN-Valluvar" |
| `te-IN` | Telugu (India) | Vrouw |  "te-IN-Chitra" |
| `th-TH` | Thai | Man |  ' th-do-Pattara ' |
| `tr-TR` | Turks (Turkije) | Vrouw | "tr-TR-SedaRUS" |
| `vi-VN` | Vietnamees | Man |  "VI-VN-a" |
| `zh-CN` | Chinees (Mandarijn, vereenvoudigd) | Vrouw |  "zh-CN-HuihuiRUS" |
|  |  | Vrouw | "zh-CN-Yaoyao-Apollo" |
|  |  | Man | "zh-CN-Kangkang-Apollo" |
| `zh-HK` | Chinees (Kantonees, traditioneel) | Vrouw |  "zh-HK-Tracy-Apollo" |
|  |  | Vrouw | "zh-HK-TracyRUS" |
|  |  | Man |  "zh-HK-Danny-Apollo" |
| `zh-TW` | Chinees (Taiwan Mandarijn) | Vrouw |  "zh-TW-Yating-Apollo" |
|  |  | Vrouw | "zh-TW-HanHanRUS" |
|  |  | Man |  "zh-TW-Zhiwei-Apollo" |

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
| Klingon                 | `tlh-Latn`    |
| Klingon (plqaD)         | `tlh-Piqd`    |
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

## <a name="speaker-recognition"></a>Speaker Recognition

Raadpleeg de volgende tabel voor ondersteunde talen voor de verschillende Speaker Recognition-Api's. Zie het [overzicht](speaker-recognition-overview.md) voor meer informatie over speaker Recognition.

| Landinstelling | Taal | Tekst afhankelijke verificatie | Tekst onafhankelijke verificatie | Tekst onafhankelijke identificatie |
|----|----|----|----|----|
| nl-NL | Engels (VS) | ja | ja | ja |
|zh-CN  |Chinees (Mandarijn, vereenvoudigd)|    N.v.t.|    ja|    ja|
|de-DE  |Duits (Duitsland)   |N.v.t.    |ja    |ja|
|en-GB  |Engels (UK)   |N.v.t.    |ja    |ja|
|fr-FR  |Frans (Frankrijk)    |N.v.t.    |ja    |ja|
|en-AU  |Engels (Australië)    |N.v.t.    |ja    |ja|
|en-CA  |Engels (Canada)   |N.v.t.|   ja|    ja|
|FR-CA  |Frans (Canada)    |N.v.t.    |ja|   ja|
|it-IT  |Italiaans|   N.v.t. |ja|   ja|
|es-ES| Spaans (Spanje) |N.v.t.    |ja|   ja|
|es-MX  |Spaans (Mexico)   |N.v.t.|   ja|    ja|
|ja-JP| Japans    |N.v.t.    |ja    |ja|
|pt-BR| Portugees (Brazilië)|    N.v.t.|    ja|    ja|

## <a name="next-steps"></a>Volgende stappen

* [Uw proef abonnement voor spraak services ophalen](https://azure.microsoft.com/try/cognitive-services/)
* [Zie spraak herkennen in C #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
