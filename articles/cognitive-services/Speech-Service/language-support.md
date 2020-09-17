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
ms.openlocfilehash: 9a1745aa0e78dafca70ea90d4a5ac92f340312b3
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/16/2020
ms.locfileid: "90706928"
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

| Landinstelling  | Taal                          | Aanpassingen                                    |
|---------|-----------------------------------|---------------------------------------------------|
| `ar-AE` | Arabisch (Verenigde Arabische Emiraten)     | Taalmodel                                    |
| `ar-BH` | Arabisch (Bahrein), modern, standaard | Taalmodel                                    |
| `ar-EG` | Arabisch (Egypte)                    | Taalmodel                                    |
| `ar-IL` | Arabisch (Israël)                   | No                                                |
| `ar-IQ` | Arabisch (Irak)                     | Taalmodel                                    |
| `ar-JO` | Arabisch (Jordanië)                   | Taalmodel                                    |
| `ar-KW` | Arabisch (Koeweit)                   | Taalmodel                                    |
| `ar-LB` | Arabisch (Libanon)                  | Taalmodel                                    |
| `ar-OM` | Arabisch (Oman)                     | Taalmodel                                    |
| `ar-PS` | Arabisch (status van Palestijnse)       | No                                                |
| `ar-QA` | Arabisch (Qatar)                    | Taalmodel                                    |
| `ar-SA` | Arabisch (Saoedi-Arabië)             | Taalmodel                                    |
| `ar-SY` | Arabisch (Syrië)                    | Taalmodel                                    |
| `bg-BG` | Bulgaars (Bulgarije)              | Taalmodel                                    |
| `ca-ES` | Catalaans (Spanje)                   | Taalmodel                                    |
| `cs-CZ` | Tsjechisch (Tsjechische Republiek)            | Taal model                                    | 
| `da-DK` | Deens (Denemarken)                  | Taalmodel                                    |
| `de-DE` | Duits (Duitsland)                  | Akoestisch model<br>Taalmodel<br>Uitspraak van |
| `el-GR` | Grieks (Griekenland)                    | Taalmodel                                    |
| `en-AU` | Engels (Australië)               | Akoestisch model<br>Taalmodel                  |
| `en-CA` | Engels (Canada)                  | Akoestisch model<br>Taalmodel                  |
| `en-GB` | Engels (Verenigd Koninkrijk)          | Akoestisch model<br>Taalmodel<br>Uitspraak van |
| `en-HK` | Engels (Hongkong)               | Taal model                                    | 
| `en-IE` | Engels (Ierland)                 | Taal model                                    | 
| `en-IN` | Engels (India)                   | Akoestisch model<br>Taalmodel                  |
| `en-NZ` | Engels (Nieuw-Zeeland)             | Akoestisch model<br>Taalmodel                  |
| `en-PH` | Engels (Filipijnen)             | Taal model                                    | 
| `en-SG` | Engels (Singapore)               | Taal model                                    | 
| `en-US` | Engels (Verenigde Staten)           | Akoestisch model<br>Taalmodel<br>Uitspraak van |
| `en-ZA` | Engels (Zuid-Afrika)            | Taal model                                    | 
| `es-AR` | Spaans (Argentinië)               | Taal model                                    | 
| `es-BO` | Spaans (Bolivia)                 | Taal model                                    | 
| `es-CL` | Spaans (Chili)                   | Taal model                                    | 
| `es-CO` | Spaans (Colombia)                | Taal model                                    | 
| `es-CR` | Spaans (Costa Rica)              | Taal model                                    | 
| `es-CU` | Spaans (Cuba)                    | Taal model                                    | 
| `es-DO` | Spaans (Dominicaanse Republiek)      | Taal model                                    | 
| `es-EC` | Spaans (Ecuador)                 | Taal model                                    | 
| `es-ES` | Spaans (Spanje)                   | Akoestisch model<br>Taalmodel                  |
| `es-GT` | Spaans (Guatemala)               | Taal model                                    | 
| `es-HN` | Spaans (Honduras)                | Taal model                                    | 
| `es-MX` | Spaans (Mexico)                  | Akoestisch model<br>Taalmodel                  |
| `es-NI` | Spaans (Nicaragua)               | Taal model                                    | 
| `es-PA` | Spaans (Panama)                  | Taal model                                    | 
| `es-PE` | Spaans (Peru)                    | Taal model                                    | 
| `es-PR` | Spaans (Puerto Rico)             | Taal model                                    | 
| `es-PY` | Spaans (Paraguay)                | Taal model                                    | 
| `es-SV` | Spaans (El Salvador)             | Taal model                                    | 
| `es-US` | Spaans (Verenigde Staten)                     | Taal model                                    | 
| `es-UY` | Spaans (Uruguay)                 | Taal model                                    | 
| `es-VE` | Spaans (Venezuela)               | Taal model                                    |
| `et-EE` | Estisch (Estland)                 | Taal model                                    | 
| `fi-FI` | Fins (Finland)                 | Taalmodel                                    |
| `fr-CA` | Frans (Canada)                   | Akoestisch model<br>Taalmodel                  |
| `fr-FR` | Frans (Frankrijk)                   | Akoestisch model<br>Taalmodel<br>Uitspraak van |
| `ga-IE` | Ierse (Ierland)                    | Taalmodel                                    |
| `gu-IN` | Gujarati (Indiase)                 | Taalmodel                                    |
| `hi-IN` | Hindi (India)                     | Akoestisch model<br>Taalmodel                  |
| `hr-HR` | Kroatisch (Kroatië)                | Taalmodel                                    |
| `hu-HU` | Hongaars (Hongarije)               | Taal model                                    | 
| `it-IT` | Italiaans (Italië)                   | Akoestisch model<br>Taalmodel<br>Uitspraak van |
| `ja-JP` | Japans (Japan)                  | Taalmodel                                    |
| `ko-KR` | Koreaans (Korea)                    | Taalmodel                                    |
| `lt-LT` | Litouws (Litouwen)            | Taalmodel                                    |
| `lv-LV` | Lets (Letland)                  | Taalmodel                                    |
| `mr-IN` | Marathi (India)                   | Taalmodel                                    |
| `mt-MT` | Maltees (Malta)                    | Taalmodel                                    |
| `nb-NO` | Noors (Bokmål) (Noor wegen)       | Taalmodel                                    |
| `nl-NL` | Nederlands (Nederland)               | Taalmodel                                    |
| `pl-PL` | Pools (Polen)                   | Taalmodel                                    |
| `pt-BR` | Portugees (Brazilië)               | Akoestisch model<br>Taalmodel<br>Uitspraak van |
| `pt-PT` | Portugees (Portugal)             | Taalmodel                                    |
| `ro-RO` | Roemeens (Roemenië)                | Taalmodel                                    |
| `ru-RU` | Russisch (Rusland)                  | Akoestisch model<br>Taalmodel                  |
| `sk-SK` | Slowaaks (Slowakije)                 | Taalmodel                                    |
| `sl-SI` | Sloveens (Slovenië)              | Taalmodel                                    |
| `sv-SE` | Zweeds (Zweden)                  | Taalmodel                                    |
| `ta-IN` | Tamil (India)                     | Taalmodel                                    |
| `te-IN` | Telugu (India)                    | Taalmodel                                    |
| `th-TH` | Thai (Thailand)                   | Taalmodel                                    |
| `tr-TR` | Turks (Turkije)                  | Taalmodel                                    |
| `zh-CN` | Chinees (Mandarijn, vereenvoudigd)    | Akoestisch model<br>Taalmodel                  |
| `zh-HK` | Chinees (Kantonees, traditioneel)  | Taalmodel                                    |
| `zh-TW` | Chinees (Taiwan Mandarijn)      | Taalmodel                                    |

