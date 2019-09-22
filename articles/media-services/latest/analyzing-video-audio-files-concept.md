---
title: Video-en audio bestanden analyseren met Azure Media Services | Microsoft Docs
description: Wanneer u Azure Media Services gebruikt, kunt u uw audio-en video-inhoud analyseren met behulp van AudioAnalyzerPreset en VideoAnalyzerPreset.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/21/2019
ms.author: juliako
ms.openlocfilehash: bc4be8eaafe805e5d9a985b005efe80bc4af1d21
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2019
ms.locfileid: "71177996"
---
# <a name="analyzing-video-and-audio-files"></a>Video-en audio bestanden analyseren

Met Azure Media Services v3 kunt u inzichten uit uw video-en audio bestanden extra heren met Video Indexer via Media Services v3 Analyzer-voor waarden (beschreven in dit artikel). Als u meer gedetailleerde inzichten wilt, gebruikt u Video Indexer rechtstreeks. Als u het verschil wilt weten tussen het gebruik van instellingen van Video Indexer en Media Services, raadpleegt u het [vergelijkingsdocument](../video-indexer/compare-video-indexer-with-media-services-presets.md).

Als u uw inhoud wilt analyseren met Media Services v3-voor instellingen, maakt u een **trans formatie** en verzendt u een **taak** die gebruikmaakt van een van deze voor instellingen: [VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset) of **AudioAnalyzerPreset**. In het volgende artikel ziet u hoe u **VideoAnalyzerPreset**gebruikt: [Vind Analyseer Video's met Azure Media Services](analyze-videos-tutorial-with-api.md).

> [!NOTE]
> Wanneer u voorinstellingen voor een Video of Audio Analyzer gebruikt, moet u de Azure-portal gebruiken om uw account in te stellen op 10 S3 Door media gereserveerde eenheden. Zie [Mediaverwerking schalen](media-reserved-units-cli-how-to.md) voor meer informatie.

## <a name="built-in-presets"></a>Ingebouwde voorinstellingen

Media Services ondersteunt momenteel de volgende ingebouwde Analyzer-voor waarden:  

|**Vooraf ingestelde naam**|**Scenario**|**Details**|
|---|---|---|
|[AudioAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset)|Audio analyseren|De voor instelling past een vooraf gedefinieerde set op AI-gebaseerde analyse bewerkingen toe, waaronder spraak transcriptie. Op dit moment ondersteunt de vooraf ingestelde verwerking van inhoud met één audio track die spraak in één taal bevat. U kunt de taal voor de audio lading in de invoer opgeven met de BCP-47-indeling van de taal code-regio. Ondersteunde talen zijn Engels (' en-US ' en ' nl-GB '), Spaans (' es-ES ' en ' es-MX '), Frans (' fr-FR '), Italiaans (' it-IT '), Japans (' ja-JP '), Portugees (' pt-BR '), Chinees (' zh-CN '), Duits (' de '), Arabisch (' ar-EG '), Russisch (' ru-RU '), Hindi (' Hi-IN ' ) en Koreaans (' ko-KR ').<br/><br/> Als de taal niet is opgegeven of is ingesteld op NULL, kiest automatische taal detectie de eerste taal gedetecteerd en wordt de geselecteerde taal verwerkt voor de duur van het bestand. De functie voor automatische taal detectie ondersteunt momenteel Engels, Chinees, Frans, Duits, Italiaans, Japans, Spaans, Russisch en Portugees. Het biedt momenteel geen ondersteuning voor dynamisch overschakelen tussen talen nadat de eerste taal is gedetecteerd. De functie voor automatische taal detectie werkt het beste met geluids opnamen met duidelijk waarneembaar spraak. Als de taal niet kan worden gevonden met de automatische taal detectie, gaat de transcriptie terug naar Engels.|
|[VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset)|Audio en video analyseren|Extraheert inzichten (Rich meta data) van audio en video en voert een JSON-indelings bestand uit. U kunt opgeven of u alleen geluids inzichten wilt extra heren tijdens het verwerken van een video bestand. Zie [video analyseren](analyze-videos-tutorial-with-api.md)voor meer informatie.|
|[FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset)|Alle gezichten detecteren die in de video aanwezig zijn|Hierin worden de instellingen beschreven die moeten worden gebruikt bij het analyseren van een video om alle gezichten te detecteren die aanwezig zijn.|

### <a name="audioanalyzerpreset"></a>AudioAnalyzerPreset

Met de voor instelling kunt u meerdere geluids inzichten uit een audio-of video bestand ophalen. De uitvoer bevat een JSON-bestand (met alle inzichten) en het VTT-bestand voor de transcriptie van audio. Deze standaard instelling accepteert een eigenschap die de taal van het invoer bestand opgeeft in de vorm van een [BCP47](https://tools.ietf.org/html/bcp47) teken reeks. De geluids inzichten zijn onder andere:

* Audio-Transcriptie: een transcriptie van de gesp roken woorden met tijds tempels. Meerdere talen worden ondersteund
* Sprekers indexering: een toewijzing van de luid sprekers en de bijbehorende gesp roken woorden
* Speech sentiment-analyse: de uitvoer van sentiment-analyse die wordt uitgevoerd op de audio-Transcriptie
* Tref woorden: tref woorden die worden geëxtraheerd uit de audio-transcriptie.

### <a name="videoanalyzerpreset"></a>VideoAnalyzerPreset

Met de voor instelling kunt u meerdere audio-en video inzichten uit een video bestand ophalen. De uitvoer bevat een JSON-bestand (met alle inzichten), een VTT-bestand voor de getranscripteerde video en een verzameling miniaturen. Deze vooraf ingestelde accepteert ook een [BCP47](https://tools.ietf.org/html/bcp47) teken reeks (die de taal van de video vertegenwoordigt) als een eigenschap. De video inzichten bevatten alle hierboven genoemde audio inzichten en de volgende bijkomende items:

* Gezichts tracking: de tijd gedurende welke gezichten aanwezig zijn in de video. Elk gezicht heeft een face-id en een bijbehorende verzameling miniaturen
* Visuele tekst: de tekst die wordt gedetecteerd via optische teken herkenning. De tekst is een tijds tempel en wordt ook gebruikt voor het extra heren van tref woorden (naast het transcript voor audio)
* Keyframes: een verzameling keyframes die uit de video worden geëxtraheerd
* Visuele toezicht op inhoud: het gedeelte van de Video's dat als volwassene of ongepaste is gemarkeerd
* Aantekening: een resultaat van het aantekeningen maken van de Video's op basis van een vooraf gedefinieerd object model

##  <a name="insightsjson-elements"></a>inzichten. json-elementen

De uitvoer bevat een JSON-bestand (Insights. json) met alle inzichten die in de video of audio zijn gevonden. De JSON kan de volgende elementen bevatten:

### <a name="transcript"></a>verslag

|Name|Description|
|---|---|
|id|De regel-ID.|
|text|De transcriptie zelf.|
|language|De transcript taal. Bedoeld ter ondersteuning van transcripten waarbij elke regel een andere taal kan hebben.|
|instanties|Een lijst met peri Oden waarin deze regel wordt weer gegeven. Als de instantie een transcript heeft, heeft deze slechts één exemplaar.|

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

### <a name="ocr"></a>optische

|Name|Description|
|---|---|
|id|De OCR-regel-ID.|
|text|De OCR-tekst.|
|vallen|Het vertrouwens niveau van de herkenning.|
|language|De OCR-taal.|
|instanties|Een lijst met peri Oden waarin deze OCR is verschenen (dezelfde OCR kan meerdere keren voor komen).|

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

### <a name="faces"></a>gezichten

|Name|Description|
|---|---|
|id|De face-ID.|
|name|De naam van het gezicht. Dit kan ' onbekend #0 ', een geïdentificeerde beroemdheden of een door de klant getrainde persoon zijn.|
|vallen|De gezichts-id-betrouw baarheid.|
|description|Een beschrijving van de beroemdheden. |
|thumbnailId|De ID van de miniatuur van het gezicht.|
|knownPersonId|De interne ID van een bekende persoon.|
|referenceId|Als het een Bing-beroemdheden is, is dit de Bing-ID.|
|Type|Momenteel alleen Bing.|
|title|Als het een beroemdheden is, is dit de titel (bijvoorbeeld ' micro soft CEO ').|
|imageUrl|Als het een beroemdheden is, wordt de afbeeldings-URL.|
|instanties|Dit zijn exemplaren van waar het gezicht zich in het opgegeven tijds bereik bevindt. Elk exemplaar heeft ook een thumbnailsId. |

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

### <a name="shots"></a>afzonderlijke

|Name|Description|
|---|---|
|id|De opname-ID.|
|keyFrames|Een lijst met keyframes in de foto (elk heeft een ID en een lijst met tijds bereik exemplaren). Key frames-instanties hebben een thumbnailId-veld met de miniatuur-ID van het keyframe.|
|instanties|Een lijst met tijds bereiken van deze opname (afbeeldingen hebben slechts één exemplaar).|

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

### <a name="statistics"></a>autoriteiten

|Name|Description|
|---|---|
|CorrespondenceCount|Aantal correspondentie in de video.|
|WordCount|Het aantal woorden per spreker.|
|SpeakerNumberOfFragments|De hoeveelheid fragmenten die de spreker in een video heeft.|
|SpeakerLongestMonolog|De langste monolog van de spreker. Als de spreker stiltes bevat in de monolog, is deze opgenomen. Stilte aan het begin en het einde van de monolog wordt verwijderd.| 
|SpeakerTalkToListenRatio|De berekening is gebaseerd op de tijd die is besteed aan de monolog van de spreker (zonder de stilte in tussen) gedeeld door de totale tijd van de video. De tijd wordt afgerond op het derde decimale punt.|


### <a name="sentiments"></a>stemmingen

Gevoel worden geaggregeerd met het veld sentimentType (positief/neutraal/negatief). Bijvoorbeeld: 0-0,1, 0,1-0,2.

|Name|Description|
|---|---|
|id|De sentiment-ID.|
|averageScore |Het gemiddelde van alle scores van alle exemplaren van dat sentiment type-positief/neutraal/negatief|
|instanties|Een lijst met tijds bereikn waar deze sentiment verscheen.|
|sentimentType |Het type kan ' positief ', ' neutraal ' of ' negatief ' zijn.|

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

|Name|Description|
|---|---|
|id|De label-ID.|
|name|De naam van het label (bijvoorbeeld ' computer ', ' TV ').|
|language|De naam taal van het label (bij omzetting). BCP-47|
|instanties|Een lijst met tijds bereiken waar dit label wordt weer gegeven (een label kan meerdere keren voor komen). Elk exemplaar heeft een veld betrouw baarheid. |


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

|Name|Description|
|---|---|
|id|De ID van het sleutel woord.|
|text|De tekst van het sleutel woord.|
|vallen|Het vertrouwens niveau van het tref woord.|
|language|De taal van het tref woord (tijdens het vertalen).|
|instanties|Een lijst met tijds bereiken waar dit tref woord is opgetreden (een tref woord kan meerdere keren voor komen).|

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

Het visualContentModeration-blok bevat Peri Oden die Video Indexer mogelijk inhoud voor volwassenen hebben gevonden. Als visualContentModeration leeg is, is er geen inhoud voor volwassenen gevonden.

Video's die een inhoud van volwassenen of ongepaste bevatten, zijn mogelijk alleen beschikbaar voor de persoonlijke weer gave. Gebruikers hebben de mogelijkheid om een aanvraag in te dienen voor een menselijke beoordeling van de inhoud. in dat geval zal het kenmerk IsAdult het resultaat van de beoordeling van de mens bevatten.

|Name|Description|
|---|---|
|id|De controle-ID van de visuele inhoud.|
|adultScore|De volwassen Score (van content moderator).|
|racyScore|De ongepaste-Score (van inhouds toezicht).|
|instanties|Een lijst met peri Oden waarin deze visuele elementen worden gevisualiseerd.|

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
