---
title: Video- en audiobestanden analyseren
titleSuffix: Azure Media Services
description: Meer informatie over het analyseren van audio- en video-inhoud met AudioAnalyzerPreset en VideoAnalyzerPreset in Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/30/2020
ms.author: juliako
ms.openlocfilehash: 1d28fc37b98493322b9e201ac899b7911dd1d705
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269885"
---
# <a name="analyze-video-and-audio-files-with-azure-media-services"></a>Video- en audiobestanden analyseren met Azure Media Services

Met Azure Media Services v3 u met Video Indexer inzichten uit uw video- en audiobestanden halen. In dit artikel worden de v3-voorinstellingen van Media Services beschreven die worden gebruikt om deze inzichten te extraheren. Als u meer gedetailleerde inzichten wilt, gebruikt u Video Indexer rechtstreeks. Als u wilt weten wanneer u voorinstellingen voor video-indexer versus mediaservices-analyzer moet gebruiken, raadpleegt u het [vergelijkingsdocument](../video-indexer/compare-video-indexer-with-media-services-presets.md).

Als u uw inhoud wilt analyseren met Media Services v3-voorinstellingen, maakt u een **transformatie** en verzendt u een **taak** die een van deze voorinstellingen gebruikt: [VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset) of **AudioAnalyzerPreset**. Zie [Video's](analyze-videos-tutorial-with-api.md)analyseren met Azure Media Services voor een zelfstudie waarin wordt uitgelegd hoe **videoanalyzerpreset**kan worden gebruikt.

> [!NOTE]
> Wanneer u voorinstellingen voor een Video of Audio Analyzer gebruikt, moet u de Azure-portal gebruiken om uw account in te stellen op 10 S3 Door media gereserveerde eenheden. Zie [Mediaverwerking schalen](media-reserved-units-cli-how-to.md) voor meer informatie.

## <a name="compliance-privacy-and-security"></a>Compliance, privacy en beveiliging

Als een belangrijke herinnering moet u voldoen aan alle toepasselijke wetten bij uw gebruik van Video Indexer en mag u Video Indexer of een andere Azure-service niet gebruiken op een manier die de rechten van anderen schendt of schadelijk kan zijn voor anderen. Voordat u video's, inclusief biometrische gegevens, uploadt naar de Video Indexer-service voor verwerking en opslag, moet u over alle juiste rechten beschikken, inclusief alle juiste toestemmingen, van de persoon(en) in de video. Voor meer informatie over compliance, privacy en beveiliging in Video Indexer, de Voorwaarden van Microsoft [Cognitive Services](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/). Voor de privacyverplichtingen van Microsoft en de verwerking van uw gegevens raadpleegt u de [privacyverklaring](https://privacy.microsoft.com/PrivacyStatement)van Microsoft, de [voorwaarden voor onlineservices](https://www.microsoft.com/licensing/product-licensing/products) (OST) en [het addendum voor gegevensverwerking](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) (DPA"). Aanvullende privacy-informatie, waaronder over het bewaren van gegevens, verwijdering / vernietiging, is beschikbaar in de OST en [hier](../video-indexer/faq.md). Door Video Indexer te gebruiken, stemt u ermee in gebonden te zijn aan de Voorwaarden voor cognitieve services, de OST, DPA en de privacyverklaring.

## <a name="built-in-presets"></a>Ingebouwde voorinstellingen

Media Services ondersteunt momenteel de volgende ingebouwde analyzer-voorinstellingen:  

|**Vooraf ingestelde naam**|**Scenario**|**Details**|
|---|---|---|
|[AudioAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset)|Audio analyseren|De voorinstelling past een vooraf gedefinieerde set ai-gebaseerde analysebewerkingen toe, waaronder spraaktranscriptie. Momenteel ondersteunt de voorinstelling het verwerken van inhoud met één audiotrack die spraak in één taal bevat. U de taal voor de audiopayload in de invoer opgeven met behulp van de BCP-47-indeling van 'language tag-region'. Ondersteunde talen zijn Engels ('en-US'), Portugees ('pt-BR'), Spaans ('es-ES' en 'es-MX'), Frans ('fr-FR'), Italiaans ('it-IT'), Japans ('ja-JP'), Portugees ('pt-BR'), Chinees ('zh-CN'), Duits ('de-DE'), Arabisch ('ar-EG' en 'ar-SY'), Russisch ('ru-RU'), Russisch ('ru-RU'), Hindi ('hi-IN') en Koreaans ('ko-KR').<br/><br/> Als de taal niet is opgegeven of ingesteld op null, kiest automatische taaldetectie de eerste taal gedetecteerd en gaat verder met de geselecteerde taal voor de duur van het bestand. De automatische taaldetectiefunctie ondersteunt momenteel Engels, Chinees, Frans, Duits, Italiaans, Japans, Spaans, Russisch en Portugees. Het ondersteunt geen dynamisch schakelen tussen talen nadat de eerste taal is gedetecteerd. De automatische taaldetectiefunctie werkt het beste met audio-opnamen met duidelijk waarneembare spraak. Als automatische taaldetectie de taal niet vindt, valt de transcriptie terug naar het Engels.|
|[VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset)|Audio en video analyseren|Haalt inzichten (uitgebreide metagegevens) uit zowel audio als video en levert een JSON-indelingsbestand op. U opgeven of u alleen audio-inzichten wilt extraheren bij het verwerken van een videobestand. Zie [Video analyseren voor](analyze-videos-tutorial-with-api.md)meer informatie.|
|[FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset)|Gezichten detecteren die aanwezig zijn in video|Beschrijft de instellingen die moeten worden gebruikt bij het analyseren van een video om alle aanwezige gezichten te detecteren.|

### <a name="audioanalyzerpreset"></a>AudioAnalyzerPreset

Met de voorinstelling u meerdere audio-inzichten uit een audio- of videobestand halen. De uitvoer bevat een JSON-bestand (met alle inzichten) en VTT-bestand voor het audiotranscript. Deze voorinstelling accepteert een eigenschap die de taal van het invoerbestand opgeeft in de vorm van een [BCP47-tekenreeks.](https://tools.ietf.org/html/bcp47) De audio-inzichten omvatten:

* **Audiotranscriptie**: Een transcriptie van de gesproken woorden met tijdstempels. Meerdere talen worden ondersteund.
* **Luidsprekerindexering**: Een mapping van de sprekers en de bijbehorende gesproken woorden.
* **Spraaksentimentanalyse**: De output van sentimentanalyse uitgevoerd op de audiotranscriptie.
* **Trefwoorden:** Trefwoorden die uit de audiotranscriptie worden gehaald.

### <a name="videoanalyzerpreset"></a>VideoAnalyzerPreset

Met de voorinstelling u meerdere audio- en video-inzichten uit een videobestand extraheren. De uitvoer bevat een JSON-bestand (met alle inzichten), een VTT-bestand voor het videotranscript en een verzameling miniaturen. Deze voorinstelling accepteert ook een [BCP47-tekenreeks](https://tools.ietf.org/html/bcp47) (die de taal van de video vertegenwoordigt) als eigenschap. De video-inzichten bevatten alle hierboven genoemde audio-inzichten en de volgende extra items:

* **Face tracking**: De tijd waarin gezichten aanwezig zijn in de video. Elk gezicht heeft een face ID en een bijbehorende verzameling miniaturen.
* **Visuele tekst**: De tekst die wordt gedetecteerd via optische tekenherkenning. De tekst is tijd gestempeld en ook gebruikt om zoekwoorden te extraheren (in aanvulling op de audio-transcript).
* **Hoofdframes:** een verzameling hoofdframes uit de video.
* **Visuele inhoudmatiging**: Het gedeelte van de video's gemarkeerd als volwassen of racy in de natuur.
* **Annotatie**: Een resultaat van het annoteren van de video's op basis van een vooraf gedefinieerd objectmodel

## <a name="insightsjson-elements"></a>insights.json-elementen

De uitvoer bevat een JSON-bestand (insights.json) met alle inzichten in de video of audio. Het JSON kan de volgende elementen bevatten:

### <a name="transcript"></a>Afschrift

|Name|Beschrijving|
|---|---|
|id|De lijn-ID.|
|tekst|Het transcript zelf.|
|language|De transcriptietaal. Bedoeld om transcript te ondersteunen waar elke regel een andere taal kan hebben.|
|Exemplaren|Een lijst met tijdsbereiken waar deze regel is weergegeven. Als de instantie transcript is, heeft deze slechts 1 exemplaar.|

Voorbeeld:

```json
"transcript": [
{
    "id": 0,
    "text": "Hi I'm Doug from office.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    }
    ]
},
{
    "id": 1,
    "text": "I have a guest. It's Michelle.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:02.7200000",
        "end": "00:00:03.9600000"
    }
    ]
}
] 
```

### <a name="ocr"></a>Ocr

|Name|Beschrijving|
|---|---|
|id|De OCR-lijn-ID.|
|tekst|De OCR-tekst.|
|betrouwbaarheid|Het erkenningsvertrouwen.|
|language|De OCR-taal.|
|Exemplaren|Een lijst met tijdbereiken waar deze OCR is weergegeven (dezelfde OCR kan meerdere keren worden weergegeven).|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 0.91,
      "language": "en-US",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    },
    {
      "id": 1,
      "text": "NOTICIAS EN VIVO",
      "confidence": 0.9,
      "language": "es-ES",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:28"
        },
        {
          "start": "00:00:32",
          "end": "00:00:38"
        }
      ]
    }
  ],