## <a name="text-to-speech"></a>Tekst naar spraak

Zowel de micro soft Speech SDK als REST Api's ondersteunen deze stemmen, die elk een specifieke taal en dialect ondersteunt, geïdentificeerd door land instellingen. U kunt ook een volledige lijst met talen en stemmen verkrijgen die worden ondersteund voor elke specifieke regio/elk eind punt via de [stemmen/List-API](rest-text-to-speech.md#get-a-list-of-voices). 

> [!IMPORTANT]
> De prijzen zijn afhankelijk van de standaard, aangepaste en Neural stemmen. Ga naar de pagina met [prijzen](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) voor meer informatie.

### <a name="neural-voices"></a>Neurale stemmen

Neural text-to-speech is een nieuw type spraak synthese dat wordt aangedreven door diepe Neural-netwerken. Als u een neurale stem gebruikt, is de gesynthetiseerde spraak bijna niet van de menselijke opnamen te onderscheiden.

Neural stemmen kunnen worden gebruikt om interacties te maken met chat bots uitbreiden en spraak assistenten die natuurlijk en aantrekkelijker zijn, en om digitale teksten, zoals e-books, te converteren naar Audiobooks en in-car navigatie systemen te verbeteren. Dankzij de menselijke, natuurlijke prosodie en duidelijke articulatie verminderen neurale stemmen de bij het luisteren optredende vermoeidheid wanneer gebruikers met AI-systemen communiceren.

