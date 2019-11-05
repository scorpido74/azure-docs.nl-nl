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
ms.date: 10/15/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: af8bb24862c05b232b7bb5d831b1eb3b1add3a7f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468807"
---
# <a name="language-and-region-support-for-the-speech-services"></a>Taal-en regio ondersteuning voor de spraak Services

Verschillende talen worden ondersteund voor verschillende functies van spraak Services. In de volgende tabellen vindt u een overzicht van taal ondersteuning.

## <a name="speech-to-text"></a>Spraak naar tekst

Zowel de micro soft Speech SDK als de REST API ondersteunen de volgende talen (land instellingen). Om de nauw keurigheid te verbeteren, wordt aanpassing aangeboden voor een subset van de talen via het uploaden van audio en Transcripten met menselijke labels of gerelateerde tekst: zinnen.  Aanpassing van de uitspraak is momenteel alleen beschikbaar voor en-US en de-DE. Meer informatie over aanpassingen [vindt u hier](how-to-custom-speech.md).

  Landinstelling | Taal | Ondersteund | Aanpas bare
 ------|----------|---------------------|---------------------
 AR-bijvoorbeeld | Arabisch (Egypte), modern Standard | Ja | Ja
 AR-SA | Arabisch (Saudi-Arabië) | Ja | Ja
 AR-AE | Arabisch (VAE) | Ja | Ja
 AR-KW | Arabisch (Koeweit) | Ja | Ja
 AR-QA | Arabisch (Qatar) | Ja | Ja
 CA-ES | Catalaans | Ja | Nee
 da-DK | Deens (Denemarken) | Ja | Nee
 de-DE | Duits (Duitsland) | Ja | Ja
 en-AU | Engels (Australië) | Ja | Ja
 en-CA | Engels (Canada) | Ja | Ja
 en-GB | Engels (Verenigd Konink rijk) | Ja | Ja
 en-IN | English (India) | Ja | Ja
 en-NZ | Engels (Nieuw-Zeeland) | Ja | Ja
 en-US | Engels (Verenigde Staten) | Ja | Ja
 es-ES | Spaans (Spanje) | Ja | Ja
 es-MX | Spaans (Mexico) | Ja | Ja
 fi-FI | Fins (Finland) | Ja | Nee
 FR-CA | Frans (Canada) | Ja | Ja
 fr-FR | Frans (Frank rijk) | Ja | Ja
 Gu-IN | Gujarati (Indiase) | Ja | Ja
 Hi-IN | Hindi (India) | Ja | Ja
 IT-IT | Italiaans (Italië) | Ja | Ja
 ja-JP | Japans (Japan) | Ja | Ja
 ko-KR | Koreaans (Korea) | Ja | Ja
 Mr-IN | Marathi (India) | Ja | Ja
 nb-NO | Noors (Bokmål) (Noor wegen) | Ja | Nee
 nl-NL | Nederlands (Nederland) | Ja | Ja
 pl-PL | Pools (Polen) | Ja | Nee
 pt-BR | Portugees (Brazilië) | Ja | Ja
 pt-PT | Portugees (Portugal) | Ja | Ja
 ru-RU | Russisch (Rusland) | Ja | Ja
 SV-SE | Zweeds (Zweden) | Ja | Nee
 ta | Tamil (India) | Ja | Ja
 te-IN | Telugu (India) | Ja | Ja
 zh-CN | Chinees (Mandarijn, vereenvoudigd) | Ja | Ja
 zh-HK | Chinees (Kantonees, traditioneel) | Ja | Ja
 zh-TW | Chinees (Taiwan Mandarijn) | Ja | Ja
 th | Thais (Thai land) | Ja | Nee
 tr-TR | Turkije | Ja | Ja |


## <a name="text-to-speech"></a>Tekst naar spraak

Zowel de micro soft Speech SDK als de REST API ondersteunt deze stemmen, die elk een specifieke taal en dialect ondersteunen, geïdentificeerd door land instellingen.

