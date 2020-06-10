---
title: Aan de slag met Live Video Analytics in IoT Edge - Azure
description: In deze quickstart kunt u aan de slag met Live Video Analytics in IoT Edge en leert u beweging detecteren in een live-videostream.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 307a81938be3e25b8a6a07bb3696ca3b7647c0aa
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261563"
---
# <a name="quickstart-get-started---live-video-analytics-on-iot-edge"></a>Quickstart: Over Live Video Analytics in IoT Edge

Deze quickstart begeleidt u door de stappen om aan de slag te gaan met Live Video Analytics in IoT Edge. Er wordt gebruikgemaakt van een Azure-VM als een IoT Edge-apparaat en van een gesimuleerde live-videostream. Nadat u de installatie stappen hebt voltooid, kunt u een gesimuleerde live-videostream uitvoeren via een mediagraaf die beweging in die stream detecteert en rapporteert. In het onderstaande diagram ziet u een grafische weergave van die mediagraaf.

![Live Video Analytics op basis van bewegingsdetectie](./media/analyze-live-video/motion-detection.png)

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Visual Studio Code](https://code.visualstudio.com/) op uw ontwikkelcomputer met een [extensie van Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Het netwerk waarmee de ontwikkelcomputer is verbonden, moet het AMQP-protocol via poort 5671 toestaan (zodat Azure IoT Tools kan communiceren met Azure IoT Hub).

> [!TIP]
> U wordt mogelijk gevraagd om docker te installeren tijdens de installatie van de Azure IoT Tools-extensie. U kunt deze vraag negeren.

## <a name="set-up-azure-resources"></a>Azure-resources instellen

De volgende Azure-resources zijn voor deze zelfstudie vereist.

* IoT Hub
* Storage-account
* Azure Media Services-account
* Linux-VM in Azure, met [IoT Edge-runtime](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) geïnstalleerd

Voor deze quickstart wordt u aangeraden gebruik te maken van het [installatiescript voor Live Video Analytics-resources](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) voor het implementeren van de hierboven genoemde Azure-resources in uw Azure-abonnement. Volg de onderstaande stappen om dit te doen:

1. Blader naar https://shell.azure.com.
1. Als dit de eerste keer is dat u Cloud Shell gebruikt, wordt u gevraagd een abonnement te selecteren voor het maken van een opslagaccount en een Microsoft Azure Files-share. Selecteer Opslag maken om een opslagaccount te maken voor het opslaan van de gegevens van uw Cloud Shell-sessie. Dit opslagaccount is gescheiden van het account dat door het script wordt gemaakt voor gebruik met uw Azure Media Services-account.
1. Selecteer in de vervolgkeuzelijst aan de linkerkant van het shell-venster de optie Bash als uw omgeving.

    ![Omgevingsselector](./media/quickstarts/env-selector.png)

1. Voer de volgende opdracht uit

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
Als het script is voltooid, ziet u alle resources die hierboven in uw abonnement zijn vermeld. Als onderdeel van de scriptuitvoer wordt een tabel met resources gegenereerd waarin de naam van de IoT-hub wordt vermeld. Zoek het resourcetype **Microsoft.Devices/IotHubs** en noteer de naam. U hebt deze in de volgende stap nodig. Met het script worden ook enkele configuratiebestanden in de map ~/clouddrive/lva-sample/ gegenereerd. U hebt deze later in de quickstart nodig.

## <a name="deploy-modules-on-your-edge-device"></a>Modules op uw edge-apparaat implementeren

Voer vanuit Cloud Shell de volgende opdracht uit

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content ~/clouddrive/lva-sample/edge-deployment/deployment.amd64.json
```

Met de bovenstaande opdracht worden de volgende modules geïmplementeerd in het edge-apparaat (de virtuele Linux-machine):

* Live Video Analytics in IoT Edge (modulenaam lvaEdge)
* RTSP-simulator (modulenaam rtspsim)

In de RTSP-simulatormodule wordt een live-videostream gesimuleerd met behulp van een videobestand dat is gekopieerd naar uw edge-apparaat tijdens het uitvoeren van het [installatiescript voor Live Video Analytics-resources](https://github.com/Azure/live-video-analytics/tree/master/edge/setup). In deze fase hebt u de modules geïmplementeerd, maar er zijn geen mediagrafen actief.

## <a name="configure-azure-iot-tools-extension-in-visual-studio-code"></a>Azure IoT Tools-extensie in Visual Studio Code configureren

Start Visual Studio Code en volg de onderstaande instructies om verbinding te maken met uw Azure IoT Hub met behulp van de Azure IoT Tools-extensie.

1. Ga naar het tabblad Explorer in Visual Studio Code via **Beeld** > **Explorer** of druk op Ctrl+Shift+E.
1. Klik in de linkerbenedenhoek van het tabblad Explorer op Azure IoT Hub.
1. Klik op het pictogram Meer opties om het contextmenu weer te geven en selecteer de optie IoT Hub-verbindingsreeks instellen.
1. Er wordt een invoervak weergegeven. Voer hierin de IoT Hub-verbindingsreeks in. U kunt de verbindingstekenreeks voor uw IoT Hub ophalen van ~/clouddrive/lva-sample/appSettings.json in Cloud Shell.
1. Als er verbinding is gemaakt, wordt de lijst met edge-apparaten weergegeven. Er moet ten minste één apparaat met de naam lva-sample-device aanwezig zijn.
1. U kunt nu uw IoT Edge-apparaten beheren en interactief werken met Azure IoT Hub via het contextmenu.
1. U kunt de modules die op het edge-apparaat zijn geïmplementeerd, weergeven door het knooppunt Modules onder lva-sample-device uit te vouwen.

    ![Knooppunt lva-sample-device](./media/quickstarts/lva-sample-device-node.png)

## <a name="use-direct-methods"></a>Directe methoden gebruiken

U kunt de module gebruiken om live-videostreams te analyseren door directe methoden aan te roepen. Lees [Directe methoden voor Live Video Analytics in IoT Edge](direct-methods.md) om inzicht te krijgen in alle directe methoden van de module. 

### <a name="invoke-graphtopologylist"></a>GraphTopologyList aanroepen
Hiermee worden alle [graaftopologieën](media-graph-concept.md#media-graph-topologies-and-instances) in de module opgesomd.

1. Klik met de rechtermuisknop op de module lvaEdge en selecteer Module Directe methode aanroepen in het contextmenu.
1. Er verschijnt een bewerkingsvak middenin het bovenste deel van het Visual Studio Code-venster. Voer GraphTopologyList in het invoervak in en druk op Enter.
1. Kopieer vervolgens de onderstaande JSON-nettolading, plak deze in het bewerkingsvak en druk op Enter.

    ```
    {
        "@apiVersion" : "1.0"
    }
    ```

    Binnen een paar seconden ziet u het venster Uitvoer in Visual Studio Code verschijnen met het volgende antwoord

    ```
    [DirectMethod] Invoking Direct Method [GraphTopologyList] to [lva-sample-device/lvaEdge] ...
    [DirectMethod] Response from [lva-sample-device/lvaEdge]:
    {
      "status": 200,
      "payload": {
        "value": []
      }
    }
    ```
    
    Het bovenstaande antwoord wordt verwacht omdat er geen graaftopologieën zijn gemaakt.
    

### <a name="invoke-graphtopologyset"></a>GraphTopologySet aanroepen

Als u dezelfde stappen gebruikt als voor het aanroepen van GraphTopologyList, kunt u GraphTopologySet aanroepen om een [graaftopologie](media-graph-concept.md#media-graph-topologies-and-instances) in te stellen met behulp van de volgende JSON als nettolading.

```
{
    "@apiVersion": "1.0",
    "name": "MotionDetection",
    "properties": {
        "description": "Analyzing live video to detect motion and emit events",
        "parameters": [
            {
                "name": "rtspUserName",
                "type": "String",
                "description": "rtsp source user name.",
                "default": "dummyUserName"
            },
            {
                "name": "rtspPassword",
                "type": "String",
                "description": "rtsp source password.",
                "default": "dummyPassword"
            },
            {
                "name": "rtspUrl",
                "type": "String",
                "description": "rtsp Url"
            }
        ],
        "sources": [
            {
                "@type": "#Microsoft.Media.MediaGraphRtspSource",
                "name": "rtspSource",
                "endpoint": {
                    "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
                    "url": "${rtspUrl}",
                    "credentials": {
                        "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
                        "username": "${rtspUserName}",
                        "password": "${rtspPassword}"
                    }
                }
            }
        ],
        "processors": [
            {
                "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
                "name": "motionDetection",
                "sensitivity": "medium",
                "inputs": [
                    {
                        "nodeName": "rtspSource"
                    }
                ]
            }
        ],
        "sinks": [
            {
                "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
                "name": "hubSink",
                "hubOutputName": "inferenceOutput",
                "inputs": [
                    {
                        "nodeName": "motionDetection"
                    }
                ]
            }
        ]
    }
}

```


De bovenstaande JSON-nettolading resulteert in een graaftopologie die drie parameters definieert (waarvan twee met standaardwaarden). De topologie heeft één bronknooppunt (RTSP-bron), één processorknooppunt (bewegingsdetectieprocessor) en één sinkknooppunt (IoT Hub-sink).

Binnen een paar seconden ziet u in het venster Uitvoer het volgende antwoord verschijnen:

```
[DirectMethod] Invoking Direct Method [GraphTopologySet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-19T07:41:34.507Z",
      "lastModifiedAt": "2020-05-19T07:41:34.507Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "medium",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

De geretourneerde status is 201, wat aangeeft dat er een nieuwe topologie is gemaakt. Voer de volgende stappen uit:

* Roep GraphTopologySet opnieuw aan en merk op dat de geretourneerde statuscode 200 is. Statuscode 200 geeft aan dat een bestaande topologie is bijgewerkt.
* Roep GraphTopologySet opnieuw aan, maar wijzig de tekenreeks voor de beschrijving. Merk op dat de statuscode in het antwoord 200 is en dat de beschrijving is bijgewerkt naar de nieuwe waarde.
* Roep GraphTopologyList aan zoals beschreven in de vorige sectie en merk op dat de topologie MotionDetection nu in de geretourneerde nettolading wordt weergegeven.

### <a name="invoke-graphtopologyget"></a>GraphTopologyGet aanroepen

Roep nu GraphTopologyGet aan met de volgende nettolading

```

{
    "@apiVersion" : "1.0",
    "name" : "MotionDetection"
}
```

Binnen een paar seconden moet in het venster Uitvoer het volgende antwoord verschijnen:

```
[DirectMethod] Invoking Direct Method [GraphTopologyGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-19T07:41:34.507Z",
      "lastModifiedAt": "2020-05-19T07:41:34.507Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "medium",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

Let op het volgende in de nettolading van het antwoord:

* Statuscode is 200. Dit geeft aan dat de bewerking is geslaagd.
* De nettolading heeft de tijdstempels 'created' en 'lastModified'.

### <a name="invoke-graphinstanceset"></a>GraphInstanceSet aanroepen

Maak vervolgens een graafexemplaar die verwijst naar de bovenstaande graaftopologie. Zoals [hier](media-graph-concept.md#media-graph-topologies-and-instances) wordt beschreven, kunt u met graafexemplaren live-videostreams van talloze camera's analyseren met dezelfde graaftopologie.

Roep de directe methode voor GraphInstanceSet aan met de volgende nettolading.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1",
    "properties" : {
        "topologyName" : "MotionDetection",
        "description" : "Sample graph description",
        "parameters" : [
            { "name" : "rtspUrl", "value" : "rtsp://rtspsim:554/media/camera-300s.mkv" }
        ]
    }
}
```

Houd rekening met het volgende:

* In de bovenstaande nettolading wordt de naam van de topologie (MotionDetection) opgegeven waarvoor het exemplaar moet worden gemaakt.
* De nettolading bevat een parameterwaarde voor rtspUrl, die geen standaardwaarde heeft in de nettolading van de topologie.

Binnen een paar seconden ziet u in het venster Uitvoer het volgende antwoord verschijnen:

```
[DirectMethod] Invoking Direct Method [GraphInstanceSet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "name": "Sample-Graph-1",
    "properties": {
      "created": "2020-05-19T07:44:33.868Z",
      "lastModified": "2020-05-19T07:44:33.868Z",
      "state": "Inactive",
      "description": "Sample graph description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        }
      ]
    }
  }
}
```

Let op het volgende in de nettolading van het antwoord:

* De statuscode is 201, wat aangeeft dat er een nieuw exemplaar is gemaakt.
* De status is Inactief en geeft aan dat het exemplaar van de grafiek is gemaakt maar niet is geactiveerd. Zie de [statussen voor mediagraaf](media-graph-concept.md) voor meer informatie.

Voer de volgende stappen uit:

* Roep GraphInstanceSet opnieuw aan met dezelfde nettolading en houd er rekening mee dat de geretourneerde statuscode nu 200 is.
* Roep GraphInstanceSet opnieuw aan, maar met een andere beschrijving en let op de bijgewerkte beschrijving in de nettolading van het antwoord, waarmee wordt aangegeven dat het exemplaar van de grafiek is bijgewerkt.
* Roep GraphInstanceSet aan maar wijzig de naam in Sample-Graph-2 en bekijk de nettolading van het antwoord. Er wordt een nieuw exemplaar van de graaf gemaakt (dat wil zeggen, de statuscode is 201).

### <a name="invoke-graphinstanceactivate"></a>GraphInstanceActivate aanroepen

Activeer nu het graafexemplaar, waarmee de stroom van livevideo via de module wordt gestart. Roep de directe methode voor GraphInstanceActivate aan met de volgende nettolading.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

Binnen een paar seconden moet in het venster Uitvoer het volgende antwoord verschijnen:

```
[DirectMethod] Invoking Direct Method [GraphInstanceActivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Statuscode 200 in de nettolading van het antwoord geeft aan dat het graafexemplaar is geactiveerd.

### <a name="invoke-graphinstanceget"></a>GraphInstanceGet aanroepen

Roep nu de directe methode voor GraphInstanceGet aan met de volgende nettolading:

```
 {
     "@apiVersion" : "1.0",
     "name" : "Sample-Graph-1"
 }
 ```

Binnen een paar seconden moet in het venster Uitvoer het volgende antwoord verschijnen:

```
[DirectMethod] Invoking Direct Method [GraphInstanceGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "name": "Sample-Graph-1",
    "properties": {
      "created": "2020-05-19T07:44:33.868Z",
      "lastModified": "2020-05-19T07:44:33.868Z",
      "state": "Active",
      "description": "graph description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        }
      ]
    }
  }
}
```

Let op het volgende in de nettolading van het antwoord:

* Statuscode is 200. Dit geeft aan dat de bewerking is geslaagd.
* De status is Actief, wat aangeeft dat het graafexemplaar nu de status Actief heeft.

## <a name="observe-results"></a>Resultaten bekijken

Het graafexemplaar dat hierboven is gemaakt en geactiveerd, maakt gebruik van het knooppunt van de bewegingsdetectieprocessor om beweging in de inkomende live-videostream te detecteren en verzendt gebeurtenissen naar het knooppunt van de IoT Hub-sink. Deze gebeurtenissen worden vervolgens doorgegeven aan de IoT Edge Hub, en kunnen nu worden waargenomen. Voer hiervoor de volgende stappen uit.

1. Open het deelvenster Explorer in Visual Studio Code en zoek Azure IoT Hub in de linkerbenedenhoek.
2. Vouw het apparaatknooppunt uit.
3. Klik met de rechtermuisknop op lva-sample-device en kies de optie Bewaking van ingebouwde gebeurtenisbewaking starten.

![Bewaking van IOT Hub-gebeurtenissen starten](./media/quickstarts/start-monitoring-iothub-events.png)

In het venster Uitvoer ziet u de volgende berichten:

```
[IoTHubMonitor] [7:44:33 AM] Message received from [lva-sample-device/lvaEdge]:
{
    "body": {
    "timestamp": 143005362606360,
    "inferences": [
        {
        "type": "motion",
        "motion": {
            "box": {
            "l": 0.828452,
            "t": 0.455224,
            "w": 0.1,
            "h": 0.088889
            }
        }
        },
        {
        "type": "motion",
        "motion": {
            "box": {
            "l": 0.661088,
            "t": 0.597015,
            "w": 0.0625,
            "h": 0.051852
            }
        }
        }
    ]
    },
    "applicationProperties": {
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-1/processors/motionDetection",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-05-19T07:45:34.404Z",
    "dataVersion": "1.0"
    }
}
```

Let op het volgende in het bovenstaande bericht

* Het bericht bevat een sectie 'body' en een sectie 'applicationProperties'. Lees het artikel [IoT Hub-bericht maken en lezen](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct) om te begrijpen wat deze secties voorstellen.
* 'subject' in applicationProperties verwijst naar het knooppunt in MediaGraph van waaruit het bericht is gegenereerd. In dit geval is het bericht afkomstig van de bewegingsdetectieprocessor.
* 'eventType' in applicationProperties geeft aan dat dit een analytische gebeurtenis is.
* 'eventTime' geeft de tijd aan waarop de gebeurtenis heeft plaatsgevonden.
* 'body' bevat gegevens over de analytische gebeurtenis. In dit geval is de gebeurtenis een deductiegebeurtenis en daarom bevat de hoofdtekst ('body') de gegevens 'timestamp' en 'inferences'.
* De sectie 'inferences' geeft aan dat het 'type' 'motion' is en dat er aanvullende gegevens zijn over de gebeurtenis 'motion'.

Als u MediaGraph enige tijd uitvoert, wordt het volgende bericht ook weergegeven in het uitvoervenster:

```
[IoTHubMonitor] [7:47:45 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1588948185746703 1 IN IP4 172.xx.xx.xx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-19T07:47:45.747Z"
  }
}
```

Let op het volgende in het bovenstaande bericht

* 'subject' in applicationProperties geeft aan dat het bericht is gegenereerd op basis van het knooppunt van de RTSP-bron in de mediagraaf.
* 'eventType' in applicationProperties geeft aan dat dit een diagnostische gebeurtenis is.
* 'body' bevat gegevens over de diagnostische gebeurtenis. In dit geval is de gebeurtenis MediaSessionEstablished en dus de hoofdtekst.

## <a name="invoke-additional-direct-methods-to-clean-up"></a>Aanvullende directe methoden aanroepen om op te schonen

Roep nu directe methoden aan voor het deactiveren en verwijderen van het graafexemplaar (in die volgorde).

### <a name="invoke-graphinstancedeactivate"></a>GraphInstanceDeactivate aanroepen

Roep de directe methode voor GraphInstanceDeactivate aan met de volgende nettolading.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

Binnen een paar seconden moet in het venster Uitvoer het volgende antwoord verschijnen:

```
[DirectMethod] Invoking Direct Method [GraphInstanceDeactivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Statuscode 200 geeft aan dat het graafexemplaar is gedeactiveerd.

Voer de volgende stappen uit.

* Roep GraphInstanceGet aan, zoals in de eerdere secties is aangegeven en bekijk de waarde van 'state'.

### <a name="invoke-graphinstancedelete"></a>GraphInstanceDelete aanroepen

Roep de directe methode voor GraphInstanceDelete aan met de volgende nettolading

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

Binnen een paar seconden moet in het venster Uitvoer het volgende antwoord verschijnen:

```
[DirectMethod] Invoking Direct Method [GraphInstanceDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Statuscode 200 in het antwoord geeft aan dat het graafexemplaar is verwijderd.

### <a name="invoke-graphtopologydelete"></a>GraphTopologyDelete aanroepen

Roep de directe methode voor GraphTopologyDelete aan met de volgende nettolading:

```
{
    "@apiVersion" : "1.0",
    "name" : "MotionDetection"
}
```

Binnen een paar seconden moet in het venster Uitvoer het volgende antwoord verschijnen:

```
[DirectMethod] Invoking Direct Method [GraphTopologyDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Statuscode 200 geeft aan dat de graaftopologie is verwijderd.

Voer de volgende stappen uit.

* Roep GraphTopologyList aan en merk op dat deze module geen graaftopologieën bevat.
* Roep GraphInstanceList aan met dezelfde nettolading als GraphTopologyList en merk op dat er geen graafexemplaren worden opgesomd.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing verder niet gaat gebruiken, verwijdert u de resources die u in deze quickstart hebt gemaakt.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het opnemen van video met behulp van Live Video Analytics in IoT Edge
* Meer informatie over diagnostische berichten.