Zie [regio's](regions.md#standard-and-neural-voices)voor meer informatie over regionale Beschik baarheid.

|Landinstelling  | Taal            | Geslacht | Spraak naam | Stijl ondersteuning |
|--|--|--|--|--|
| `ar-EG` | Arabisch (Egypte)                  | Vrouw | `ar-EG-SalmaNeural`      | Algemeen |
| `ar-SA` | Arabisch (Saoedi-Arabië)           | Vrouw | `ar-SA-ZariyahNeural`    | Algemeen |
| `ca-ES` | Catalaans (Spanje)                 | Vrouw | `ca-ES-AlbaNeural`       | Algemeen |
| `da-DK` | Deens (Denemarken)                | Vrouw | `da-DK-ChristelNeural`   | Algemeen |
| `de-DE` | Duits (Duitsland)                | Vrouw | `de-DE-KatjaNeural`      | Algemeen |
| `en-AU` | Engels (Australië)             | Vrouw | `en-AU-NatashaNeural`    | Algemeen |
| `en-CA` | Engels (Canada)                | Vrouw | `en-CA-ClaraNeural`      | Algemeen |
| `en-GB` | Engels (UK)                    | Vrouw | `en-GB-LibbyNeural`      | Algemeen |
|         |                                 | Vrouw | `en-GB-MiaNeural`        | Algemeen |
| `en-IN` | Engels (India)                 | Vrouw | `en-IN-NeerjaNeural`     | Algemeen |
| `en-US` | Engels (VS)                    | Vrouw | `en-US-AriaNeural`       | Algemeen, meerdere spraak stijlen beschikbaar |
|         |                                 | Man   | `en-US-GuyNeural`        | Algemeen |
| `es-ES` | Spaans (Spanje)                 | Vrouw | `es-ES-ElviraNeural`     | Algemeen |
| `es-MX` | Spaans (Mexico)                | Vrouw | `es-MX-DaliaNeural`      | Algemeen |
| `fi-FI` | Fins (Finland)               | Vrouw | `fi-FI-NooraNeural`      | Algemeen |
| `fr-CA` | Frans (Canada)                 | Vrouw | `fr-CA-SylvieNeural`     | Algemeen |
| `fr-FR` | Frans (Frankrijk)                 | Vrouw | `fr-FR-DeniseNeural`     | Algemeen |
| `hi-IN` | Hindi (India)                   | Vrouw | `hi-IN-SwaraNeural`      | Algemeen |
| `it-IT` | Italiaans (Italië)                 | Vrouw | `it-IT-ElsaNeural`       | Algemeen |
| `ja-JP` | Japans                        | Vrouw | `ja-JP-NanamiNeural`     | Algemeen |
| `ko-KR` | Koreaans                          | Vrouw | `ko-KR-SunHiNeural`      | Algemeen |
| `nb-NO` | Noors                       | Vrouw | `nb-NO-IselinNeural`     | Algemeen |
| `nl-NL` | Nederlands (Netherland)              | Vrouw | `nl-NL-ColetteNeural`    | Algemeen |
| `pl-PL` | Pools (Polen)                 | Vrouw | `pl-PL-ZofiaNeural`      | Algemeen |
| `pt-BR` | Portugees (Brazilië)             | Vrouw | `pt-BR-FranciscaNeural`  | Algemeen, meerdere spraak stijlen beschikbaar |
| `tr-TR` | Turks                         | Vrouw | `tr-TR-EmelNeural`       | Algemeen |
| `pt-PT` | Portugees (Portugal)           | Vrouw | `pt-PT-FernandaNeural`   | Algemeen |
| `ru-RU` | Russisch (Rusland)                | Vrouw | `ru-RU-DariyaNeural`     | Algemeen |
| `sv-SE` | Zweeds (Zweden)                | Vrouw | `sv-SE-HilleviNeural`    | Algemeen |
| `th-TH` | Thai (Thailand)                 | Vrouw | `th-TH-AcharaNeural`     | Algemeen |
| `zh-CN` | Chinees (Mandarijn, vereenvoudigd)  | Vrouw | `zh-CN-XiaoxiaoNeural`   | Algemeen, meerdere spraak stijlen beschikbaar |
|         |                                 | Vrouw | `zh-CN-XiaoyouNeural`    | Kid Voice, geoptimaliseerd voor het opnemen van tekst in een verhaal |
|         |                                 | Man   | `zh-CN-YunyangNeural`    | Geoptimaliseerd voor nieuws lezen, meerdere spraak stijlen beschikbaar |
|         |                                 | Man   | `zh-CN-YunyeNeural`      | Geoptimaliseerd voor het opnemen van tekst in een verhaal |
| `zh-HK` | Chinees (Kantonees, traditioneel)   | Vrouw | `zh-HK-HiuGaaiNeural`| Algemeen |
| `zh-TW` | Chinees (Taiwan Mandarijn)   | Vrouw | `zh-TW-HsiaoYuNeural`    | Algemeen |

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

| Landinstelling | Taal | Tekstafhankelijke verificatie | Tekstonafhankelijke verificatie | Tekst onafhankelijke identificatie |
|----|----|----|----|----|
| nl-NL | Engels (VS) | ja | ja | ja |
|zh-CN    |Chinees (Mandarijn, vereenvoudigd)|    N.v.t.|    ja|    ja|
|de-DE    |Duits (Duitsland)    |N.v.t.    |ja    |ja|
|en-GB    |Engels (UK)    |N.v.t.    |ja    |ja|
|fr-FR    |Frans (Frankrijk)    |N.v.t.    |ja    |ja|
|en-AU    |Engels (Australië)    |N.v.t.    |ja    |ja|
|en-CA    |Engels (Canada)    |N.v.t.|    ja|    ja|
|FR-CA    |Frans (Canada)    |N.v.t.    |ja|    ja|
|it-IT    |Italiaans|    N.v.t.    |ja|    ja|
|es-ES|    Spaans (Spanje)    |N.v.t.    |ja|    ja|
|es-MX    |Spaans (Mexico)    |N.v.t.|    ja|    ja|
|ja-JP|    Japans    |N.v.t.    |ja    |ja|
|pt-BR|    Portugees (Brazilië)|    N.v.t.|    ja|    ja|

## <a name="next-steps"></a>Volgende stappen

* [Een gratis Azure-account maken](https://azure.microsoft.com/free/cognitive-services/)
* [Zie spraak herkennen in C #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