> [!IMPORTANT]
> De prijzen zijn afhankelijk van de standaard, aangepaste en Neural stemmen. Ga naar de pagina met [prijzen](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) voor meer informatie.

### <a name="neural-voices"></a>Neural stemmen

Neural text-to-speech is een nieuw type spraak synthese dat wordt aangedreven door diepe Neural-netwerken. Wanneer u een Neural-stem gebruikt, is de gesynthesizerde spraak bijna niet van de menselijke opnamen onderscheiden.

Neural stemmen kunnen worden gebruikt om interacties te maken met chat bots uitbreiden en spraak assistenten die natuurlijk en aantrekkelijker zijn, en om digitale teksten, zoals e-books, te converteren naar Audiobooks en in-car navigatie systemen te verbeteren. Met het menselijke net zoals natuurlijke prosody en heldere afbakening van woorden, verlaagt Neural stemmen aanzienlijk op het Luis teren van vermoeidheid wanneer gebruikers met AI-systemen communiceren.

Zie [regio's](regions.md#standard-and-neural-voices)voor een volledige lijst met Neural stemmen en regionale Beschik baarheid.

Landinstelling | Taal | Geslacht | Volledige toewijzing van service naam | Korte spraak naam
--------|----------|--------|---------|------------
de-DE | Duits (Duitsland) | Vrouw | "Micro soft server Speech Text to Speech Voice (de-DE, KatjaNeural)" | "de-DE-KatjaNeural"
en-US | English (US) | Man | "Micro soft server Speech Text to Speech Voice (nl-nl, GuyNeural)" | "en-US-GuyNeural"
en-US | English (US) | Vrouw | "Micro soft server Speech Text to Speech Voice (nl-nl, JessaNeural)" | "en-US-JessaNeural"
IT-IT | Italiaans (Italië) | Vrouw |"Micro soft server Speech Text to Speech Voice (IT-IT, ElsaNeural)" | ' it-IT-ElsaNeural '
zh-CN | Chinees (vasteland) | Vrouw | "Micro soft server Speech Text to Speech Voice (zh-CN, XiaoxiaoNeural)" | "zh-CN-XiaoxiaoNeural"

> [!NOTE]
> U kunt de volledige service naam toewijzing of de naam van de korte spraak gebruiken in uw verzoeken voor spraak synthese.

### <a name="standard-voices"></a>Standaard stemmen

Meer dan 75 standaard stemmen zijn beschikbaar in meer dan 45 talen en land instellingen, waarmee u tekst kunt converteren naar gesynthesizerde spraak. Zie [regio's](regions.md#standard-and-neural-voices)voor meer informatie over regionale Beschik baarheid.

Landinstelling | Taal | Geslacht | Volledige toewijzing van service naam | Korte spraak naam
-------|----------|---------|----------|----------
AR-bijvoorbeeld\* | Arabisch (Egypte) | Vrouw | "Micro soft server Speech Text to Speech Voice (AR-BIJV. Hoda)" | "ar-EG-Hoda"
AR-SA | Arabisch (Saudi-Arabië) | Man | "Micro soft server Speech Text to Speech Voice (AR-SA, Naayf)" | "AR-SA-Naayf"
bg-BG | Bulgaars | Man | "Micro soft server Speech Text to Speech Voice (bg-BG, Ivan)" | "bg-BG-Ivan"
CA-ES | Catalaans (Spanje) | Vrouw | "Micro soft server Speech Text to Speech Voice (ca-ES, HerenaRUS)" | "ca-ES-HerenaRUS"
CS-CZ | Tsjechisch | Man | "Micro soft server Speech Text to Speech Voice (CS-CZ, Jakub)" | "CS-CZ-Jakub"
da-DK | Deens | Vrouw | "Micro soft server Speech Text to Speech Voice (da-DK, HelleRUS)" | "da-DK-HelleRUS"
de-AT | Duits (Oosten rijk) | Man | "Micro soft server Speech Text to Speech Voice (de-AT, Michael)" | "de-Michael"
Dech | Duits (Zwitser land) | Man | "Micro soft server Speech Text to Speech Voice (de-CH, Karsten)" | "de-CH-Karsten"
de-DE | Duits (Duitsland) | Vrouw | "Micro soft server Speech Text to Speech Voice (de-DE, Hedda)" | "de-DE-Hedda"
| | | Vrouw | "Micro soft server Speech Text to Speech Voice (de-DE, HeddaRUS)" | "de-DE-HeddaRUS"
| | | Man | "Micro soft server Speech Text to Speech Voice (de-DE, Stefan, Apollo)" | "de-DE-Stefan-Apollo"
El-GR | Grieks | Man | "Micro soft server Speech Text to Speech Voice (El-GR, Stefanos)" | "El-GR-Stefanos"
en-AU | Engels (Australië) | Vrouw | "Micro soft server Speech Text to Speech Voice (en-AU, Catherine)" | "en-AU-Catherine"
| | | Vrouw | "Micro soft server Speech Text to Speech Voice (en-AU, HayleyRUS)" | "en-AU-HayleyRUS"
en-CA | Engels (Canada) | Vrouw | "Micro soft server Speech Text to Speech Voice (en-CA, Linda)" | "en-CA-Linda"
| | | Vrouw | "Micro soft server Speech Text to Speech Voice (en-CA, HeatherRUS)" | "en-CA-HeatherRUS"
en-GB | English (UK) | Vrouw | "Micro soft server Speech Text to Speech Voice (en-GB, Susan, Apollo)" | "en-GB-Susan-Apollo"
| | | Vrouw | "Micro soft server Speech Text to Speech Voice (en-GB, HazelRUS)" | "en-GB-HazelRUS"
| | | Man | "Micro soft server Speech Text to Speech Voice (en-GB, George, Apollo)" | "en-GB-George-Apollo"
en-IE | Engels (Ierland) | Man | "Micro soft server Speech Text to Speech Voice (en-IE, Johan)" | "en-IE-Johan"
en-IN | English (India) | Vrouw | "Micro soft server Speech Text to Speech Voice (en-IN, heera, Apollo)" | "en-IN-heera-Apollo"
| | | Vrouw | "Micro soft server Speech Text to Speech Voice (en-IN, PriyaRUS)" | "en-IN-PriyaRUS"
| | | Man | "Micro soft server Speech Text to Speech Voice (en-IN, Ravi, Apollo)" | "en-IN-Ravi-Apollo"
en-US | English (US) | Vrouw | "Micro soft server Speech Text to Speech Voice (nl-nl, ZiraRUS)" | "en-US-ZiraRUS"
| | | Vrouw | "Micro soft server Speech Text to Speech Voice (nl-nl, JessaRUS)" | "en-US-JessaRUS"
| | | Man | "Micro soft server Speech Text to Speech Voice (nl-nl, BenjaminRUS)" | "en-US-BenjaminRUS"
| | | Vrouw | "Micro soft server Speech Text to Speech Voice (nl-nl, Jessa24kRUS)" | "en-US-Jessa24kRUS"
| | | Man | "Micro soft server Speech Text to Speech Voice (nl-nl, Guy24kRUS)" | "en-US-Guy24kRUS"
es-ES | Spaans (Spanje) |Vrouw | "Micro soft server Speech Text to Speech Voice (es-ES, Laura, Apollo)" | "es-ES-Laura-Apollo"
| | | Vrouw | "Micro soft server Speech Text to Speech Voice (es-ES, HelenaRUS)" | "es-ES-HelenaRUS"
| | | Man | "Micro soft server Speech Text to Speech Voice (es-ES, Pablo, Apollo)" | "es-ES-Pablo-Apollo"
es-MX | Spaans (Mexico) | Vrouw | "Micro soft server Speech Text to Speech Voice (es-MX, HildaRUS)" | "es-MX-HildaRUS"
| | | Man | "Micro soft server Speech Text to Speech Voice (es-MX, Raul, Apollo)" | "es-MX-Raul-Apollo"
fi-FI | Fins | Vrouw | "Micro soft server Speech Text to Speech Voice (fi-FI, HeidiRUS)" | "fi-FI-HeidiRUS"
FR-CA | Frans (Canada) |Vrouw | "Micro soft server Speech Text to Speech Voice (FR-CA, Caroline)" | "FR-CA-Caroline"
| | | Vrouw | "Micro soft server Speech Text to Speech Voice (FR-CA, HarmonieRUS)" | "FR-CA-HarmonieRUS"
FR-CH | Frans (Zwitser land)| Man | "Micro soft server Speech Text to Speech Voice (FR-CH, Guillaume)" | "FR-CH-Guillaume"
fr-FR | Frans (Frank rijk)| Vrouw | "Micro soft server Speech Text to Speech Voice (fr-FR, Julie, Apollo)" | "fr-FR-Pascaline-Apollo"
| | | Vrouw | "Micro soft server Speech Text to Speech Voice (fr-FR, HortenseRUS)" | "fr-FR-HortenseRUS"
| | | Man | "Micro soft server Speech Text to Speech Voice (fr-FR, Paul, Apollo)" | "fr-FR-Paul-Apollo"
he IL| Hebreeuws (Israël) | Man| "Micro soft server Speech Text to Speech Voice (ik-IL, Asaf)" | "he IL-Asaf"
Hi-IN | Hindi (India) | Vrouw | "Micro soft server Speech Text to Speech Voice (Hi-IN, Kalpana, Apollo)" | "Hi-IN-Kalpana-Apollo"
| | |Vrouw | "Micro soft server Speech Text to Speech Voice (Hi-IN, Kalpana)" | "Hi-IN-Kalpana"
| | | Man | "Micro soft server Speech Text to Speech Voice (Hi-IN, Hemant)" | "Hi-IN-Hemant"
HR-HR | Kroatisch | Man | "Micro soft server Speech Text to Speech Voice (HR-HR, Matej)" | "HR-HR-Matej"
hu-HU | Hongaars | Man | "Micro soft server Speech Text to Speech Voice (hu-HU, Szabolcs)" | "hu-HU-Szabolcs"
id-ID | Indonesisch| Man | "Micro soft server Speech Text to Speech Voice (id-ID, andika)" | "id-ID-andika"
IT-IT | Italiaans | Man | "Micro soft server Speech Text to Speech Voice (IT-IT, Cosimo, Apollo)" | "it-IT-Cosimo-Apollo"
| | | Vrouw | "Micro soft server Speech Text to Speech Voice (IT-IT, LuciaRUS)" | ' it-IT-LuciaRUS '
ja-JP | Japans | Vrouw | "Micro soft server Speech Text to Speech Voice (ja-JP, Ayumi, Apollo)" | "ja-JP-Ayumi-Apollo"
| | | Man | "Micro soft server Speech Text to Speech Voice (ja-JP, Ichiro, Apollo)" | "ja-JP-Ichiro-Apollo"
| | | Vrouw | "Micro soft server Speech Text to Speech Voice (ja-JP, HarukaRUS)" | "ja-JP-HarukaRUS"
ko-KR | Koreaans | Vrouw | "Micro soft server Speech Text to Speech Voice (ko-KR, HeamiRUS)" | "ko-KR-HeamiRUS"
MS-mijn | Maleis | Man | "Micro soft server Speech Text to Speech Voice (MS-MY, Rizwan)" | ' MS-MY-Rizwan '
nb-NO | Noors | Vrouw | "Micro soft server Speech Text to Speech Voice (nb-NO, HuldaRUS)" | "nb-NO-HuldaRUS"
nl-NL | Nederlands | Vrouw | "Micro soft server Speech Text to Speech Voice (nl-NL, HannaRUS)" | "nl-NL-HannaRUS"
pl-PL | Pools | Vrouw | "Micro soft server Speech Text to Speech Voice (pl-PL, PaulinaRUS)" | "pl-PL-PaulinaRUS"
pt-BR | Portugees (Brazilië) | Vrouw | "Micro soft server Speech Text to Speech Voice (pt-BR, HeloisaRUS)" | "pt-BR-HeloisaRUS"
| | | Man |"Micro soft server Speech Text to Speech Voice (pt-BR, Apollo)" | "pt-BR-Apollo"
pt-PT | Portugees (Portugal) | Vrouw | "Micro soft server Speech Text to Speech Voice (pt-PT, HeliaRUS)" | "pt-PT-HeliaRUS"
RO-RO | Roemeens | Man | "Micro soft server Speech Text to Speech Voice (RO-RO, Andrei)" | "ro-RO-Andrei"
ru-RU |Russisch| Vrouw | "Micro soft server Speech Text to Speech Voice (ru-RU, Irina, Apollo)" | "ru-RU-Irina-Apollo"
| | | Man | "Micro soft server Speech Text to Speech Voice (ru-RU, Pavel, Apollo)" | "ru-RU-Pavel-Apollo"
| | | Vrouw | "Micro soft server Speech Text to Speech Voice (ru-RU, EkaterinaRUS)" | ru-RU-EkaterinaRUS
sk-SK | Slowaaks | Man | "Micro soft server Speech Text to Speech Voice (sk-SK, Filip)" | "sk-SK-Filip"
sl-SI | Sloveens | Man | "Micro soft server Speech Text to Speech Voice (sl-SI, lado)" | "sl-SI-lado"
SV-SE | Zweeds | Vrouw | "Micro soft server Speech Text to Speech Voice (sv-SE, HedvigRUS)" | "sv-SE-HedvigRUS"
ta | Tamil (India) | Man | "Micro soft server Speech Text to Speech Voice (TA-IN, Valluvar)" | "TA-IN-Valluvar"
te-IN | Telugu (India) | Vrouw | "Micro soft server Speech Text to Speech Voice (Chitra)" | "te-IN-Chitra"
th | Thais | Man | "Micro soft server Speech Text to Speech Voice (th-TH, Pattara)" | ' th-do-Pattara '
tr-TR | Turks | Vrouw | "Micro soft server Speech Text to Speech Voice (tr-TR, SedaRUS)" | "tr-TR-SedaRUS"
VI-VN | Vietnamees | Man | "Micro soft server Speech Text to Speech Voice (VI-VN, a)" | "VI-VN-a"
zh-CN | Chinees (vasteland) | Vrouw | "Micro soft server Speech Text to Speech Voice (zh-CN, HuihuiRUS)" | "zh-CN-HuihuiRUS"
| | | Vrouw | "Micro soft server Speech Text to Speech Voice (zh-CN, Yaoyao, Apollo)" | "zh-CN-Yaoyao-Apollo"
| | | Man | "Micro soft server Speech Text to Speech Voice (zh-CN, Kangkang, Apollo)" | "zh-CN-Kangkang-Apollo"
zh-HK | Chinees (Hongkong) | Vrouw | "Micro soft server Speech Text to Speech Voice (zh-HK, Tracy, Apollo)" | "zh-HK-Tracy-Apollo"
| | | Vrouw | "Micro soft server Speech Text to Speech Voice (zh-HK, TracyRUS)" | "zh-HK-TracyRUS"
| | | Man | "Micro soft server Speech Text to Speech Voice (zh-HK, Danny, Apollo)" | "zh-HK-Danny-Apollo"
zh-TW | Chinees (Taiwan) | Vrouw | "Micro soft server Speech Text to Speech Voice (zh-TW, Yating, Apollo)" | "zh-TW-Yating-Apollo"
| | | Vrouw | "Micro soft server Speech Text to Speech Voice (zh-TW, HanHanRUS)" | "zh-TW-HanHanRUS"
| | | Man | "Micro soft server Speech Text to Speech Voice (zh-TW, Zhiwei, Apollo)" | "zh-TW-Zhiwei-Apollo"

\* *AR: ondersteunt moderne standaard Arabisch (MSA).*

> [!NOTE]
> U kunt de volledige service naam toewijzing of de naam van de korte spraak gebruiken in uw verzoeken voor spraak synthese.

### <a name="customization"></a>Aanpassing

Stem aanpassing is beschikbaar voor de-DE, en-GB, en-IN, en-US, es-MX, fr-FR, it-IT, pt-BR en zh-CN. Selecteer de juiste land instelling die overeenkomt met de trainings gegevens die u nodig hebt om een aangepast spraak model te trainen. Als de opname gegevens die u hebt gesp roken in het Engels met een Britse accent, selecteert u en-GB.  

> [!NOTE]
> We bieden geen ondersteuning voor bidirectionele model trainingen in aangepaste spraak, met uitzonde ring van het Chinese Engelse bi-taal gebruik. Selecteer ' Chinees-Engels-tweetalig ' als u een Chinese stem wilt trainen die ook Engels kan spreken. Spraak training in alle land instellingen begint met een gegevensset van 2000 + uitingen, met uitzonde ring van de en-US en zh-CN, waar u kunt beginnen met elke grootte van de trainings gegevens.

## <a name="speech-translation"></a>Spraakomzetting

De API voor **spraak omzetting** ondersteunt verschillende talen voor conversie van spraak naar spraak en spraak naar tekst. De bron taal moet altijd afkomstig zijn uit de tabel met spraak-naar-tekst taal. De beschik bare doel talen zijn afhankelijk van het feit of het Vertaal doel spraak of tekst is. U kunt binnenkomende spraak omzetten in meer dan [60 talen](https://www.microsoft.com/translator/business/languages/). Een subset van deze talen is beschikbaar voor [spraak synthese](language-support.md#text-languages).

### <a name="text-languages"></a>Tekst talen

| Tekst taal    | Taalcode |
|:----------- |:-------------:|
| Afrikaans      | `af`          |
| Arabisch       | `ar`          |
| Bengalese      | `bn`          |
| Bosnisch (Latijns)      | `bs`          |
| Bulgaars      | `bg`          |
| Kantonees (traditioneel)      | `yue`          |
| Catalaans      | `ca`          |
| Vereenvoudigd Chinees      | `zh-Hans`          |
| Traditioneel Chinees      | `zh-Hant`          |
| Kroatisch      | `hr`          |
| Tsjechisch      | `cs`          |
| Deens      | `da`          |
| Nederlands      | `nl`          |
| Nederlands      | `en`          |
| Estisch      | `et`          |
| Fijian      | `fj`          |
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
| Samoan      | `sm`          |
| Servisch (Cyrillisch)      | `sr-Cyrl`          |
| Servisch (Latijns)      | `sr-Latn`          |
| Slowaaks     | `sk`          |
| Sloveens      | `sl`          |
| Spaans      | `es`          |
| Zweeds      | `sv`          |
| Tahitian      | `ty`          |
| Tamil      | `ta`          |
| Thais      | `th`          |
| Tongaanse      | `to`          |
| Turks      | `tr`          |
| Oekraïens      | `uk`          |
| Urdu      | `ur`          |
| Vietnamees      | `vi`          |
| Welsh      | `cy`          |
| Yucatec Maya      | `yua`          |


## <a name="next-steps"></a>Volgende stappen

* [Uw proefabonnement voor Speech Services ophalen](https://azure.microsoft.com/try/cognitive-services/)
* [Zie spraak herkennen in c #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
