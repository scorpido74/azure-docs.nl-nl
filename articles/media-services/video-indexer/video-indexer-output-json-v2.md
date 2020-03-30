---
title: De video-indexeruitvoer onderzoeken die wordt geproduceerd door v2-API - Azure
titleSuffix: Azure Media Services
description: In dit onderwerp wordt de uitvoer van Azure Media Services Video Indexer besproken door v2 API.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/09/2019
ms.author: juliako
ms.openlocfilehash: 2fac5e07f9646c4fc0fac7b1be53b5a5ac1ea803
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245926"
---
# <a name="examine-the-video-indexer-output-produced-by-api"></a>De video-indexeruitvoer onderzoeken die door API wordt geproduceerd

Wanneer u de API **voor videoindex opvragen** en de antwoordstatus in orde is, krijgt u een gedetailleerde JSON-uitvoer als antwoordinhoud. De JSON-inhoud bevat details van de opgegeven video-inzichten. De inzichten omvatten: transcripties, OcRs, gezichten, onderwerpen, blokken, enz. Elk inzichtstype bevat exemplaren van tijdbereiken die laten zien wanneer het inzicht in de video wordt weergegeven. 

1. Als u het JSON-bestand wilt ophalen, roept u [Videoindex-API op](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Index?)
1. Als u ook geïnteresseerd bent in specifieke artefacten, roept u [De URL-API voor downloaden van videoartefact en](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Artifact-Download-Url?)

    Geef in de API-aanroep het gevraagde artefacttype op (OCR, Gezichten, Hoofdframes enz.)