```

### <a name="faces"></a>Gezichten

|Name|Beschrijving|
|---|---|
|id|De gezichts-ID.|
|name|De gezichtsherkenning. Het kan 'Unknown #0', een geïdentificeerde beroemdheid of een klant opgeleid persoon.|
|betrouwbaarheid|Het gezichtsidentificatievertrouwen.|
|description|Een beschrijving van de beroemdheid. |
|thumbnailId|De ID van de miniatuur van dat gezicht.|
|knownPersonId|De interne ID (als het een bekende persoon).|
|referenceId|De Bing ID (als het een Bing beroemdheid).|
|referenceType|Momenteel alleen Bing.|
|titel|De titel (als het een beroemdheid is, bijvoorbeeld 'Microsoft's CEO').|
|imageUrl|De afbeelding URL, als het een beroemdheid.|
|Exemplaren|Gevallen waarin het gezicht in het opgegeven tijdsbereik verscheen. Elke instantie heeft ook een thumbnailsId. |

```json
"faces": [{
    "id": 2002,
    "name": "Xam 007",
    "confidence": 0.93844,
    "description": null,
    "thumbnailId": "00000000-aee4-4be2-a4d5-d01817c07955",
    "knownPersonId": "8340004b-5cf5-4611-9cc4-3b13cca10634",
    "referenceId": null,
    "title": null,
    "imageUrl": null,
    "instances": [{
        "thumbnailsIds": ["00000000-9f68-4bb2-ab27-3b4d9f2d998e",
        "cef03f24-b0c7-4145-94d4-a84f81bb588c"],
        "adjustedStart": "00:00:07.2400000",
        "adjustedEnd": "00:00:45.6780000",
        "start": "00:00:07.2400000",
        "end": "00:00:45.6780000"
    },
    {
        "thumbnailsIds": ["00000000-51e5-4260-91a5-890fa05c68b0"],
        "adjustedStart": "00:10:23.9570000",
        "adjustedEnd": "00:10:39.2390000",
        "start": "00:10:23.9570000",
        "end": "00:10:39.2390000"
    }]
}]
```

### <a name="shots"></a>Shots

|Name|Beschrijving|
|---|---|
|id|De shot ID.|
|Hoofdframes|Een lijst met hoofdframes in de opname (elk heeft een ID en een lijst met tijdsbereiken van instanties). Belangrijke frames-exemplaren hebben een miniatuurId-veld met de miniatuur-id van het hoofdframe.|
|Exemplaren|Een lijst van tijdbereiken van dit schot (schoten hebben slechts 1 exemplaar).|

```json
"Shots": [
    {
      "id": 0,
      "keyFrames": [
        {
          "id": 0,
          "instances": [
            {
                "thumbnailId": "00000000-0000-0000-0000-000000000000",
              "start": "00: 00: 00.1670000",
              "end": "00: 00: 00.2000000"
            }
          ]
        }
      ],
      "instances": [
        {
            "thumbnailId": "00000000-0000-0000-0000-000000000000",  
          "start": "00: 00: 00.2000000",
          "end": "00: 00: 05.0330000"
        }
      ]
    },
    {
      "id": 1,
      "keyFrames": [
        {
          "id": 1,
          "instances": [
            {
                "thumbnailId": "00000000-0000-0000-0000-000000000000",      
              "start": "00: 00: 05.2670000",
              "end": "00: 00: 05.3000000"
            }
          ]
        }
      ],
      "instances": [
        {
      "thumbnailId": "00000000-0000-0000-0000-000000000000",
          "start": "00: 00: 05.2670000",
          "end": "00: 00: 10.3000000"
        }
      ]
    }
  ]
