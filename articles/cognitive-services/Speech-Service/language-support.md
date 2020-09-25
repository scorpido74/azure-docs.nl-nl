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
ms.openlocfilehash: b50f6c4b2ed86b950e68cf8c55b63af687ff9ead
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91298046"
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
| `ar-EG` | Arabisch (Egypte) | Vrouw | `ar-EG-SalmaNeural` | Algemeen |
| `ar-SA` | Arabisch (Saudi-Arabië) | Vrouw | `ar-SA-ZariyahNeural` | Algemeen |
| `bg-BG` <sup>Nieuw</sup> | Bulgaars (Bulgary) | Vrouw | `bg-BG-KalinaNeural` | Algemeen |
| `ca-ES` | Catalaans (Spanje) | Vrouw | `ca-ES-AlbaNeural` | Algemeen |
| `cs-CZ` <sup>Nieuw</sup> | Tsjechisch (Tsjechisch)  | Vrouw | `cs-CZ-VlastaNeural` | Algemeen |
| `da-DK` | Deens (Denemarken) | Vrouw | `da-DK-ChristelNeural` | Algemeen |
| `de-AT` <sup>Nieuw</sup> | Duits (Oostenrijk) | Vrouw | `de-AT-IngridNeural` | Algemeen |
| `de-CH` <sup>Nieuw</sup> | Duits (Zwitserland) | Vrouw | `de-CH-LeniNeural` | Algemeen |
| `de-DE` | Duits (Duitsland) | Vrouw | `de-DE-KatjaNeural` | Algemeen |
| `de-DE` <sup>Nieuw</sup> | Duits (Duitsland) | Man | `de-DE-ConradNeural` | Algemeen |
| `el-GR` <sup>Nieuw</sup> | Grieks (Griekenland) | Vrouw | `el-GR-AthinaNeural` | Algemeen |
| `en-AU` | Engels (Australië) | Vrouw | `en-AU-NatashaNeural` | Algemeen |
| `en-AU` <sup>Nieuw</sup> | Australië (Australië) | Man | `en-AU-WilliamNeural` | Algemeen |
| `en-CA` | Engels (Canada) | Vrouw | `en-CA-ClaraNeural` | Algemeen |
| `en-GB` | Engels (Verenigd Konink rijk) | Vrouw | `en-GB-LibbyNeural` | Algemeen |
| `en-GB` | Engels (Verenigd Konink rijk) | Vrouw | `en-GB-MiaNeural` | Algemeen |
| `en-GB` <sup>Nieuw</sup> | Engels (Verenigd Koninkrijk) | Man | `En-GB-RyanNeural` | Algemeen |
| `en-IE` <sup>Nieuw</sup> | Ierse Engels (Ierland) | Vrouw | `en-IE-EmilyNeural` | Algemeen |
| `en-IN` | Engels (India) | Vrouw | `en-IN-NeerjaNeural` | Algemeen |
| `en-US` | Engels (Verenigde Staten) | Vrouw | `en-US-AriaNeural` | Algemeen, meerdere spraak stijlen beschikbaar |
| `en-US` | Engels (Verenigde Staten) | Man | `en-US-GuyNeural` | Algemeen |
| `en-US` <sup>Nieuw</sup> | Engels (Verenigde Staten) | Vrouw | `en-US-JennyNeural` | Algemeen, meerdere spraak stijlen beschikbaar |
| `es-ES` | Spaans (Spanje) | Vrouw | `es-ES-ElviraNeural` | Algemeen |
| `es-ES` <sup>Nieuw</sup> | Spaans (Spanje) | Man | `es-ES-AlvaroNeural` | Algemeen |
| `es-MX` | Spaans (Mexico) | Vrouw | `es-MX-DaliaNeural` | Algemeen |
| `es-MX` <sup>Nieuw</sup> | Spaans (Mexico) | Man | `es-MX-JorgeNeural` | Algemeen |
| `fi-FI` | Fins (Finland) | Vrouw | `fi-FI-NooraNeural` | Algemeen |
| `fr-CA` | Frans (Canada) | Vrouw | `fr-CA-SylvieNeural` | Algemeen |
| `fr-CA` <sup>Nieuw</sup> | Frans (Canada) | Man | `fr-CA-JeanNeural` | Algemeen |
| `fr-CH` <sup>Nieuw</sup> | Frans (Zwitserland) | Vrouw | `fr-CH-ArianeNeural` | Algemeen |
| `fr-FR` | Frans (Frank rijk) | Vrouw | `fr-FR-DeniseNeural` | Algemeen |
| `fr-FR` <sup>Nieuw</sup> | Frans (Frankrijk) | Man | `fr-FR-HenriNeural` | Algemeen |
| `he-IL` <sup>Nieuw</sup> | Hebreeuws (Isareal) | Vrouw | `he-IL-HilaNeural` | Algemeen |
| `hi-IN` | Hindi (India) | Vrouw | `hi-IN-SwaraNeural` | Algemeen |
| `hr-HR` <sup>Nieuw</sup> | Kroatisch (Kroatië) | Vrouw | `hr-HR-GabrijelaNeural` | Algemeen |
| `hu-HU` <sup>Nieuw</sup> | Hongaars (Hongarije) | Vrouw | `hu-HU-NoemiNeural` | Algemeen |
| `id-ID` <sup>Nieuw</sup> | Bahasa Indonesisch (Indonesië) | Man | `id-ID-ArdiNeural` | Algemeen |
| `it-IT` | Italiaans (Italië) | Vrouw | `it-IT-ElsaNeural` | Algemeen |
| `it-IT` <sup>Nieuw</sup> | Italiaans (Italië) | Vrouw | `it-IT-IsabellaNeural` | Algemeen |
| `it-IT` <sup>Nieuw</sup> | Italiaans (Italië) | Man | `it-IT-DiegoNeural` | Algemeen |
| `ja-JP` | Japans (Japan) | Vrouw | `ja-JP-NanamiNeural` | Algemeen |
| `ja-JP` <sup>Nieuw</sup> | Japans (Japan) | Man | `ja-JP-KeitaNeural` | Algemeen |
| `ko-KR` | Koreaans (Zuid-Korea) | Vrouw | `ko-KR-SunHiNeural` | Algemeen |
| `ko-KR` <sup>Nieuw</sup> | Koreaans (Korea) | Man | `ko-KR-InJoonNeural` | Algemeen |
| `ms-MY` <sup>Nieuw</sup> | Maleis (Maleisië) | Vrouw | `ms-MY-YasminNeural` | Algemeen |
| `nb-NO` | Noors, Bokmål (Noor wegen) | Vrouw | `nb-NO-IselinNeural` | Algemeen |
| `nl-NL` | Nederlands (Nederland) | Vrouw | `nl-NL-ColetteNeural` | Algemeen |
| `pl-PL` | Pools (Polen) | Vrouw | `pl-PL-ZofiaNeural` | Algemeen |
| `pt-BR` | Portugees (Brazilië) | Vrouw | `pt-BR-FranciscaNeural` | Algemeen, meerdere spraak stijlen beschikbaar |
| `pt-BR` <sup>Nieuw</sup> | Braziliaans-Portugees (Brazilië) | Man | `pt-BR-AntonioNeural` | Algemeen |
| `pt-PT` | Portugees (Portugal) | Vrouw | `pt-PT-FernandaNeural` | Algemeen |
| `ro-RO` <sup>Nieuw</sup> | Roemeens (Roemenië) | Vrouw | `ro-RO-AlinaNeural` | Algemeen |
| `ru-RU` | Russisch (Rusland) | Vrouw | `ru-RU-DariyaNeural` | Algemeen |
| `sk-SK` <sup>Nieuw</sup> | Slowaaks (Slowakije) | Vrouw | `sk-SK-ViktoriaNeural` | Algemeen |
| `sl-SI` <sup>Nieuw</sup> | Sloveens (Slovenië) | Vrouw | `sl-SI-PetraNeural` | Algemeen |
| `sv-SE` | Zweeds (Zweden) | Vrouw | `sv-SE-HilleviNeural` | Algemeen |
| `ta-IN` <sup>Nieuw</sup> | Tamil (India) | Vrouw | `ta-IN-PallaviNeural` | Algemeen |
| `te-IN` <sup>Nieuw</sup> | Telugu (India) | Vrouw | `te-IN-ShrutiNeural` | Algemeen |
| `th-TH` | Thais (Thai land) | Vrouw | `th-TH-AcharaNeural` | Algemeen |
| `th-TH` <sup>Nieuw</sup> | Thai (Thailand) | Vrouw | `th-TH-PremwadeeNeural` | Algemeen |
| `tr-TR` | Turks (Turkije) | Vrouw | `tr-TR-EmelNeural` | Algemeen |
| `vi-VN` <sup>Nieuw</sup> | Vietnamees (Vietnam) | Vrouw | `vi-VN-HoaiMyNeural` | Algemeen |
| `zh-CN` | Mandarijn (vereenvoudigd Chinees, China) | Vrouw | `zh-CN-XiaoxiaoNeural` | Algemeen, meerdere spraak stijlen beschikbaar |
| `zh-CN` | Mandarijn (vereenvoudigd Chinees, China) | Vrouw | `zh-CN-XiaoyouNeural` | Kid Voice, geoptimaliseerd voor het opnemen van tekst in een verhaal |
| `zh-CN` | Mandarijn (vereenvoudigd Chinees, China) | Man | `zh-CN-YunyangNeural` | Geoptimaliseerd voor nieuws lezen, meerdere spraak stijlen beschikbaar |
| `zh-CN` | Mandarijn (vereenvoudigd Chinees, China) | Man | `zh-CN-YunyeNeural` | Geoptimaliseerd voor het opnemen van tekst in een verhaal |
| `zh-HK` | Kantonees (traditioneel Chinees, Hongkong) | Vrouw | `zh-HK-HiuGaaiNeural` | Algemeen |
| `zh-TW` | Mandarijn (traditioneel Chinees, Taiwan) | Vrouw | `zh-TW-HsiaoYuNeural` | Algemeen |


