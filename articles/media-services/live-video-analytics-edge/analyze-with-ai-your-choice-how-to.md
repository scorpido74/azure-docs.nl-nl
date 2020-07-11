---
title: Analyseer live video met AI van uw keuze-Azure
description: In dit artikel leert u hoe u een IoT Edge module bouwt die kan worden geïntegreerd met live video Analytics op IoT Edge om live video te analyseren met behulp van een computer vision model van uw keuze.
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: 6a1ea3ebd8c7de4c691d7a982dbc08e9d08d9e38
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86182862"
---
# <a name="analyze-live-video-with-ai-of-your-choice"></a>Livevideo analyseren met de AI van uw keuze

In dit artikel leert u hoe u een IoT Edge module bouwt die kan worden geïntegreerd met live video Analytics op IoT Edge om live video te analyseren met behulp van een computer vision model van uw keuze. 

## <a name="pre-reading"></a>Vooraf lezen

[Mediagrafiekconcepten](media-graph-concept.md)

## <a name="media-graph-extension"></a>Media Graph-extensie

Live video Analytics op IoT Edge definieert een uitbreidings model waarmee u de verwerkings mogelijkheden voor media grafieken kunt uitbreiden naar uw eigen media Analytics-onderdelen via een grafiek extensie. Uw analyse component kan gebruikmaken van traditionele beeldverwerkings technieken of computer vision-AI-modellen. Graph-extensies worden ingeschakeld door het knoop punt voor de [http-extensie processor](media-graph-concept.md#http-extension-processor) in een media grafiek op te nemen. De HTTP-extensie processor kan video frames door geven naar een HTTP-eind punt dat door u is opgegeven en fungeert als de interface voor uw onderdeel via dat. De verbinding kan worden gemaakt met een lokaal of extern eind punt en kan, indien nodig, worden beveiligd door verificatie en TLS-versleuteling. Daarnaast kunt u met de processor de video frames optioneel schalen en coderen voordat ze worden doorgestuurd.

U kunt ervoor kiezen om het deuw keuze model uit te voeren op een wille keurige beschik bare afzonderings runtime, zoals ONNX, tensor flow, PyTorch of anderen op uw eigen docker-container. U kunt ook de programmeer taal en-bibliotheken van uw keuze gebruiken om de mogelijkheden voor het afleiden van installatie kopieën weer te geven als een HTTP-server in uw eigen container. De depresentatieve container moet worden geïmplementeerd naast de live video Analytics Edge-module voor de beste prestaties en wordt vervolgens aangeroepen via de HTTP-extensie processor in uw grafiek topologie.
Daarnaast kan de frequentie van de aanroepen naar uw aangepaste module worden beperkt door optioneel een [Motion detector-processor](media-graph-concept.md#motion-detection-processor) en een upstream voor de [frame snelheids filter](media-graph-concept.md#frame-rate-filter-processor) voor de http-extensie processor toe te voegen.

In het onderstaande diagram ziet u de gegevens stroom op hoog niveau:

![Edge-apparaat](./media/analyze-live-video-with-ai-your-choice-how-to/edge-device.png)

Zo kunt u video analyseren met behulp van de AI-modellen van uw keuze om te voldoen aan uw unieke zakelijke behoeften. De AI-modellen kunnen afkomstig zijn van de open-source community of van uw eigen gegevens wetenschappers of van een gespecialiseerde AI-provider.

## <a name="media-graph-http-extension-contract-definitions"></a>Contract definities voor de HTTP-extensie van media Graph

In deze sectie wordt het HTTP-contract gedefinieerd dat de gegevens stroom definieert.

### <a name="http-extensibility-protocol"></a>HTTP-uitbreidings Protocol  

Het HTTP-contract wordt als volgt gedefinieerd:

* Uw module fungeert als de HTTP-server.
* Live video Analytics op IoT Edge module fungeert als de HTTP-client.

### <a name="request"></a>Aanvraag

Aanvragen van de module live video Analytics naar uw module zouden er als volgt uitzien:

| Sleutel | Waarde |
|---|---|
|POST| `https://hostname/optional-path?optional-query`|
|Accepteren|Application/JSON,*/*|
|Autorisatie| Basic, Digest, Bearer (via aangepaste ondersteuning voor kopteksten)|
|Content-Type|image/jpeg<br/>image/png<br/>image/bmp<br/>afbeelding/x-RAW|
|Content-length|Lengte van hoofd tekst, in bytes|
|User-Agent|Azure Media Services|
|Hoofdtekst|Afbeeldings bytes, binaire code ring in een van de ondersteunde inhouds typen.|

#### <a name="example"></a>Voorbeeld

```
POST http://localhost:8080/inference HTTP/1.1
Host: localhost:8080
x-ms-client-request-id: d6050cd4-c9f2-42d3-9adc-53ba7e440f17
Content-Type: image/bmp
Content-Length: 519222

(Image Binary Content)
```

### <a name="response"></a>Reactie

Reacties van uw module naar de live video Analytics-module moeten er als volgt uitzien

| Sleutel | Waarde |
|---|---|
|Statuscodes|200 OK-resultaten van het inleiden van interferenties gevonden<br/>204 geen inhoud-geen inhoud gevonden door de AI<br/>400 ongeldige aanvraag-niet verwacht<br/>500 interne server fout-niet verwacht<br/>503 Server bezet-AMS wordt een back-up gemaakt op basis van de header ' opnieuw proberen na ' of op basis van een standaard hoeveelheid tijd in de koptekst van het hoofdletter gebruik dat niet vooraf is ingesteld.|
|Content-Type|application/json|
|Content-length|    Lengte van hoofd tekst, in bytes|
|Hoofdtekst|JSON-object met één eigenschap ' dezicht '.|

#### <a name="example"></a>Voorbeeld

```
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 468
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 17 Apr 2020 04:44:01 GMT

{
  "inferences": [
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.9048132 },
        "box": { "l": 0.42681578, "t": 0.47660735, "w": 0.019501392, "h": 0.020954132 }
      }
    },
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.8953932 },
        "box": { "l": 0.55083525, "t": 0.4843858, "w": 0.046550274, "h": 0.046502113 }
      }
    }    
  ]
}
```

Het wordt nadrukkelijk aanbevolen om antwoorden te retour neren met behulp van geldige JSON-documenten volgens het vooraf gemaakte schema dat hieronder is gedefinieerd. Dit zorgt voor een betere compatibiliteit met andere onderdelen en mogelijke toekomstige mogelijkheden die zijn toegevoegd aan de module live video Analytics.

Als uw module een antwoord retourneert waarbij het inhouds type niet ' application/json ' is, wordt het bericht door live video Analytics gecodeerd als basis 64-inhoud en geserialiseerd als een ondoorzichtige JSON-nettolading.

Als uw module een antwoord retourneert met het inhouds type ' application/json ', maar het JSON-schema niet voldoet aan het [onderstaande schema voor meta gegevens](#inference-metadata-schema), wordt de bericht lading via de pijp lijn doorgestuurd, maar wordt de interoperabiliteit verminderd.

> [!NOTE]
> Als uw module geen resultaten oplevert, moet deze HTTP 204-status code (geen inhoud) retour neren met een lege antwoord tekst. Live video analyse begrijpt dit als een leeg resultaat en stuurt de gebeurtenis niet over in de pijp lijn.

### <a name="inference-metadata-schema"></a>Meta gegevens schema afleiding

Elk object voor het afwijzen van interferentie volgt dit schema van de hoofd verzameling:

```
{
  "type": "[classification|motion|entity|text|other]",
  "subtype": "",              // Free form subtype id

  // Object has *at most one of* the following
  "classification":
  {
    "tag": 
    {
      "value": "",            // Tag value
      "confidence": 0.0       // Tag confidence
    },
    "attributes":[            // Optional attributes
      {
        "name": "",           
        "value": "",          
        "confidence": 0.0     
      }
    ]
  },

  "motion":
  {
    "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
    // "regionId": ""
  },

  "entity":
  {
    "tag": 
    {
      "value": "",            // Tag value
      "confidence": 0.0       // Tag confidence
    },
    "attributes":[            // Optional attributes
      {
        "name": "",           
        "value": "",          
        "confidence": 0.0     
      }
    ],
    "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
  },

  "text":
  {
    "value": "",              // Text value
    "language": "",           // Optional BCP47 Language Code (https://tools.ietf.org/html/bcp47)
    "startTimestamp": 0,      // Optional start timestamp
    "endTimestamp": 0         // Optional end timestamp
  },

  // Plus every object can have zero or more extensions
  "extensions":
  {
    "name1": "value1",
    "name2": "value2"
    // ...
  }
}
```
 
### <a name="data-contracts---class-hierarchy"></a>Gegevens contracten-klassen hiërarchie

![Gegevens contracten-klassen hiërarchie](./media/analyze-live-video-with-ai-your-choice-how-to/data-contracts.png)

### <a name="examples"></a>Voorbeelden  

Het onderstaande voor beeld bevat één gebeurtenis met alle ondersteunde typen inactiviteit:

```
{
  "inferences": [
    // Light detection
    {
      "type": "classification",
      "subtype": "lightDetection",
      "classification": {
        "tag": { "value": "daylight", "confidence": 0.86 },
        "attributes": [
            { "name": "isBlackAndWhite", "value": "false", "confidence": 0.71 }
        ]
      }
    },

    // Motion detection
    {
      "type": "motion",
      "subtype": "motionDetection",
      "motion":
      {
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      }
    },

    // Object detection
    {
      "type": "entity",
      "subtype": "objectDetection",    
      "entity":
      {
        "tag": { "value": "dog", "confidence": 0.97 },
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      }
    },

    // Vehicle identification
    {
      "type": "entity",
      "subtype": "vehicleIdentification",    
      "entity":
      {
        "tag": { "value": "007-SPY", "confidence": 0.82 },
        "attributes":[  
          { "name": "color", "value": "black", "confidence": 0.90 },
          { "name": "body", "value": "coupe", "confidence": 0.87 },
          { "name": "make", "value": "Aston Martin", "confidence": 0.35 },
          { "name": "model", "value": "DBS V12", "confidence": 0.33 }
        ],
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      }
    },

    // People identification
    {
      "type": "entity",
      "subtype": "peopleIdentification",    
      "entity":
      {
        "tag": { "value":"Erwin Schrödinger", "confidence": 0.50 },
        "attributes":[  
          { "name": "age", "value": "73", "confidence": 0.87 },
          { "name": "glasses", "value": "yes", "confidence": 0.94 }
        ],
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      },
    }
  ]
}
```
 
## <a name="sample-http-extension-modules"></a>Voor beelden van HTTP-extensie modules

Een paar voor beelden van HTTP-extensie modules vindt u in de [Live video Analytics github opslag plaats](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis). Een van deze voor [beelden van video analyse](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) laat zien hoe u het [YOLOv3](https://pjreddie.com/darknet/yolo/) [ONNX](http://onnx.ai/) -model kunt gebruiken om een IOT Edge module voor object detectie te maken. In een ander voor beeld van een [video analyse](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx-tiny) ziet u hoe u kleine YOLOv3 gebruikt, een licht gewicht versie van het YOLOv3 ONNX-model. U kunt dezelfde benadering gebruiken om een eigen module te bouwen met een AI-model van uw keuze.

## <a name="next-steps"></a>Volgende stappen

[Problemen oplossen](troubleshoot-how-to.md)