```

### <a name="statistics"></a>statistieken

|Name|Beschrijving|
|---|---|
|CorrespondentieTelling|Aantal correspondenties in de video.|
|WordAantal|Het aantal woorden per spreker.|
|SpeakernumberofFragments|De hoeveelheid fragmenten die de spreker in een video heeft.|
|SpeakerLongestMonolog|De langste monlog van de spreker. Als de luidspreker stiltes in de monologen heeft, is deze inbegrepen. Stilte aan het begin en het einde van de monologen wordt verwijderd.|
|SpeakerTalkToListenRatio|De berekening is gebaseerd op de tijd die wordt besteed aan de monolog van de spreker (zonder de stilte ertussen) gedeeld door de totale tijd van de video. De tijd wordt afgerond op het derde decimaal punt.|


### <a name="sentiments"></a>Gevoelens

Sentimenten worden geaggregeerd door hun sentimentType veld (Positief/Neutraal/Negatief). Bijvoorbeeld 0-0,1, 0,1-0,2.

|Name|Beschrijving|
|---|---|
|id|De sentiment-ID.|
|gemiddeldScore |Het gemiddelde van alle scores van alle gevallen van dat sentimenttype - Positief/Neutraal/Negatief|
|Exemplaren|Een lijst van tijdsbereiken waar dit gevoel verscheen.|
|sentimentType |Het type kan 'Positief', 'Neutraal' of 'Negatief' zijn.|

```json
"sentiments": [
{
    "id": 0,
    "averageScore": 0.87,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:23",
        "end": "00:00:41"
    }
    ]
}, {
    "id": 1,
    "averageScore": 0.11,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:13",
        "end": "00:00:21"
    }
    ]
}
]
```

### <a name="labels"></a>labels

|Name|Beschrijving|
|---|---|
|id|De label-ID.|
|name|De labelnaam (bijvoorbeeld 'Computer', 'TV').|
|language|De naamtaal van het label (indien vertaald). BCP-47|
|Exemplaren|Een lijst met tijdsbereiken waar dit label is verschenen (een label kan meerdere keren worden weergegeven). Elke instantie heeft een vertrouwensveld. |

```json
"labels": [
    {
      "id": 0,
      "name": "person",
      "language": "en-US",
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 00.0000000",
          "end": "00: 00: 25.6000000"
        },
        {
          "confidence": 1.0,
          "start": "00: 01: 33.8670000",
          "end": "00: 01: 39.2000000"
        }
      ]
    },
    {
      "name": "indoor",
      "language": "en-US",
      "id": 1,
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 06.4000000",
          "end": "00: 00: 07.4670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 09.6000000",
          "end": "00: 00: 10.6670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 11.7330000",
          "end": "00: 00: 20.2670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 21.3330000",
          "end": "00: 00: 25.6000000"
        }
      ]
    }
  ] 