U ook de samengevatte inzichten van de video visueel bekijken door op de knop **Afspelen** op de video op de website [van Video Indexer](https://www.videoindexer.ai/) te drukken. Zie [Video-inzichten weergeven en bewerken](video-indexer-view-edit.md)voor meer informatie.

![Inzichten](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

In dit artikel wordt de JSON-inhoud onderzocht die is geretourneerd door de **API Voor videoindex worden** geretourneerd. 

> [!NOTE]
> Het verstrijken van alle toegangstokens in Video Indexer is een uur.


## <a name="root-elements"></a>Hoofdelementen

|Name|Beschrijving|
|---|---|
|accountId|De VI-account-id van de afspeellijst.|
|id|De id van de afspeellijst.|
|name|De naam van de afspeellijst.|
|description|De beschrijving van de afspeellijst.|
|userName|De naam van de gebruiker die de afspeellijst heeft gemaakt.|
|Gemaakt|De creatietijd van de afspeellijst.|
|privacyMode|De privacymodus van de afspeellijst (Privé/Openbaar).|
|state|De afspeellijst (geüpload, verwerkt, verwerkt, mislukt, in quarantaine geplaatst).|
|isOwned|Geeft aan of de afspeellijst is gemaakt door de huidige gebruiker.|
|isBewerkbaar|Geeft aan of de huidige gebruiker gemachtigd is om de afspeellijst te bewerken.|
|isBase|Geeft aan of de afspeellijst een basisafspeellijst (een video) of een afspeellijst van andere video's is (afgeleid).|
|duurInSeconden|De totale duur van de afspeellijst.|
|samengevatInzichten|Bevat een [samengevatInsights](#summarizedinsights).
|video's|Een lijst met [video's](#videos) die de afspeellijst samenstellen.<br/>Als deze afspeellijst met opgebouwde tijdsbereiken van andere video's (afgeleid) bevat, bevatten de video's in deze lijst alleen gegevens uit de opgenomen tijdsbereiken.|

```json
{
  "accountId": "bca61527-1221-bca6-1527-a90000002000",
  "id": "abc3454321",
  "name": "My first video",
  "description": "I am trying VI",
  "userName": "Some name",
  "created": "2018/2/2 18:00:00.000",
  "privacyMode": "Private",
  "state": "Processed",
  "isOwned": true,
  "isEditable": false,
  "isBase": false,
  "durationInSeconds": 120, 
  "summarizedInsights" : { . . . }
  "videos": [{ . . . }]
}
```

## <a name="summarizedinsights"></a>samengevatInzichten

In dit gedeelte ziet u de samenvatting van de inzichten.

|Kenmerk | Beschrijving|
|---|---|
|name|De naam van de video. Bijvoorbeeld Azure Monitor.|
|id|De ID van de video. Bijvoorbeeld 63c6d532ff.|
|privacyMode|Uw uitsplitsing kan een van de volgende modi hebben: **Privé**, **Openbaar**. **Openbaar** - de video is zichtbaar voor iedereen in je account en iedereen die een link naar de video heeft. **Privé** - de video is zichtbaar voor iedereen in je account.|
|duur|Bevat één duur die de tijd beschrijft waarin een inzicht is opgetreden. Duur is in seconden.|
|thumbnailVideoId|De ID van de video waaruit de miniatuur is genomen.
|thumbnailId|De miniatuur-ID van de video. Als u de werkelijke miniatuur wilt krijgen, roept u [Get-Thumbnail](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Thumbnail) op en geeft u de thumbnailVideoId en thumbnailId door.|
|Gezichten|Kan nul of meer vlakken bevatten. Zie [gezichten](#faces)voor meer gedetailleerde informatie.|
|trefwoorden|Kan nul of meer zoekwoorden bevatten. Zie [trefwoorden](#keywords)voor meer gedetailleerde informatie.|
|Gevoelens|Kan nul of meer sentimenten bevatten. Zie [sentimenten voor](#sentiments)meer gedetailleerde informatie.|
|audioEffecten| Kan nul of meer audioEffecten bevatten. Zie [audioEffecten](#audioEffects)voor meer gedetailleerde informatie.|
|labels| Kan nul of meer labels bevatten. Zie [labels](#labels)voor meer informatie .|
|Merken| Kan nul of meer merken bevatten. Zie [merken](#brands)voor meer gedetailleerde informatie.|
|statistieken | Zie [statistieken](#statistics)voor meer gedetailleerde informatie .|
|Emoties| Kan nul of meer emoties bevatten. Voor meer gedetailleerde informatie, zie [emoties](#emotions).|
|Onderwerpen|Kan nul of meer onderwerpen bevatten. De [onderwerpen](#topics) inzicht.|

## <a name="videos"></a>video's

|Name|Beschrijving|
|---|---|
|accountId|De VI-account-id van de video.|
|id|De id van de video.|
|name|De naam van de video.
|state|De status van de video (geüpload, verwerkt, verwerkt, mislukt, in quarantaine geplaatst).|
|verwerkingVooruitgang|De voortgang van de verwerking tijdens de verwerking (bijvoorbeeld 20%).|
|failureCode|De foutcode als deze niet wordt verwerkt (bijvoorbeeld 'UnsupportedFileType').|
|failureMessage|Het foutbericht als dit niet is verwerkt.|
|extern id|De externe id van de video (indien opgegeven door de gebruiker).|
|externalUrl (extern)|De externe url van de video (indien opgegeven door de gebruiker).|
|metagegevens|De externe metagegevens van de video (indien opgegeven door de gebruiker).|
|isAdult|Geeft aan of de video handmatig is beoordeeld en geïdentificeerd als een video voor volwassenen.|
|Inzichten|Het insights object. Zie [inzichten](#insights)voor meer informatie.|
|thumbnailId|De miniatuur-ID van de video. Als u de werkelijke miniatuuroproep [Get-Thumbnail](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Thumbnail) wilt krijgen en de video-id en thumbnailId wilt doorgeven.|
|gepubliceerdUrl|Een url om de video te streamen.|
|gepubliceerdUrlProxy|Een url om de video van (voor Apple-apparaten) te streamen.|
|viewToken|Een kortstondige weergavetoken voor het streamen van de video.|
|bronTaal|De brontaal van de video.|
|language|De werkelijke taal van de video (vertaling).|
|indexingPreset|De voorinstelling die wordt gebruikt om de video te indexeren.|
|streamingPreset|De voorinstelling die wordt gebruikt om de video te publiceren.|
|linguïstischModelId|Het CRIS-model dat wordt gebruikt om de video te transcriberen.|
|statistieken | Zie [statistieken](#statistics)voor meer informatie .|

```json
{
    "videos": [{
        "accountId": "2cbbed36-1972-4506-9bc7-55367912df2d",
        "id": "142a356aa6",
        "state": "Processed",
        "privacyMode": "Private",
        "processingProgress": "100%",
        "failureCode": "General",
        "failureMessage": "",
        "externalId": null,
        "externalUrl": null,
        "metadata": null,
        "insights": {. . . },
        "thumbnailId": "89d7192c-1dab-4377-9872-473eac723845",
        "publishedUrl": "https://videvmediaservices.streaming.mediaservices.windows.net:443/d88a652d-334b-4a66-a294-3826402100cd/Xamarine.ism/manifest",
        "publishedProxyUrl": null,
        "viewToken": "Bearer=<token>",
        "sourceLanguage": "En-US",
        "language": "En-US",
        "indexingPreset": "Default",
        "linguisticModelId": "00000000-0000-0000-0000-000000000000"
    }],
}
```
### <a name="insights"></a>Inzichten

Elk inzicht (bijvoorbeeld transcriptieregels, gezichten, merken, enz.), bevat een lijst met unieke elementen (bijvoorbeeld face1, face2, face3), en elk element heeft zijn eigen metadata en een lijst met exemplaren (die tijdbereiken zijn met extra optionele metadata).

Een gezicht kan een ID, een naam, een miniatuur, andere metagegevens en een lijst met tijdelijke instanties hebben (bijvoorbeeld: 00:00:05 – 00:00:10, 00:01:00 - 00:02:30 en 00:41:21 – 00:41:49.) Elke tijdelijke instantie kan extra metagegevens hebben. Bijvoorbeeld, de rechthoek van het gezicht coördinaten (20,230,60,60).

|Versie|De codeversie|
|---|---|
|bronTaal|De brontaal van de video (uitgaande van één hoofdtaal). In de vorm van een [BCP-47](https://tools.ietf.org/html/bcp47) snaar.|
|language|De insights taal (vertaald uit de brontaal). In de vorm van een [BCP-47](https://tools.ietf.org/html/bcp47) snaar.|
|Afschrift|Het [transcript](#transcript) inzicht.|
|Ocr|Het [OCR-inzicht.](#ocr)|
|trefwoorden|De [trefwoorden](#keywords) inzicht.|
|blokken|Kan een of meer [blokken](#blocks) bevatten|
|Gezichten|De [gezichten](#faces) inzicht.|
|labels|De [labels](#labels) inzicht.|
|Shots|De [schoten](#shots) inzicht.|
|Merken|De [merken](#brands) inzicht.|
|audioEffecten|De [audioEffecten](#audioEffects) inzicht.|
|Gevoelens|De [gevoelens](#sentiments) inzicht.|
|visualContentModeration|De [visualContentModeration](#visualcontentmoderation) inzicht.|
|textualContentModeration|De [textualContentModeration](#textualcontentmoderation) inzicht.|
|Emoties| De [emoties](#emotions) inzicht.|
|Onderwerpen|De [onderwerpen](#topics) inzicht.|

Voorbeeld:

```json
{
  "version": "0.9.0.0",
  "sourceLanguage": "en-US",
  "language": "es-ES",
  "transcript": ...,
  "ocr": ...,
  "keywords": ...,
  "faces": ...,
  "labels": ...,
  "shots": ...,
  "brands": ...,
  "audioEffects": ...,
  "sentiments": ...,
  "visualContentModeration": ...,
  "textualContentModeration": ...
}
```

#### <a name="blocks"></a>blokken

Kenmerk | Beschrijving
---|---
id|ID van het blok.|
Exemplaren|Een lijst met tijdsbereiken van dit blok.|

#### <a name="transcript"></a>Afschrift

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

#### <a name="ocr"></a>Ocr

|Name|Beschrijving|
|---|---|
|id|De OCR-lijn-ID.|
|tekst|De OCR-tekst.|
|betrouwbaarheid|Het erkenningsvertrouwen.|
|language|De OCR-taal.|
|Exemplaren|Een lijst met tijdbereiken waar deze OCR is weergegeven (dezelfde OCR kan meerdere keren worden weergegeven).|
|hoogte|De hoogte van de OCR-rechthoek|
|top|De bovenste locatie in px|
|links| De linkerlocatie in px|
|breedte|De breedte van de OCR-rechthoek|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 675.971,
      "height": 35,
      "language": "en-US",
      "left": 31,
      "top": 97,
      "width": 400,      
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    }
  ],
```

#### <a name="keywords"></a>trefwoorden

|Name|Beschrijving|
|---|---|
|id|Het trefwoord-ID.|
|tekst|De trefwoordtekst.|
|betrouwbaarheid|Het erkenningsvertrouwen van het zoekwoord.|
|language|De trefwoordtaal (indien vertaald).|
|Exemplaren|Een lijst met tijdsbereiken waar dit zoekwoord is weergegeven (een zoekwoord kan meerdere keren worden weergegeven).|

```json
{
    id: 0,
    text: "technology",
    confidence: 1,
    language: "en-US",
    instances: [{
            adjustedStart: "0:05:15.782",
            adjustedEnd: "0:05:16.249",
            start: "0:05:15.782",
            end: "0:05:16.249"
    },
    {
            adjustedStart: "0:04:54.761",
            adjustedEnd: "0:04:55.228",
            start: "0:04:54.761",
            end: "0:04:55.228"
    }]
}
```

#### <a name="faces"></a>Gezichten

|Name|Beschrijving|
|---|---|
|id|De gezichts-ID.|
|name|De naam van het gezicht. Het kan 'Unknown #0, een geïdentificeerde beroemdheid of een klant opgeleid persoon.|
|betrouwbaarheid|Het gezichtsidentificatievertrouwen.|
|description|Een beschrijving van de beroemdheid. |
|thumbnailId|De ID van de miniatuur van dat gezicht.|
|knownPersonId|Als het een bekende persoon is, zijn interne ID.|
|referenceId|Als het een Bing beroemdheid, haar Bing ID.|
|referenceType|Momenteel alleen Bing.|
|titel|Als het een beroemdheid is, is de titel (bijvoorbeeld 'Microsoft's CEO').|
|imageUrl|Als het een beroemdheid, de afbeelding url.|
|Exemplaren|Dit zijn gevallen van waar het gezicht verscheen in de gegeven tijdbereik. Elke instantie heeft ook een thumbnailsId. |

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

#### <a name="labels"></a>labels

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

#### <a name="scenes"></a>Scènes

|Name|Beschrijving|
|---|---|
|id|De plaats-ID.|
|Exemplaren|Een lijst met tijdsbereiken van deze scène (een scène kan slechts 1 exemplaar hebben).|

```json
"scenes":[  
    {  
      "id":0,
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },
    {  
      "id":1,
      "instances":[  
          {  
            "start":"0:00:06.34",
            "end":"0:00:47.047",
            "duration":"0:00:40.707"
          }
      ]
    },

]
```

#### <a name="shots"></a>Shots

|Name|Beschrijving|
|---|---|
|id|De shot ID.|
|Hoofdframes|Een lijst met keyFrames in de opname (elk heeft een ID en een lijst met instanties tijdbereiken). Elke keyFrame-instantie heeft een miniatuurId-veld, dat de miniatuur-id van het hoofdframe bevat.|
|Exemplaren|Een lijst met tijdsbereiken van dit schot (een opname kan slechts 1 exemplaar hebben).|

```json
"shots":[  
    {  
      "id":0,
      "keyFrames":[  
          {  
            "id":0,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:00.209",
                  "end":"0:00:00.251",
                  "duration":"0:00:00.042"
                }
            ]
          },
          {  
            "id":1,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:04.755",
                  "end":"0:00:04.797",
                  "duration":"0:00:00.042"
                }
            ]
          }
      ],
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },

]
```

#### <a name="brands"></a>Merken

Bedrijfs- en productmerknamen gedetecteerd in de spraak naar teksttranscript en/of Video OCR. Dit omvat geen visuele herkenning van merken of logodetectie.

|Name|Beschrijving|
|---|---|
|id|De merk-ID.|
|name|De merken naam.|
|referenceId | Het achtervoegsel van het merk wikipedia url. 'Target_Corporation' is bijvoorbeeld het achtervoegsel van [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation).
|referenceUrl | De Wikipedia-url van het merk, indien aanwezig. Bijvoorbeeld. [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation)
|description|De beschrijving van de merken.|
|tags|Een lijst met vooraf gedefinieerde tags die aan dit merk zijn gekoppeld.|
|betrouwbaarheid|De betrouwbaarheid van de Video Indexer merkdetector (0-1).|
|Exemplaren|Een lijst van tijdsbereiken van dit merk. Elke instantie heeft een brandType, die aangeeft of dit merk is verschenen in het transcript of in OCR.|

```json
"brands": [
{
    "id": 0,
    "name": "MicrosoftExcel",
    "referenceId": "Microsoft_Excel",
    "referenceUrl": "http: //en.wikipedia.org/wiki/Microsoft_Excel",
    "referenceType": "Wiki",
    "description": "Microsoft Excel is a sprea..",
    "tags": [],
    "confidence": 0.975,
    "instances": [
    {
        "brandType": "Transcript",
        "start": "00: 00: 31.3000000",
        "end": "00: 00: 39.0600000"
    }
    ]
},
{
    "id": 1,
    "name": "Microsoft",
    "referenceId": "Microsoft",
    "referenceUrl": "http: //en.wikipedia.org/wiki/Microsoft",
    "description": "Microsoft Corporation is...",
    "tags": [
    "competitors",
    "technology"
    ],
    "confidence": 1.0,
    "instances": [
    {
        "brandType": "Transcript",
        "start": "00: 01: 44",
        "end": "00: 01: 45.3670000"
    },
    {
        "brandType": "Ocr",
        "start": "00: 01: 54",
        "end": "00: 02: 45.3670000"
    }
    ]
}
]
```

#### <a name="statistics"></a>statistieken

|Name|Beschrijving|
|---|---|
|CorrespondentieTelling|Aantal correspondenties in de video.|
|SpeakerWordCount|Het aantal woorden per spreker.|
|SpeakernumberofFragments|De hoeveelheid fragmenten die de spreker in een video heeft.|
|SpeakerLongestMonolog|De langste monlog van de spreker. Als de luidspreker stiltes in de monologen heeft, wordt deze meegeleverd. Stilte aan het begin en het einde van de monologen wordt verwijderd.| 
|SpeakerTalkToListenRatio|De berekening is gebaseerd op de tijd die wordt besteed aan de monolog van de spreker (zonder de stilte ertussen) gedeeld door de totale tijd van de video. De tijd wordt afgerond op het derde decimaal punt.|

#### <a name="audioeffects"></a><a id="audioEffects"/>audioEffecten

|Name|Beschrijving|
|---|---|
|id|De audio-effect-ID.|
|type|Het type audio-effect (bijvoorbeeld klappen, spraak, stilte).|
|Exemplaren|Een lijst met tijdbereiken waar dit audio-effect is verschenen.|

```json
"audioEffects": [
{
    "id": 0,
    "type": "Clapping",
    "instances": [
    {
        "start": "00:00:00",
        "end": "00:00:03"
    },
    {
        "start": "00:01:13",
        "end": "00:01:21"
    }
    ]
}
]
```

#### <a name="sentiments"></a>Gevoelens

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

#### <a name="visualcontentmoderation"></a>visualContentModeration

Het visualContentModeration-blok bevat tijdsbereiken waarvan Video Indexer heeft vastgesteld dat deze mogelijk inhoud voor volwassenen heeft. Als visualContentModeration leeg is, is er geen inhoud voor volwassenen geïdentificeerd.

Video's waarvan wordt vastgesteld dat ze inhoud voor volwassenen of racy bevatten, zijn mogelijk alleen beschikbaar voor privéweergave. Gebruikers hebben de mogelijkheid om een verzoek in te dienen voor een menselijke beoordeling van de inhoud, in welk geval het kenmerk IsAdult het resultaat van de menselijke beoordeling zal bevatten.

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

#### <a name="textualcontentmoderation"></a>textualContentModeration 

|Name|Beschrijving|
|---|---|
|id|De tekstuele inhoudsmoderatie-ID.|
|bannedWordsCount |Het aantal verboden woorden.|
|bannedWordsRatio |De verhouding van het totale aantal woorden.|

#### <a name="emotions"></a>Emoties

Video Indexer identificeert emoties op basis van spraak- en audiosignalen. De geïdentificeerde emotie zou kunnen zijn: vreugde, verdriet, woede of angst.

|Name|Beschrijving|
|---|---|
|id|De emotie-ID.|
|type|De emotie moment dat werd geïdentificeerd op basis van spraak en audio signalen. De emotie zou kunnen zijn: vreugde, verdriet, woede, of angst.|
|Exemplaren|Een lijst van tijdswaaiers waar deze emotie verscheen.|

```json
"emotions": [{
    "id": 0,
    "type": "Fear",
    "instances": [{
      "adjustedStart": "0:00:39.47",
      "adjustedEnd": "0:00:45.56",
      "start": "0:00:39.47",
      "end": "0:00:45.56"
    },
    {
      "adjustedStart": "0:07:19.57",
      "adjustedEnd": "0:07:23.25",
      "start": "0:07:19.57",
      "end": "0:07:23.25"
    }]
  },
  {
    "id": 1,
    "type": "Anger",
    "instances": [{
      "adjustedStart": "0:03:55.99",
      "adjustedEnd": "0:04:05.06",
      "start": "0:03:55.99",
      "end": "0:04:05.06"
    },
    {
      "adjustedStart": "0:04:56.5",
      "adjustedEnd": "0:05:04.35",
      "start": "0:04:56.5",
      "end": "0:05:04.35"
    }]
  },
  {
    "id": 2,
    "type": "Joy",
    "instances": [{
      "adjustedStart": "0:12:23.68",
      "adjustedEnd": "0:12:34.76",
      "start": "0:12:23.68",
      "end": "0:12:34.76"
    },
    {
      "adjustedStart": "0:12:46.73",
      "adjustedEnd": "0:12:52.8",
      "start": "0:12:46.73",
      "end": "0:12:52.8"
    },
    {
      "adjustedStart": "0:30:11.29",
      "adjustedEnd": "0:30:16.43",
      "start": "0:30:11.29",
      "end": "0:30:16.43"
    },
    {
      "adjustedStart": "0:41:37.23",
      "adjustedEnd": "0:41:39.85",
      "start": "0:41:37.23",
      "end": "0:41:39.85"
    }]
  },
  {
    "id": 3,
    "type": "Sad",
    "instances": [{
      "adjustedStart": "0:13:38.67",
      "adjustedEnd": "0:13:41.3",
      "start": "0:13:38.67",
      "end": "0:13:41.3"
    },
    {
      "adjustedStart": "0:28:08.88",
      "adjustedEnd": "0:28:18.16",
      "start": "0:28:08.88",
      "end": "0:28:18.16"
    }]
  }
],
```

#### <a name="topics"></a>Onderwerpen

Video Indexer maakt gevolgtrekking van de belangrijkste onderwerpen uit transcripties. Indien mogelijk is de [2e-level IPTC](https://iptc.org/standards/media-topics/) taxonomie inbegrepen. 

|Name|Beschrijving|
|---|---|
|id|De topic-ID.|
|name|De onderwerpnaam, bijvoorbeeld: "Pharmaceuticals".|
|referenceId|Broodkruimels die de hiërarchie van onderwerpen weerspiegelen. Bijvoorbeeld: "Gezondheid en welzijn / Geneeskunde en gezondheidszorg / Farmaceutica".|
|betrouwbaarheid|De betrouwbaarheidsscore in het bereik [0,1]. Hoger is meer vertrouwen.|
|language|De taal die in het onderwerp wordt gebruikt.|
|iptcNaam|De IPTC-mediacodenaam, indien gedetecteerd.|
|Exemplaren |Momenteel indexeert Video Indexer een onderwerp niet op tijdsintervallen, dus de hele video wordt gebruikt als interval.|

```json
"topics": [{
    "id": 0,
    "name": "INTERNATIONAL RELATIONS",
    "referenceId": "POLITICS AND GOVERNMENT/FOREIGN POLICY/INTERNATIONAL RELATIONS",
    "referenceType": "VideoIndexer",
    "confidence": 1,
    "language": "en-US",
    "instances": [{
        "adjustedStart": "0:00:00",
        "adjustedEnd": "0:03:36.25",
        "start": "0:00:00",
        "end": "0:03:36.25"
    }]
}, {
    "id": 1,
    "name": "Politics and Government",
    "referenceType": "VideoIndexer",
    "iptcName": "Politics",
    "confidence": 0.9041,
    "language": "en-US",
    "instances": [{
        "adjustedStart": "0:00:00",
        "adjustedEnd": "0:03:36.25",
        "start": "0:00:00",
        "end": "0:03:36.25"
    }]
}]
. . .
```

## <a name="next-steps"></a>Volgende stappen

[Ontwikkelaarportal voor video-indexer](https://api-portal.videoindexer.ai)

Zie [Widgets voor videoindexer insluiten in uw toepassingen](video-indexer-embed-widgets.md)voor informatie over het insluiten van widgets in uw toepassing. 

