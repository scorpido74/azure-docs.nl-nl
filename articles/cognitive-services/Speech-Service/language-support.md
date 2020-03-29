---
title: Taalondersteuning - Spraakservice
titleSuffix: Azure Cognitive Services
description: De spraakservice ondersteunt tal van talen voor spraak-naar-tekst- en tekst-naar-spraakconversie, samen met spraakvertaling. Dit artikel bevat een uitgebreide lijst met taalondersteuning per servicefunctie.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: d01ab60790311649e424a98d5a08c6af0bca90f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336043"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Taal- en spraakondersteuning voor de spraakservice

Taalondersteuning verschilt per spraakservicefunctionaliteit. In de volgende tabellen wordt een overzicht gegeven van de taalondersteuning voor [spraak-naar-tekst-](#speech-to-text) [en spraak-](#text-to-speech)en [spraakvertaalserviceaanbiedingen.](#speech-translation)

## <a name="speech-to-text"></a>Spraak naar tekst

Zowel de Microsoft Speech SDK als de REST API ondersteunen de volgende talen (landals). Om de nauwkeurigheid te verbeteren, wordt aanpassing aangeboden voor een subset van de talen door het uploaden van Audio + Human-label Transcripts of Related Text: Sentences. Aanpassing van de uitspraak `en-US` `de-DE`is momenteel alleen beschikbaar voor en . Meer informatie over maatwerk [vindt u hier](how-to-custom-speech.md).

<!--
To get the AM and ML bits:
https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20models%3A/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Landinstelling  | Taal                          | Ondersteund | Aanpassingen                                    |
|---------|-----------------------------------|-----------|---------------------------------------------------|
| `ar-AE` | Arabisch (VAE)                      | Ja       | Nee                                                |
| `ar-BH` | Arabisch (Bahrein), moderne standaard | Ja       | Taalmodel                                    |
| `ar-EG` | Arabisch (Egypte)                    | Ja       | Taalmodel                                    |
| `ar-KW` | Arabisch (Koeweit)                   | Ja       | Nee                                                |
| `ar-QA` | Arabisch (Qatar)                    | Ja       | Nee                                                |
| `ar-SA` | Arabisch (Saoedi-Arabië)             | Ja       | Nee                                                |
| `ar-SY` | Arabisch (Syrië)                    | Ja       | Taalmodel                                    |
| `ca-ES` | Catalaans                           | Ja       | Taalmodel                                    |
| `da-DK` | Deens (Denemarken)                  | Ja       | Taalmodel                                    |
| `de-DE` | Duits (Duitsland)                  | Ja       | Akoestisch model<br>Taalmodel<br>Uitspraak |
| `en-AU` | Engels (Australië)               | Ja       | Akoestisch model<br>Taalmodel                  |
| `en-CA` | Engels (Canada)                  | Ja       | Akoestisch model<br>Taalmodel                  |
| `en-GB` | Engels (Verenigd Koninkrijk)          | Ja       | Akoestisch model<br>Taalmodel<br>Uitspraak |
| `en-IN` | Engels (India)                   | Ja       | Akoestisch model<br>Taalmodel                  |
| `en-NZ` | Engels (Nieuw-Zeeland)             | Ja       | Akoestisch model<br>Taalmodel                  |
| `en-US` | Engels (Verenigde Staten)           | Ja       | Akoestisch model<br>Taalmodel<br>Uitspraak |
| `es-ES` | Spaans (Spanje)                   | Ja       | Akoestisch model<br>Taalmodel                  |
| `es-MX` | Spaans (Mexico)                  | Ja       | Akoestisch model<br>Taalmodel                  |
| `fi-FI` | Fins (Finland)                 | Ja       | Taalmodel                                    |
| `fr-CA` | Frans (Canada)                   | Ja       | Akoestisch model<br>Taalmodel                  |
| `fr-FR` | Frans (Frankrijk)                   | Ja       | Akoestisch model<br>Taalmodel<br>Uitspraak |
| `gu-IN` | Gujarati (Indiaan)                 | Ja       | Taalmodel                                    |
| `hi-IN` | Hindi (India)                     | Ja       | Akoestisch model<br>Taalmodel                  |
| `it-IT` | Italiaans (Italië)                   | Ja       | Akoestisch model<br>Taalmodel<br>Uitspraak |
| `ja-JP` | Japans (Japan)                  | Ja       | Taalmodel                                    |
| `ko-KR` | Koreaans (Korea)                    | Ja       | Taalmodel                                    |
| `mr-IN` | Marathi (India)                   | Ja       | Taalmodel                                    |
| `nb-NO` | Noors (Bokmål) (Noorwegen)       | Ja       | Taalmodel                                    |
| `nl-NL` | Nederlands (Nederland)               | Ja       | Taalmodel                                    |
| `pl-PL` | Pools (Polen)                   | Ja       | Taalmodel                                    |
| `pt-BR` | Portugees (Brazilië)               | Ja       | Akoestisch model<br>Taalmodel<br>Uitspraak |
| `pt-PT` | Portugees (Portugal)             | Ja       | Taalmodel                                    |
| `ru-RU` | Russisch (Rusland)                  | Ja       | Akoestisch model<br>Taalmodel                  |
| `sv-SE` | Zweeds (Zweden)                  | Ja       | Taalmodel                                    |
| `ta-IN` | Tamil (India)                     | Ja       | Taalmodel                                    |
| `te-IN` | Telugu (India)                    | Ja       | Nee                                                |
| `th-TH` | Thai (Thailand)                   | Ja       | Nee                                                |
| `tr-TR` | Turks (Turkije)                  | Ja       | Nee                                                |
| `zh-CN` | Chinees (Mandarijn, vereenvoudigd)    | Ja       | Akoestisch model<br>Taalmodel                  |
| `zh-HK` | Chinees (Kantonees, Traditioneel)  | Ja       | Taalmodel                                    |
| `zh-TW` | Chinees (Taiwanees Mandarijn)      | Ja       | Taalmodel                                    |

## <a name="text-to-speech"></a>Tekst naar spraak

Zowel de Microsoft Speech SDK als de REST API's ondersteunen deze stemmen, die elk een specifieke taal en dialect ondersteunen, geïdentificeerd door locale.

> [!IMPORTANT]
> Prijzen variëren voor standaard, aangepaste en neurale stemmen. Ga naar [de prijspagina](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) voor meer informatie.

### <a name="neural-voices"></a>Neurale stemmen

Neurale tekst-naar-spraak is een nieuw type spraaksynthese aangedreven door diepe neurale netwerken. Bij het gebruik van een neurale stem, gesynthetiseerde spraak is bijna niet te onderscheiden van de menselijke opnames.

Neurale stemmen kunnen worden gebruikt om interacties met chatbots en spraakassistenten natuurlijker en boeiender te maken, digitale teksten zoals e-books om te zetten in audioboeken en navigatiesystemen in de auto te verbeteren. Met de mens-achtige natuurlijke prosodie en duidelijke articulatie van woorden, neurale stemmen aanzienlijk verminderen luistervermoeidheid wanneer gebruikers communiceren met AI-systemen.

Zie [regio's](regions.md#standard-and-neural-voices)voor meer informatie over de regionale beschikbaarheid.

| Landinstelling  | Taal            | Geslacht | Full service naamtoewijzing                                               | Korte stemnaam        |
|---------|---------------------|--------|-------------------------------------------------------------------------|-------------------------|
| `de-DE` | Duits (Duitsland)    | Vrouw | "Microsoft Server Speech Text to Speech Voice (de-DE, KatjaNeural)"     | "de-DE-KatjaNeural"     |
| `en-US` | Engels (VS)        | Vrouw | "Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)"      | "en-US-AriaNeural"      |
| `en-US` | Engels (VS)        | Man   | "Microsoft Server Speech Text to Speech Voice (en-US, GuyNeural)"       | "en-US-GuyNeural"       |
| `it-IT` | Italiaans (Italië)     | Vrouw | "Microsoft Server Speech Text to Speech Voice (it-IT, ElsaNeural)"      | "it-IT-ElsaNeural"      |
| `pt-BR` | Portugees (Brazilië) | Vrouw | "Microsoft Server Speech Text to Speech Voice (pt-BR, FranciscaNeural)" | "pt-BR-FranciscaNeural" |
| `zh-CN` | Chinees (Mandarijn, vereenvoudigd)  | Vrouw | "Microsoft Server Speech Text to Speech Voice (zh-CN, XiaoxiaoNeural)"  | "zh-CN-XiaoxiaoNeural"  |

> [!IMPORTANT]
> De `en-US-JessaNeural` stem is `en-US-AriaNeural`veranderd in . Als je eerder "Jessa" gebruikte, ga dan over naar "Aria".

Zie Taal voor de [opmaakvan spraaksynthese](speech-synthesis-markup.md#adjust-speaking-styles)voor meer informatie over het configureren en aanpassen van neurale stemmen.

> [!TIP]
> U de full service naamtoewijzing of de korte spraaknaam gebruiken in uw spraaksyntheseaanvragen.

### <a name="standard-voices"></a>Standaardstemmen

Meer dan 75 standaardstemmen zijn beschikbaar in meer dan 45 talen en land, waarmee u tekst omzetten in gesynthetiseerde spraak. Zie [regio's](regions.md#standard-and-neural-voices)voor meer informatie over de regionale beschikbaarheid.

| Landinstelling | Taal | Geslacht | Full service naamtoewijzing | Korte naam |
|--|--|--|--|--|
| <sup>1</sup>`ar-EG` | Arabisch (Egypte) | Vrouw | "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)" | "ar-EG-Hoda" |
| `ar-SA` | Arabisch (Saoedi-Arabië) | Man | "Microsoft Server Speech To Speech Voice (ar-SA, Naayf)" | "ar-SA-Naayf" |
| `bg-BG` | Bulgaars | Man | "Microsoft Server Speech To Speech Voice (bg-BG, Ivan)" | "BG-BG-Ivan" |
| `ca-ES` | Catalaans (Spanje) | Vrouw | "Microsoft Server Speech To Speech Voice (ca-ES, HerenaRUS)" | "ca-ES-HerenaRUS" |
| `cs-CZ` | Tsjechisch | Man | "Microsoft Server Speech To Speech Voice (cs-CZ, Jakub)" | "cs-CZ-Jakub" |
| `da-DK` | Deens | Vrouw | "Microsoft Server Speech To Speech Voice (da-DK, HelleRUS)" | "da-DK-HelleRUS" |
| `de-AT` | Duits (Oostenrijk) | Man | "Microsoft Server Speech To Speech Voice (de-AT, Michael)" | "de-AT-Michael" |
| `de-CH` | Duits (Zwitserland) | Man | "Microsoft Server Speech To Speech Voice (de-CH, Karsten)" | "de-CH-Karsten" |
| `de-DE` | Duits (Duitsland) | Vrouw | "Microsoft Server Speech To Speech Voice (de-DE, Hedda)" | "de-DE-Hedda" |
|  |  | Vrouw | "Microsoft Server Speech To Speech Voice (de-DE, HeddaRUS)" | "de-DE-HeddaRUS" |
|  |  | Man | "Microsoft Server Speech To Speech Voice (de-DE, Stefan, Apollo)" | "de-DE-Stefan-Apollo" |
| `el-GR` | Grieks | Man | "Microsoft Server Speech Text to Speech Voice (el-GR, Stefanos)" | "el-GR-Stefanos" |
| `en-AU` | Engels (Australië) | Vrouw | "Microsoft Server Speech To Speech Voice (en-AU, Catherine)" | "en-AU-Catherine" |
|  |  | Vrouw | "Microsoft Server Speech To Speech Voice (en-AU, HayleyRUS)" | "en-AU-HayleyRUS" |
| `en-CA` | Engels (Canada) | Vrouw | "Microsoft Server Speech To Speech Voice (en-CA, Linda)" | "en-CA-Linda" |
|  |  | Vrouw | "Microsoft Server Speech To Speech Voice (en-CA, HeatherRUS)" | "en-CA-HeatherRUS" |
| `en-GB` | Engels (VK) | Vrouw | "Microsoft Server Speech To Speech Voice (en-GB, Susan, Apollo)" | "en-GB-Susan-Apollo" |
|  |  | Vrouw | "Microsoft Server Speech To Speech Voice (en-GB, HazelRUS)" | "en-GB-HazelRUS" |
|  |  | Man | "Microsoft Server Speech To Speech Voice (en-GB, George, Apollo)" | "en-GB-George-Apollo" |
| `en-IE` | Engels (Ierland) | Man | "Microsoft Server Speech To Speech Voice (en-IE, Sean)" | "en-IE-Sean" |
| `en-IN` | Engels (India) | Vrouw | "Microsoft Server Speech To Speech Voice (en-IN, Heera, Apollo)" | "en-IN-Heera-Apollo" |
|  |  | Vrouw | "Microsoft Server Speech To Speech Voice (en-IN, PriyaRUS)" | "en-IN-PriyaRUS" |
|  |  | Man | "Microsoft Server Speech To Speech Voice (en-IN, Ravi, Apollo)" | "en-IN-Ravi-Apollo" |
| `en-US` | Engels (VS) | Vrouw | "Microsoft Server Speech To Speech Voice (en-US, ZiraRUS)" | "en-US-ZiraRUS" |
|  |  | Vrouw | "Microsoft Server Speech To Speech Voice (en-US, AriaRUS)" | "en-US-AriaRUS" |
|  |  | Man | "Microsoft Server Speech To Speech Voice (en-US, BenjaminRUS)" | "en-US-BenjaminRUS" |
|  |  | Man | "Microsoft Server Speech To Speech Voice (en-US, Guy24kRUS)" | "en-US-Guy24kRUS" |
| `es-ES` | Spaans (Spanje) | Vrouw | "Microsoft Server Speech To Speech Voice (es-ES, Laura, Apollo)" | "es-ES-Laura-Apollo" |
|  |  | Vrouw | "Microsoft Server Speech To Speech Voice (es-ES, HelenaRUS)" | "es-ES-HelenaRUS" |
|  |  | Man | "Microsoft Server Speech To Speech Voice (es-ES, Pablo, Apollo)" | "es-ES-Pablo-Apollo" |
| `es-MX` | Spaans (Mexico) | Vrouw | "Microsoft Server Speech To Speech Voice (es-MX, HildaRUS)" | "es-MX-HildaRUS" |
|  |  | Man | "Microsoft Server Speech To Speech Voice (es-MX, Raul, Apollo)" | "es-MX-Raul-Apollo" |
| `fi-FI` | Fins | Vrouw | "Microsoft Server Speech To Speech Voice (fi-FI, HeidiRUS)" | "FI-FI-HeidiRUS" |
| `fr-CA` | Frans (Canada) | Vrouw | "Microsoft Server Speech To Speech Voice (fr-CA, Caroline)" | "fr-CA-Caroline" |
|  |  | Vrouw | "Microsoft Server Speech To Speech Voice (fr-CA, HarmonieRUS)" | "fr-CA-HarmonieRUS" |
| `fr-CH` | Frans (Zwitserland) | Man | "Microsoft Server Speech To Speech Voice (fr-CH, Guillaume)" | "fr-CH-Guillaume" |
| `fr-FR` | Frans (Frankrijk) | Vrouw | "Microsoft Server Speech To Speech Voice (fr-FR, Julie, Apollo)" | "fr-FR-Julie-Apollo" |
|  |  | Vrouw | "Microsoft Server Speech To Speech Voice (fr-FR, HortenseRUS)" | "fr-FR-HortenseRUS" |
|  |  | Man | "Microsoft Server Speech To Speech Voice (fr-FR, Paul, Apollo)" | "fr-FR-Paul-Apollo" |
| `he-IL` | Hebreeuws (Israël) | Man | "Microsoft Server Speech To Speech Voice (he-IL, Asaf)" | "hij-IL-Asaf" |
| `hi-IN` | Hindi (India) | Vrouw | "Microsoft Server Speech To Speech Voice (hi-IN, Kalpana, Apollo)" | "hi-in-Kalpana-Apollo" |
|  |  | Vrouw | "Microsoft Server Speech To Speech Voice (hi-IN, Kalpana)" | "hi-in-Kalpana" |
|  |  | Man | "Microsoft Server Speech To Speech Voice (hi-IN, Hemant)" | "hi-in-Hemant" |
| `hr-HR` | Kroatisch | Man | "Microsoft Server Speech To Speech Voice (hr-HR, Matej)" | "hr-HR-Matej" |
| `hu-HU` | Hongaars | Man | "Microsoft Server Speech To Speech Voice (hu-HU, Szabolcs)" | "hu-HU-Szabolcs" |
| `id-ID` | Indonesisch | Man | "Microsoft Server Speech To Speech Voice (id-ID, Andika)" | "ID-ID-Andika" |
| `it-IT` | Italiaans | Man | "Microsoft Server Speech Text to Speech Voice (it-IT, Cosimo, Apollo)" | "IT-IT-Cosimo-Apollo" |
|  |  | Vrouw | "Microsoft Server Speech To Speech Voice (it-IT, LuciaRUS)" | "IT-IT-LuciaRUS" |
| `ja-JP` | Japans | Vrouw | "Microsoft Server Speech Text to Speech Voice (ja-JP, Ayumi, Apollo)" | "ja-JP-Ayumi-Apollo" |
|  |  | Man | "Microsoft Server Speech Text to Speech Voice (ja-JP, Ichiro, Apollo)" | "ja-JP-Ichiro-Apollo" |
|  |  | Vrouw | "Microsoft Server Speech Text to Speech Voice (ja-JP, HarukaRUS)" | "ja-JP-HarukaRUS" |
| `ko-KR` | Koreaans | Vrouw | "Microsoft Server Speech To Speech Voice (ko-KR, HeamiRUS)" | "ko-KR-HeamiRUS" |
| `ms-MY` | Maleisisch | Man | "Microsoft Server Speech To Speech Voice (ms-MY, Rizwan)" | "ms-MY-Rizwan" |
| `nb-NO` | Norwegian | Vrouw | "Microsoft Server Speech Text to Speech Voice (nb-NO, HuldaRUS)" | "nb-NO-HuldaRUS" |
| `nl-NL` | Nederlands | Vrouw | "Microsoft Server Speech To Speech Voice (NL-NL, HannaRUS)" | "NL-NL-HannaRUS" |
| `pl-PL` | Pools | Vrouw | "Microsoft Server Speech To Speech Voice (pl-PL, PaulinaRUS)" | "pl-PL-PaulinaRUS" |
| `pt-BR` | Portugees (Brazilië) | Vrouw | "Microsoft Server Speech To Speech Voice (pt-BR, HeloisaRUS)" | "pt-BR-HeloisaRUS" |
|  |  | Man | "Microsoft Server Speech To Speech Voice (pt-BR, Daniel, Apollo)" | "pt-BR-Daniel-Apollo" |
| `pt-PT` | Portugees (Portugal) | Vrouw | "Microsoft Server Speech To Speech Voice (pt-PT, HeliaRUS)" | "PT-PT-HeliaRUS" |
| `ro-RO` | Roemeens | Man | "Microsoft Server Speech To Speech Voice (ro-RO, Andrei)" | "ro-RO-Andrei" |
| `ru-RU` | Russisch | Vrouw | "Microsoft Server Speech Text to Speech Voice (ru-RU, Irina, Apollo)" | "ru-RU-Irina-Apollo" |
|  |  | Man | "Microsoft Server Speech Text to Speech Voice (ru-RU, Pavel, Apollo)" | "RU-RU-Pavel-Apollo" |
|  |  | Vrouw | "Microsoft Server Speech Text to Speech Voice (ru-RU, EkaterinaRUS)" | ru-RU-EkaterinaRUS |
| `sk-SK` | Slowaaks | Man | "Microsoft Server Speech To Speech Voice (sk-SK, Filip)" | "SK-SK-Filip" |
| `sl-SI` | Sloveens | Man | "Microsoft Server Speech To Speech Voice (sl-SI, Lado)" | "sl-SI-Lado" |
| `sv-SE` | Zweeds | Vrouw | "Microsoft Server Speech To Speech Voice (sv-SE, HedvigRUS)" | "sv-SE-HedvigRUS" |
| `ta-IN` | Tamil (India) | Man | "Microsoft Server Speech To Speech Voice (ta-IN, Valluvar)" | "ta-IN-Valluvar" |
| `te-IN` | Telugu (India) | Vrouw | "Microsoft Server Speech To Speech Voice (te-IN, Chitra)" | "te-In-Chitra" |
| `th-TH` | Thai | Man | "Microsoft Server Speech To Speech Voice (th-TH, Pattara)" | "th-TH-Pattara" |
| `tr-TR` | Turks (Turkije) | Vrouw | "Microsoft Server Speech To Speech Voice (tr-TR, SedaRUS)" | "tr-TR-SedaRUS" |
| `vi-VN` | Vietnamees | Man | "Microsoft Server Speech To Speech Voice (vi-VN, An)" | "vi-VN-An" |
| `zh-CN` | Chinees (Mandarijn, vereenvoudigd) | Vrouw | "Microsoft Server Speech To Speech Voice (zh-CN, HuihuiRUS)" | "zh-CN-HuihuiRUS" |
|  |  | Vrouw | "Microsoft Server Speech Text to Speech Voice (zh-CN, Yaoyao, Apollo)" | "zh-CN-Yaoyao-Apollo" |
|  |  | Man | "Microsoft Server Speech Text to Speech Voice (zh-CN, Kangkang, Apollo)" | "zh-CN-Kangkang-Apollo" |
| `zh-HK` | Chinees (Kantonees, Traditioneel) | Vrouw | "Microsoft Server Speech Text to Speech Voice (zh-HK, Tracy, Apollo)" | "zh-HK-Tracy-Apollo" |
|  |  | Vrouw | "Microsoft Server Speech Text to Speech Voice (zh-HK, TracyRUS)" | "zh-HK-TracyRUS" |
|  |  | Man | "Microsoft Server Speech Text to Speech Voice (zh-HK, Danny, Apollo)" | "zh-HK-Danny-Apollo" |
| `zh-TW` | Chinees (Taiwanees Mandarijn) | Vrouw | "Microsoft Server Speech Text to Speech Voice (zh-TW, Yating, Apollo)" | "zh-TW-Yating-Apollo" |
|  |  | Vrouw | "Microsoft Server Speech Text to Speech Voice (zh-TW, HanHanRUS)" | "zh-TW-HanHanRUS" |
|  |  | Man | "Microsoft Server Speech Text to Speech Voice (zh-TW, Zhiwei, Apollo)" | "zh-TW-Zhiwei-Apollo" |

**1** *ar-EG ondersteunt Modern Standard Arabic (MSA).*

> [!IMPORTANT]
> De `en-US-Jessa` stem is `en-US-Aria`veranderd in . Als je eerder "Jessa" gebruikte, ga dan over naar "Aria".

> [!TIP]
> U de full service naamtoewijzing of de korte spraaknaam gebruiken in uw spraaksyntheseaanvragen.

### <a name="customization"></a>Aanpassing

Stemaanpassing is `de-DE`beschikbaar `en-GB` `en-IN`voor `en-US` `es-MX`, `fr-FR` `it-IT`, `pt-BR`, `zh-CN`, , en . Selecteer de juiste landint die overeenkomt met de trainingsgegevens die u hebt om een aangepast spraakmodel te trainen. Als de opnamegegevens die u hebt bijvoorbeeld in het `en-GB`Engels worden gesproken met een Brits accent, selecteert u .

> [!NOTE]
> Wij ondersteunen geen tweetalige modeltraining in Custom Voice, behalve voor de Chinees-Engelse tweetalig. Selecteer "Chinees-Engels tweetalig" als je een Chinese stem wilt trainen die ook Engels kan spreken. Spraaktraining in alle landplaatsen begint met een gegevensset van meer dan `en-US` `zh-CN` 2.000 uitingen, behalve de en waar u beginnen met elke grootte van trainingsgegevens.

## <a name="speech-translation"></a>Spraakomzetting

De **API voor spraakvertaling** ondersteunt verschillende talen voor spraak-naar-spraak- en spraak-naar-tekstvertaling. De brontaal moet altijd afkomstig zijn uit de tabel Spraak-naar-tekst. De beschikbare doeltalen zijn afhankelijk van de vraag of het vertaaldoel spraak of tekst is. U binnenkomende spraak vertalen in meer dan [60 talen.](https://www.microsoft.com/translator/business/languages/) Een subset van talen is beschikbaar voor [spraaksynthese](language-support.md#text-languages).

### <a name="text-languages"></a>Teksttalen

| Teksttaal           | Taalcode |
|:------------------------|:-------------:|
| Afrikaans               | `af`          |
| Arabisch                  | `ar`          |
| Bangla                  | `bn`          |
| Bosnisch (Latijns)         | `bs`          |
| Bulgaars               | `bg`          |
| Kantonees (Traditioneel) | `yue`         |
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
| Kiswahili               | `sw`          |
| Klingon                 | `tlh`         |
| Klingon (plqaD)         | `tlh-Qaak`    |
| Koreaans                  | `ko`          |
| Lets                 | `lv`          |
| Litouws              | `lt`          |
| Malagasi                | `mg`          |
| Maleisisch                   | `ms`          |
| Malajalam               | `ml`          |
| Maltees                 | `mt`          |
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
| Tongan (Tongan)                  | `to`          |
| Turks                 | `tr`          |
| Oekraïens               | `uk`          |
| Urdu                    | `ur`          |
| Vietnamees              | `vi`          |
| Welsh                   | `cy`          |
| Yucatec Maya            | `yua`         |

## <a name="next-steps"></a>Volgende stappen

* [Uw proefabonnement van spraakservice aanschaffen](https://azure.microsoft.com/try/cognitive-services/)
* [Bekijk hoe u spraak in C herkent #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