> [!IMPORTANT]
> De `en-US-JessaNeural` stem is gewijzigd in `en-US-AriaNeural` . Als u ' Jessa ' eerder gebruikt, converteer dan naar ' Aria '.

Zie voor meer informatie over het configureren en aanpassen van Neural stemmen de taal voor de [opmaak van spraak synthese](speech-synthesis-markup.md#adjust-speaking-styles).

> [!TIP]
> U kunt de volledige toewijzing van de service naam blijven gebruiken als ' micro soft server Speech Text to Speech Voice (nl-nl, AriaNeural) ' in uw spraakherkennings aanvragen.

### <a name="standard-voices"></a>Standaard stemmen

Meer dan 75 standaard stemmen zijn beschikbaar in meer dan 45 talen en land instellingen, waarmee u tekst kunt converteren naar gesynthesizerde spraak. Zie [regio's](regions.md#standard-and-neural-voices)voor meer informatie over regionale Beschik baarheid.

| Landinstelling | Taal | Geslacht | Spraak naam |
|--|--|--|--|
| `ar-EG` | Arabisch (Arabisch) | Vrouw | `ar-EG-Hoda` |
| `ar-SA` | Arabisch (Saoedi-Arabië) | Man | `ar-SA-Naayf` |
| `bg-BG` | Bulgaars (Bulgarije) | Man | `bg-BG-Ivan` |
| `ca-ES` | Catalaans (Spanje) | Vrouw | `ca-ES-HerenaRUS` |
| `cs-CZ` | Tsjechisch (Tsjechische Republiek) | Man | `cs-CZ-Jakub` |
| `da-DK` | Deens (Denemarken) | Vrouw | `da-DK-HelleRUS` |
| `de-AT` | Duits (Oostenrijk) | Man | `de-AT-Michael` |
| `de-CH` | Duits (Zwitserland) | Man | `de-CH-Karsten` |
| `de-DE` | Duits (Duitsland) | Vrouw | `de-DE-HeddaRUS` |
| `de-DE` | Duits (Duitsland) | Man | `de-DE-Stefan` |
| `el-GR` | Grieks (Griekenland) | Man | `el-GR-Stefanos` |
| `en-AU` | Engels (Australië) | Vrouw | `en-AU-Catherine` |
| `en-AU` | Engels (Australië) | Vrouw | `en-AU-HayleyRUS` |
| `en-CA` | Engels (Canada) | Vrouw | `en-CA-HeatherRUS` |
| `en-CA` | Engels (Canada) | Vrouw | `en-CA-Linda` |
| `en-GB` | Engels (Verenigd Koninkrijk) | Man | `en-GB-George` |
| `en-GB` | Engels (Verenigd Koninkrijk) | Vrouw | `en-GB-HazelRUS` |
| `en-GB` | Engels (Verenigd Koninkrijk) | Vrouw | `en-GB-Susan` |
| `en-IE` | Engels (Ierland) | Man | `en-IE-Sean` |
| `en-IN` | Engels (India) | Vrouw | `en-IN-Heera` |
| `en-IN` | Engels (India) | Vrouw | `en-IN-PriyaRUS` |
| `en-IN` | Engels (India) | Man | `en-IN-Ravi` |
| `en-US` | Engels (Verenigde Staten) | Man | `en-US-BenjaminRUS` |
| `en-US` | Engels (Verenigde Staten) | Man | `en-US-GuyRUS` |
| `en-US` | Engels (Verenigde Staten) | Vrouw | `en-US-JessaRUS` |
| `en-US` | Engels (Verenigde Staten) | Vrouw | `en-US-ZiraRUS` |
| `es-ES` | Spaans (Spanje) | Vrouw | `es-ES-HelenaRUS` |
| `es-ES` | Spaans (Spanje) | Vrouw | `es-ES-Laura` |
| `es-ES` | Spaans (Spanje) | Man | `es-ES-Pablo` |
| `es-MX` | Spaans (Mexico) | Vrouw | `es-MX-HildaRUS` |
| `es-MX` | Spaans (Mexico) | Man | `es-MX-Raul` |
| `fi-FI` | Fins (Finland) | Vrouw | `fi-FI-HeidiRUS` |
| `fr-CA` | Frans (Canada) | Vrouw | `fr-CA-Caroline` |
| `fr-CA` | Frans (Canada) | Vrouw | `fr-CA-HarmonieRUS` |
| `fr-CH` | Frans (Zwitserland) | Man | `fr-CH-Guillaume` |
| `fr-FR` | Frans (Frankrijk) | Vrouw | `fr-FR-HortenseRUS` |
| `fr-FR` | Frans (Frankrijk) | Vrouw | `fr-FR-Julie` |
| `fr-FR` | Frans (Frankrijk) | Man | `fr-FR-Paul` |
| `he-IL` | Hebreeuws (Israël) | Man | `he-IL-Asaf` |
| `hi-IN` | Hindi (India) | Man | `hi-IN-Hemant` |
| `hi-IN` | Hindi (India) | Vrouw | `hi-IN-Kalpana` |
| `hr-HR` | Kroatisch (Kroatië) | Man | `hr-HR-Matej` |
| `hu-HU` | Hongaars (Hongarije) | Man | `hu-HU-Szabolcs` |
| `id-ID` | Indonesisch (Indonesië) | Man | `id-ID-Andika` |
| `it-IT` | Italiaans (Italië) | Man | `it-IT-Cosimo` |
| `it-IT` | Italiaans (Italië) | Vrouw | `it-IT-LuciaRUS` |
| `ja-JP` | Japans (Japan) | Vrouw | `ja-JP-Ayumi` |
| `ja-JP` | Japans (Japan) | Vrouw | `ja-JP-HarukaRUS` |
| `ja-JP` | Japans (Japan) | Man | `ja-JP-Ichiro` |
| `ko-KR` | Koreaans (Zuid-Korea) | Vrouw | `ko-KR-HeamiRUS` |
| `ms-MY` | Maleis (Maleisië) | Man | `ms-MY-Rizwan` |
| `nb-NO` | Noors, Bokmål (Noor wegen) | Vrouw | `nb-NO-HuldaRUS` |
| `nl-NL` | Nederlands (Nederland) | Vrouw | `nl-NL-HannaRUS` |
| `pl-PL` | Pools (Polen) | Vrouw | `pl-PL-PaulinaRUS` |
| `pt-BR` | Portugees (Brazilië) | Man | `pt-BR-Daniel` |
| `pt-BR` | Portugees (Brazilië) | Vrouw | `pt-BR-HeloisaRUS` |
| `pt-PT` | Portugees (Portugal) | Vrouw | `pt-PT-HeliaRUS` |
| `ro-RO` | Roemeens (Roemenië) | Man | `ro-RO-Andrei` |
| `ru-RU` | Russisch (Rusland) | Vrouw | `ru-RU-EkaterinaRUS` |
| `ru-RU` | Russisch (Rusland) | Vrouw | `ru-RU-Irina` |
| `ru-RU` | Russisch (Rusland) | Man | `ru-RU-Pavel` |
| `sk-SK` | Slowaaks (Slowakije) | Man | `sk-SK-Filip` |
| `sl-SI` | Sloveens (Slovenië) | Man | `sl-SI-Lado` |
| `sv-SE` | Zweeds (Zweden) | Vrouw | `sv-SE-HedvigRUS` |
| `ta-IN` | Tamil (India) | Man | `ta-IN-Valluvar` |
| `te-IN` | Telugu (India) | Vrouw | `te-IN-Chitra` |
| `th-TH` | Thai (Thailand) | Man | `th-TH-Pattara` |
| `tr-TR` | Turks (Turkije) | Vrouw | `tr-TR-SedaRUS` |
| `vi-VN` | Vietnamees (Vietnam) | Man | `vi-VN-An` |
| `zh-CN` | Mandarijn (vereenvoudigd Chinees, China) | Vrouw | `zh-CN-HuihuiRUS` |
| `zh-CN` | Mandarijn (vereenvoudigd Chinees, China) | Man | `zh-CN-Kangkang` |
| `zh-CN` | Mandarijn (vereenvoudigd Chinees, China) | Vrouw | `zh-CN-Yaoyao` |
| `zh-HK` | Kantonees (traditioneel Chinees, Hongkong) | Man | `zh-HK-Danny` |
| `zh-HK` | Kantonees (traditioneel Chinees, Hongkong) | Vrouw | `zh-HK-TracyRUS` |
| `zh-TW` | Mandarijn (traditioneel Chinees, Taiwan) | Vrouw | `zh-TW-HanHanRUS` |
| `zh-TW` | Mandarijn (traditioneel Chinees, Taiwan) | Vrouw | `zh-TW-Yating` |
| `zh-TW` | Mandarijn (traditioneel Chinees, Taiwan) | Man | `zh-TW-Zhiwei` |

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