```

### <a name="keywords"></a>trefwoorden

|Name|Beschrijving|
|---|---|
|id|Het trefwoord-ID.|
|tekst|De trefwoordtekst.|
|betrouwbaarheid|Het erkenningsvertrouwen van het zoekwoord.|
|language|De trefwoordtaal (indien vertaald).|
|Exemplaren|Een lijst met tijdsbereiken waar dit zoekwoord is weergegeven (een zoekwoord kan meerdere keren worden weergegeven).|

```json
"keywords": [
{
    "id": 0,
    "text": "office",
    "confidence": 1.6666666666666667,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    },
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    }
    ]
},
{
    "id": 1,
    "text": "icons",
    "confidence": 1.4,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    },
    {
        "start": "00:00:13.9900000",
        "end": "00:00:15.6100000"
    }
    ]
}
] 
```

#### <a name="visualcontentmoderation"></a>visualContentModeration

Het visualContentModeration-blok bevat tijdsbereiken waarvan Video Indexer heeft vastgesteld dat deze mogelijk inhoud voor volwassenen heeft. Als visualContentModeration leeg is, is er geen inhoud voor volwassenen geïdentificeerd.

Video's waarvan wordt vastgesteld dat ze inhoud voor volwassenen of racy bevatten, zijn mogelijk alleen beschikbaar voor privéweergave. Gebruikers kunnen een verzoek indienen voor een menselijke beoordeling `IsAdult` van de inhoud, in welk geval het kenmerk het resultaat van de menselijke beoordeling zal bevatten.

|Name|Beschrijving|
|---|---|
|id|De visuele inhoudsmoderatie-ID.|
|adultScore (adultScore)|De score voor volwassenen (van inhoudsmoderator).|
|racyScore racyScore|De racy score (van inhoudmatiging).|
|Exemplaren|Een lijst met tijdsbereiken waar deze visuele inhoudsmoderatie is weergegeven.|

```json
"VisualContentModeration": [
{
    "id": 0,
    "adultScore": 0.00069,
    "racyScore": 0.91129,
    "instances": [
    {
        "start": "00:00:25.4840000",
        "end": "00:00:25.5260000"
    }
    ]
},
{
    "id": 1,
    "adultScore": 0.99231,
    "racyScore": 0.99912,
    "instances": [
    {
        "start": "00:00:35.5360000",
        "end": "00:00:35.5780000"
    }
    ]
}
] 
```
## <a name="next-steps"></a>Volgende stappen

[Zelfstudie: Video's analyseren met Azure Media Services](analyze-videos-tutorial-with-api.md)
