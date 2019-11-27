---
title: Taal ondersteuning-spraak service
titleSuffix: Azure Cognitive Services
description: De spraak service ondersteunt talloze talen voor conversie van spraak naar tekst en tekst naar spraak, samen met spraak omzetting. In dit artikel vindt u een uitgebreide lijst met taal ondersteuning per service functie.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 87b065d2ecf9d80c072918c14e7861f72b8d7aba
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74304906"
---
# <a name="language-and-region-support-for-the-speech-services"></a>Taal-en regio ondersteuning voor de spraak Services

Taal ondersteuning is afhankelijk van de functionaliteit van de spraak service. In de volgende tabellen vindt u een overzicht van taal ondersteuning voor service aanbiedingen voor [spraak naar tekst](#speech-to-text), [tekst naar spraak](#text-to-speech)en [spraak omzetting](#speech-translation) .

## <a name="speech-to-text"></a>Spraak naar tekst

Zowel de micro soft Speech SDK als de REST API ondersteunen de volgende talen (land instellingen). Om de nauw keurigheid te verbeteren, wordt aanpassing aangeboden voor een subset van de talen via het uploaden van audio en Transcripten met menselijke labels of gerelateerde tekst: zinnen.  Het aanpassen van de uitspraak is momenteel alleen beschikbaar voor `en-US` en `de-DE`. Meer informatie over aanpassingen [vindt u hier](how-to-custom-speech.md).

 Landinstelling | Taal | Ondersteund | Aanpas bare
------|------------|-----------|-------------
`ar-EG` | Arabisch (Egypte), moderne standard | Ja | Ja
`ar-SA` | Arabisch (Saoedi-Arabië) | Ja | Ja
`ar-AE` | Arabisch (VAE) | Ja | Ja
`ar-KW` | Arabisch (Koeweit) | Ja | Ja
`ar-QA` | Arabisch (Qatar) | Ja | Ja
`ca-ES` | Catalaans | Ja | Nee
`da-DK` | Deens (Denemarken) | Ja | Nee
`de-DE` | Duits (Duitsland) | Ja | Ja
`en-AU` | Engels (Australië) | Ja | Ja
`en-CA` | Engels (Canada) | Ja | Ja
`en-GB` | Engels (Verenigd Koninkrijk) | Ja | Ja
`en-IN` | English (India) | Ja | Ja
`en-NZ` | Engels (Nieuw-Zeeland) | Ja | Ja
`en-US` | Engels (Verenigde Staten) | Ja | Ja
`es-ES` | Spaans (Spanje) | Ja | Ja
`es-MX` | Spaans (Mexico) | Ja | Ja
`fi-FI` | Fins (Finland) | Ja | Nee
`fr-CA` | Frans (Canada) | Ja | Ja
`fr-FR` | Frans (Frankrijk) | Ja | Ja
`gu-IN` | Gujarati (Indiase) | Ja | Ja
`hi-IN` | Hindi (India) | Ja | Ja
`it-IT` | Italiaans (Italië) | Ja | Ja
`ja-JP` | Japans (Japan) | Ja | Ja
`ko-KR` | Koreaans (Korea) | Ja | Ja
`mr-IN` | Marathi (India) | Ja | Ja
`nb-NO` | Noors (Bokmål) (Noorwegen) | Ja | Nee
`nl-NL` | Nederlands (Nederland) | Ja | Ja
`pl-PL` | Pools (Polen) | Ja | Nee
`pt-BR` | Portugees (Brazilië) | Ja | Ja
`pt-PT` | Portugees (Portugal) | Ja | Ja
`ru-RU` | Russisch (Rusland) | Ja | Ja
`sv-SE` | Zweeds (Zweden) | Ja | Nee
`ta-IN` | Tamil (India) | Ja | Ja
`te-IN` | Telugu (India) | Ja | Ja
`zh-CN` | Chinees (Mandarijn, vereenvoudigd) | Ja | Ja
`zh-HK` | Chinees (Kantonees, traditioneel) | Ja | Ja
`zh-TW` | Chinees (Taiwan Mandarijn) | Ja | Ja
`th-TH` | Thai (Thailand) | Ja | Nee
`tr-TR` | Turkije | Ja | Ja

## <a name="text-to-speech"></a>Tekst naar spraak

Zowel de micro soft Speech SDK als REST Api's ondersteunen deze stemmen, die elk een specifieke taal en dialect ondersteunt, geïdentificeerd door land instellingen.

> [!IMPORTANT]
> De prijzen zijn afhankelijk van de standaard, aangepaste en Neural stemmen. Ga naar de pagina met [prijzen](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) voor meer informatie.

### <a name="neural-voices"></a>Neural stemmen

Neural text-to-speech is een nieuw type spraak synthese dat wordt aangedreven door diepe Neural-netwerken. Wanneer u een Neural-stem gebruikt, is de gesynthesizerde spraak bijna niet van de menselijke opnamen onderscheiden.

Neural stemmen kunnen worden gebruikt om interacties te maken met chat bots uitbreiden en spraak assistenten die natuurlijk en aantrekkelijker zijn, en om digitale teksten, zoals e-books, te converteren naar Audiobooks en in-car navigatie systemen te verbeteren. Met het menselijke net zoals natuurlijke prosody en heldere afbakening van woorden, verlaagt Neural stemmen aanzienlijk op het Luis teren van vermoeidheid wanneer gebruikers met AI-systemen communiceren.

Zie [regio's](regions.md#standard-and-neural-voices)voor een volledige lijst met Neural stemmen en regionale Beschik baarheid.

Landinstelling | Taal | Geslacht | Volledige toewijzing van service naam | Korte spraak naam
--------|----------|--------|---------|------------
`de-DE` | Duits (Duitsland) | Vrouw | "Micro soft server Speech Text to Speech Voice (de-DE, KatjaNeural)" | "de-DE-KatjaNeural"
`en-US` | English (US) | Man | "Micro soft server Speech Text to Speech Voice (nl-nl, GuyNeural)" | "en-US-GuyNeural"
`en-US` | English (US) | Vrouw | "Micro soft server Speech Text to Speech Voice (nl-nl, JessaNeural)" | "en-US-JessaNeural"
`it-IT` | Italiaans (Italië) | Vrouw |"Micro soft server Speech Text to Speech Voice (IT-IT, ElsaNeural)" | ' it-IT-ElsaNeural '
`zh-CN` | Chinees (vasteland) | Vrouw | "Micro soft server Speech Text to Speech Voice (zh-CN, XiaoxiaoNeural)" | "zh-CN-XiaoxiaoNeural"

> [!NOTE]
> U kunt de volledige service naam toewijzing of de naam van de korte spraak gebruiken in uw verzoeken voor spraak synthese.

### <a name="standard-voices"></a>Standaard stemmen

Meer dan 75 standaard stemmen zijn beschikbaar in meer dan 45 talen en land instellingen, waarmee u tekst kunt converteren naar gesynthesizerde spraak. Zie [regio's](regions.md#standard-and-neural-voices)voor meer informatie over regionale Beschik baarheid.

Landinstelling | Taal | Geslacht | Volledige toewijzing van service naam | Korte naam
-------|----------|---------|----------|----------
<sup>&dagger;</sup>`ar-EG` | Arabisch (Egypte) | Vrouw | "Microsoft Server spraak tekst en spraak spraak (ar-bijvoorbeeld Hoda)" | "ar-EG-Hoda"
`ar-SA` | Arabisch (Saoedi-Arabië) | Man | "Microsoft Server spraak tekst en spraak, spraak (ar-SA, Naayf)" | "ar-SA-Naayf"
`bg-BG` | Bulgaars | Man | "Microsoft Server tekst naar spraak stem (bg-BG, Ivan)" | "bg-BG-Ivan"
`ca-ES` | Catalaans | Vrouw | "Microsoft Server tekst naar spraak stem (ca-ES, HerenaRUS)" | "ca-ES-HerenaRUS"
`cs-CZ` | Tsjechisch | Man | "Microsoft Server spraak tekst en spraak spraak (cs-CZ, Jakub)" | "cs-CZ-Jakub"
`da-DK` | Deens | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (da-DK, HelleRUS)" | "da-DK-HelleRUS"
`de-AT` | Duits (Oostenrijk) | Man | "Microsoft Server spraak tekst en spraak, spraak (de-AT, Michael)" | "de-Michael"
`de-CH` | Duits (Zwitserland) | Man | "Microsoft Server spraak tekst en spraak, spraak (de-h, Karsten)" | "de-CH-Karsten"
`de-DE` | Duits (Duitsland) | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (nl-nl, Hedda)" | "de-DE-Hedda"
| | | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (nl-nl, HeddaRUS)" | "de-DE-HeddaRUS"
| | | Man | "Microsoft Server spraak tekst en spraak, spraak (nl-nl, Stefan, Apollo)" | "de-DE-Stefan-Apollo"
`el-GR` | Grieks | Man | "Microsoft Server spraak tekst en spraak, spraak (el-GR, Stefanos)" | "El-GR-Stefanos"
`en-AU` | Engels (Australië) | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (en-AU, Catherine)" | "en-AU-Catherine"
| | | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (en-AU, HayleyRUS)" | "en-AU-HayleyRUS"
`en-CA` | Engels (Canada) | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (en-CA, Linda)" | "en-CA-Linda"
| | | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (en-CA, HeatherRUS)" | "en-CA-HeatherRUS"
`en-GB` | English (UK) | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (en-GB, Susan, Apollo)" | "en-GB-Susan-Apollo"
| | | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (en-GB, HazelRUS)" | "en-GB-HazelRUS"
| | | Man | "Microsoft Server spraak tekst en spraak, spraak (en-GB, George, Apollo)" | "en-GB-George-Apollo"
`en-IE` | Engels (Ierland) | Man | "Microsoft Server spraak tekst en spraak, spraak (en Internet Explorer, Jan)" | "en-IE-Johan"
`en-IN` | English (India) | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (en-IN, Heera, Apollo)" | "en-IN-heera-Apollo"
| | | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (en-IN, PriyaRUS)" | "en-IN-PriyaRUS"
| | | Man | "Microsoft Server spraak tekst en spraak, spraak (en-IN, Ravi, Apollo)" | "en-IN-Ravi-Apollo"
`en-US` | English (US) | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (en-US, ZiraRUS)" | "en-US-ZiraRUS"
| | | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (en-US, JessaRUS)" | "en-US-JessaRUS"
| | | Man | "Microsoft Server spraak tekst en spraak, spraak (en-US, BenjaminRUS)" | "en-US-BenjaminRUS"
| | | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (en-US, Jessa24kRUS)" | "en-US-Jessa24kRUS"
| | | Man | "Microsoft Server spraak tekst en spraak, spraak (en-US, Guy24kRUS)" | "en-US-Guy24kRUS"
`es-ES` | Spaans (Spanje) |Vrouw | "Microsoft Server tekst naar spraak stem (es-ES, Laura, Apollo)" | "es-ES-Laura-Apollo"
| | | Vrouw | "Microsoft Server tekst naar spraak stem (es-ES, HelenaRUS)" | "es-ES-HelenaRUS"
| | | Man | "Microsoft Server tekst naar spraak stem (es-ES, Pablo, Apollo)" | "es-ES-Pablo-Apollo"
`es-MX` | Spaans (Mexico) | Vrouw | "Microsoft Server-stem tekst naar spraak (es-MX, HildaRUS)" | "es-MX-HildaRUS"
| | | Man | "Microsoft Server-stem tekst naar spraak (es-MX, Raul, Apollo)" | "es-MX-Raul-Apollo"
`fi-FI` | Fins | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (fi-FI, HeidiRUS)" | "fi-FI-HeidiRUS"
`fr-CA` | Frans (Canada) |Vrouw | "Microsoft Server tekst naar spraak stem (fr-CA, Caroline)" | "FR-CA-Caroline"
| | | Vrouw | "Microsoft Server tekst naar spraak stem (fr-CA, HarmonieRUS)" | "FR-CA-HarmonieRUS"
`fr-CH` | Frans (Zwitserland)| Man | "Microsoft Server tekst naar spraak stem (fr-h, Guillaume)" | "FR-CH-Guillaume"
`fr-FR` | Frans (Frankrijk)| Vrouw | "Microsoft Server tekst naar spraak stem (fr-FR, Julia, Apollo)" | "fr-FR-Julie-Apollo"
| | | Vrouw | "Microsoft Server tekst naar spraak stem (fr-FR, HortenseRUS)" | "fr-FR-HortenseRUS"
| | | Man | "Microsoft Server tekst naar spraak stem (fr-FR, Paul, Apollo)" | "fr-FR-Paul-Apollo"
`he-IL` | Hebreeuws (Israël) | Man| "Microsoft Server spraak tekst en spraak, spraak (hij-IL, Asaf)" | "he IL-Asaf"
`hi-IN` | Hindi (India) | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (High-IN, Kalpana, Apollo)" | "Hi-IN-Kalpana-Apollo"
| | | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (High-IN, Kalpana)" | "Hi-IN-Kalpana"
| | | Man | "Microsoft Server spraak tekst en spraak, spraak (High-IN, Hemant)" | "Hi-IN-Hemant"
`hr-HR` | Kroatisch | Man | "Microsoft Server tekst naar spraak stem (hr-HR, Matej)" | "hr-HR-Matej"
`hu-HU` | Hongaars | Man | "Microsoft Server spraak tekst en spraak, spraak (hu-HU, Szabolcs)" | "hu-HU-Szabolcs"
`id-ID` | Indonesisch| Man | "Microsoft Server spraak tekst en spraak, spraak (id-ID, Andika)" | "id-ID-andika"
`it-IT` | Italiaans | Man | "Microsoft Server spraak tekst en spraak, spraak (it-IT, Cosimo, Apollo)" | "it-IT-Cosimo-Apollo"
| | | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (it-IT, LuciaRUS)" | "it-IT-LuciaRUS"
`ja-JP` | Japans | Vrouw | "Microsoft Server tekst naar spraak stem (ja-JP, Ayumi, Apollo)" | "ja-JP-Ayumi-Apollo"
| | | Man | "Microsoft Server tekst naar spraak stem (ja-JP, Ichiro, Apollo)" | "ja-JP-Ichiro-Apollo"
| | | Vrouw | "Microsoft Server tekst naar spraak stem (ja-JP, HarukaRUS)" | "ja-JP-HarukaRUS"
`ko-KR` | Koreaans | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (ko-KR, HeamiRUS)" | "ko-KR-HeamiRUS"
`ms-MY` | Maleis | Man | "Microsoft Server-stem tekst naar spraak (ms-mijn Rizwan)" | ' MS-MY-Rizwan '
`nb-NO` | Noors | Vrouw | "Microsoft Server-stem tekst naar spraak (nb-NO HuldaRUS)" | "nb-NO-HuldaRUS"
`nl-NL` | Nederlands | Vrouw | "Microsoft Server tekst naar spraak stem (nl-NL, HannaRUS)" | "nl-NL-HannaRUS"
`pl-PL` | Pools | Vrouw | "Microsoft Server tekst naar spraak stem (pl-PL, PaulinaRUS)" | "pl-PL-PaulinaRUS"
`pt-BR` | Portugees (Brazilië) | Vrouw | "Microsoft Server tekst naar spraak stem (pt-BR, HeloisaRUS)" | "pt-BR-HeloisaRUS"
| | | Man |"Microsoft Server tekst naar spraak stem (pt-BR, Daniel, Apollo)" | "pt-BR-Daniel-Apollo"
`pt-PT` | Portugees (Portugal) | Vrouw | "Microsoft Server tekst naar spraak stem (pt-PT, HeliaRUS)" | "pt-PT-HeliaRUS"
`ro-RO` | Roemeens | Man | "Microsoft Server spraak tekst en spraak, spraak (ro-RO, Andrei)" | "ro-RO-Andrei"
`ru-RU` |Russisch| Vrouw | "Microsoft Server tekst naar spraak stem (ru-RU, Irina, Apollo)" | "ru-RU-Irina-Apollo"
| | | Man | "Microsoft Server tekst naar spraak stem (ru-RU, Pavel, Apollo)" | "ru-RU-Pavel-Apollo"
| | | Vrouw | "Microsoft Server tekst naar spraak stem (ru-RU, EkaterinaRUS)" | ru-RU-EkaterinaRUS
`sk-SK` | Slowaaks | Man | "Microsoft Server tekst naar spraak stem (sk-SK, Filip)" | "sk-SK-Filip"
`sl-SI` | Sloveens | Man | "Microsoft Server tekst naar spraak stem (sl-SI, Lado)" | "sl-SI-Lado"
`sv-SE` | Zweeds | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (sv-SE, HedvigRUS)" | "sv-SE-HedvigRUS"
`ta-IN` | Tamil (India) | Man | "Microsoft Server spraak tekst en spraak, spraak (ta-IN, Valluvar)" | "TA-IN-Valluvar"
`te-IN` | Telugu (India) | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (te-IN, Chitra)" | "te-IN-Chitra"
`th-TH` | Thais | Man | "Microsoft Server tekst naar spraak stem (th-TH, Pattara)" | ' th-do-Pattara '
`tr-TR` | Turks | Vrouw | "Microsoft Server tekst naar spraak stem (tr-TR, SedaRUS)" | "tr-TR-SedaRUS"
`vi-VN` | Vietnamees | Man | "Microsoft Server-stem tekst naar spraak (vi-VN, een)" | "VI-VN-a"
`zh-CN` | Chinees (vasteland) | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (zh-CN, HuihuiRUS)" | "zh-CN-HuihuiRUS"
| | | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (zh-CN, Yaoyao, Apollo)" | "zh-CN-Yaoyao-Apollo"
| | | Man | "Microsoft Server spraak tekst en spraak, spraak (zh-CN, Kangkang, Apollo)" | "zh-CN-Kangkang-Apollo"
`zh-HK` | Chinees (Hongkong SAR) | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (zh-HK Tracy, Apollo)" | "zh-HK-Tracy-Apollo"
| | | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (zh-HK TracyRUS)" | "zh-HK-TracyRUS"
| | | Man | "Microsoft Server spraak tekst en spraak, spraak (zh-HK Danny, Apollo)" | "zh-HK-Danny-Apollo"
`zh-TW` | Chinees (Taiwan) | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (zh-TW, Yating, Apollo)" | "zh-TW-Yating-Apollo"
| | | Vrouw | "Microsoft Server spraak tekst en spraak, spraak (zh-TW, HanHanRUS)" | "zh-TW-HanHanRUS"
| | | Man | "Microsoft Server spraak tekst en spraak, spraak (zh-TW, Zhiwei, Apollo)" | "zh-TW-Zhiwei-Apollo"

&dagger; *AR: ondersteunt moderne standaard Arabisch (MSA).*

> [!NOTE]
> U kunt de volledige service naam toewijzing of de naam van de korte spraak gebruiken in uw verzoeken voor spraak synthese.

### <a name="customization"></a>Aanpassing

Stem aanpassing is beschikbaar voor `de-DE`, `en-GB`, `en-IN`, `en-US`, `es-MX`, `fr-FR`, `it-IT`, `pt-BR`en `zh-CN`. Selecteer de juiste land instelling die overeenkomt met de trainings gegevens die u nodig hebt om een aangepast spraak model te trainen. Als de opname gegevens die u hebt gesp roken in het Engels met een Britse accent, selecteert u `en-GB`.

> [!NOTE]
> We bieden geen ondersteuning voor bidirectionele model trainingen in aangepaste spraak, met uitzonde ring van het Chinese Engelse bi-taal gebruik. Selecteer ' Chinees-Engels-tweetalig ' als u een Chinese stem wilt trainen die ook Engels kan spreken. Spraak training in alle land instellingen begint met een gegevensset van 2000 + uitingen, met uitzonde ring van de `en-US` en `zh-CN` waar u kunt beginnen met elke grootte van de trainings gegevens.

## <a name="speech-translation"></a>Spraakomzetting

De API voor **spraak omzetting** ondersteunt verschillende talen voor conversie van spraak naar spraak en spraak naar tekst. De bron taal moet altijd afkomstig zijn uit de tabel met spraak-naar-tekst taal. De beschikbare talen voor doel, is afhankelijk van of het doel van de vertaling spraak of tekst is. U kunt binnenkomende spraak omzetten in meer dan [60 talen](https://www.microsoft.com/translator/business/languages/). Er is een subset van talen beschikbaar voor [spraak synthese](language-support.md#text-languages).

### <a name="text-languages"></a>Tekst-talen

| Taal van de tekst    | Taalcode |
|:----------- |:-------------:|
| Afrikaans      | `af`          |
| Arabisch       | `ar`          |
| Bengalees      | `bn`          |
| Bosnisch (Latijns)      | `bs`          |
| Bulgaars      | `bg`          |
| Kantonees (Traditioneel)      | `yue`          |
| Catalaans      | `ca`          |
| Vereenvoudigd Chinees      | `zh-Hans`          |
| Traditioneel Chinees      | `zh-Hant`          |
| Kroatisch      | `hr`          |
| Tsjechisch      | `cs`          |
| Deens      | `da`          |
| Nederlands      | `nl`          |
| Nederlands      | `en`          |
| Estisch      | `et`          |
| Fiji      | `fj`          |
| Filipijns      | `fil`          |
| Fins      | `fi`          |
| Frans      | `fr`          |
| Duits      | `de`          |
| Grieks      | `el`          |
| Haïtiaans      | `ht`          |
| Hebreeuws      | `he`          |
| Hindi      | `hi`          |
| Hmong Daw      | `mww`          |
| Hongaars      | `hu`          |
| Indonesisch      | `id`          |
| Italiaans      | `it`          |
| Japans      | `ja`          |
| Swahili      | `sw`          |
| Klingon      | `tlh`          |
| Klingon (plqaD)      | `tlh-Qaak`          |
| Koreaans      | `ko`          |
| Lets      | `lv`          |
| Litouws      | `lt`          |
| Malagassische      | `mg`          |
| Maleis      | `ms`          |
| Maltees      | `mt`          |
| Noors      | `nb`          |
| Perzisch      | `fa`          |
| Pools      | `pl`          |
| Portugees      | `pt`          |
| Queretaro Otomi      | `otq`          |
| Roemeens      | `ro`          |
| Russisch      | `ru`          |
| Samoaanse      | `sm`          |
| Servisch (Cyrillisch)      | `sr-Cyrl`          |
| Servisch (Latijns)      | `sr-Latn`          |
| Slowaaks     | `sk`          |
| Sloveens      | `sl`          |
| Spaans      | `es`          |
| Zweeds      | `sv`          |
| Tahitian      | `ty`          |
| Tamil      | `ta`          |
| Telugu      | `te`          |
| Thais      | `th`          |
| Tongan      | `to`          |
| Turks      | `tr`          |
| Oekraïens      | `uk`          |
| Urdu      | `ur`          |
| Vietnamees      | `vi`          |
| Welsh      | `cy`          |
| Yucatec Maya      | `yua`          |


## <a name="next-steps"></a>Volgende stappen

* [Uw proefabonnement voor Speech Services ophalen](https://azure.microsoft.com/try/cognitive-services/)
* [Zie spraak herkennen inC#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
