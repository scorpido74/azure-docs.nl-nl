---
title: Live video analyseren met uw eigen model - Azure
description: In deze quickstart gebruikt u computer vision om de live videofeed van een (gesimuleerde) IP-camera te analyseren.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 0b502fb4bcfa3a11890167c5ef297463a3c51cdc
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261486"
---
# <a name="quickstart-analyze-live-video-with-your-own-model"></a>Quickstart: Live video analyseren met uw eigen model

In deze quickstart ziet u hoe u Live Video Analytics in IoT Edge kunt gebruiken om de live videofeed van een (gesimuleerde) IP-camera te analyseren door een computer vision-model toe te passen om objecten te detecteren. Een subset van de frames in de live videofeed wordt verzonden naar een deductieservice en de resultaten worden verzonden naar de IoT Edge-hub. Er wordt gebruikgemaakt van een Azure-VM als een IoT Edge-apparaat en van een gesimuleerde live-videostream. Dit artikel is gebaseerd op voorbeeldcode die is geschreven in C#.

Dit artikel bouwt voort op [deze](detect-motion-emit-events-quickstart.md) quickstart. 

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Visual Studio Code](https://code.visualstudio.com/) op uw computer met de volgende extensies:
    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) geïnstalleerd op uw systeem
* Als u [deze](detect-motion-emit-events-quickstart.md) quickstart nog niet eerder hebt gedaan, voer dan de volgende stappen uit:
     * [Azure-resources instellen](detect-motion-emit-events-quickstart.md#set-up-azure-resources)

> [!TIP]
> Tijdens de installatie van de Azure IoT Tools wordt u mogelijk gevraagd om Docker te installeren. U kunt deze vraag negeren.

## <a name="review-the-sample-video"></a>De voorbeeldvideo bekijken
Als onderdeel van de bovenstaande stappen voor het instellen van de Azure-resources, wordt een (korte) video van een snelweg gekopieerd naar de virtuele Linux-machine in Azure die wordt gebruikt als het IoT Edge-apparaat. Dit videobestand wordt gebruikt voor het simuleren van een livestream voor deze zelfstudie.

U kunt een toepassing als [VLC Player](https://www.videolan.org/vlc/) gebruiken, deze starten, op Control+N drukken en [deze](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv) link naar de video van de snelweg plakken om met afspelen te beginnen. U ziet dat de beelden van het verkeer op een snelweg zijn en dat er veel voertuigen voorbij komen.

Wanneer u de onderstaande stappen hebt uitgevoerd, hebt u Live Video Analytics in IoT Edge gebruikt voor het detecteren van objecten zoals voertuigen, enzovoort, en het publiceren van deductiegebeurtenissen naar de IoT Edge-hub.

## <a name="overview"></a>Overzicht

![Overzicht](./media/quickstarts/overview-qs5.png)

Het bovenstaande diagram laat zien hoe de signalen in deze quickstart stromen. Een edge-module (details [hier](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)) simuleert een IP-camera die als host fungeert voor een RTSP-server. Een [RTSP-bron](media-graph-concept.md#rtsp-source)knooppunt haalt de video-feed van deze server, en verstuurt videoframes naar het [framefilterprocessor](media-graph-concept.md#frame-rate-filter-processor)-knooppunt. Deze processor beperkt de framesnelheid van de videostream die het [HTTP-extensieprocessor](media-graph-concept.md#http-extension-processor)-knooppunt bereikt. 

Het HTTP-extensie-knooppunt speelt de rol van een proxy, door de videoframes te converteren naar het opgegeven afbeeldingstype en de afbeelding via REST door te sturen naar een andere Edge-module die een AI-model achter een HTTP-eindpunt uitvoert. In dit voorbeeld is die Edge-module gebouwd met behulp van het [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx)-model, waarmee vele soorten objecten kunnen worden gedetecteerd. Het HTTP-extensieprocessor-knooppunt verzamelt de detectieresultaten en publiceert de gebeurtenissen naar het [IoT Hub Sink](media-graph-concept.md#iot-hub-message-sink )-knooppunt, dat deze gebeurtenis vervolgens naar de [IoT Edge-hub](../../iot-edge/iot-edge-glossary.md#iot-edge-hub) stuurt.

In deze quickstart gaat u het volgende doen:

1. De mediagrafiek maken en implementeren.
1. De resultaten interpreteren.
1. Resources opschonen.



## <a name="create-and-deploy-the-media-graph"></a>De mediagrafiek maken en implementeren
    
### <a name="examine-and-edit-the-sample-files"></a>De voorbeeld bestanden bekijken en bewerken

Als onderdeel van de vereisten hebt u de voorbeeldcode naar een map gedownload. Start Visual Studio Code en open de map.

1. Blader in Visual Studio code naar src/edge. U ziet het. env-bestand dat u hebt gemaakt, samen met een paar sjabloonbestanden voor de implementatie.

    * De implementatiesjabloon verwijst naar het implementatiemanifest voor het edge-apparaat met enkele tijdelijke waarden. Het. env-bestand bevat de waarden voor die variabelen.
1. Blader vervolgens naar de map 'src/cloud-to-device-console-app'. Hier ziet u het bestand appsettings.json dat u hebt gemaakt, samen met enkele andere bestanden:

    * c2d-console-app.csproj: dit is het projectbestand voor Visual Studio Code.
    * operations.json: dit bestand bevat de verschillende bewerkingen die u het programma wilt laten uitvoeren.
    * Program.cs: dit is de voorbeeldcode van het programma. Deze doet het volgende:

        * De app-instellingen laden.
        *  Hiermee roept u directe methoden aan die worden weergegeven door de module Live Video Analytics in IoT Edge. U kunt de module gebruiken om live-videostreams te analyseren door [directe methoden](direct-methods.md) aan te roepen 
        * Hiermee pauzeert u zodat u de uitvoer van het programma in het TERMINAL-venster en de gebeurtenissen die worden gegenereerd door de module in het uitvoervenster kunt controleren.
        * Hiermee worden directe methoden voor het opschonen van resources opgeroepen   


1. Breng de volgende wijzigingen aan in het bestand operations.json
    * Wijzig de koppeling naar de graaftopologie: `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/topology.json"`
    * Bewerk onder GraphInstanceSet de naam van de graaftopologie zodat deze overeenkomt met de waarde in de bovenstaande link `"topologyName" : "InferencingWithHttpExtension"`
    * Bewerk de naam onder GraphTopologyDelete `"name": "InferencingWithHttpExtension"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Het IoT Edge-distributiemanifest genereren en implementeren

1. Klik met de rechtermuisknop op het bestand src/edge/deployment.yolov3.template.json en klik op IoT Edge-implementatiemanifest genereren.

    ![IoT Edge-implementatiemanifest genereren](./media/quickstarts/generate-iot-edge-deployment-manifest-yolov3.png)  
1. Hiermee maakt u een manifestbestand in de map src/edge/config met de naam deployment.yolov3.amd64.json.
1. Als u de [quickstart](detect-motion-emit-events-quickstart.md)eerder hebt voltooid, kunt u deze stap overslaan. Anders stelt u de IoT Hub-verbindingsreeks in door naast het deelvenster AZURE IOT HUB in de linkerbenedenhoek te klikken op het pictogram Meer acties. U kunt de tekenreeks uit het bestand appsettings.json kopiëren. (Hier volgt een andere aanbevolen methode om ervoor te zorgen dat u de juiste IoT Hub hebt geconfigureerd in Visual Studio code via de opdracht [IOT hub selecteren](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub)).
    
    ![IoTHub-verbindingsreeks](./media/quickstarts/set-iotconnection-string.png)
1. Klik vervolgens met de rechtermuisknop op src/edge/config/deployment.yolov3.amd64.json en klik op Implementatie voor één apparaat maken. 

    ![Implementatie voor één apparaat maken](./media/quickstarts/create-deployment-single-device.png)
1. Vervolgens wordt u gevraagd om een IoT Hub-apparaat te selecteren. Selecteer lva-sample-device in de vervolgkeuzelijst.
1. Vernieuw in ongeveer 30 seconden de Azure IOT-hub in het gedeelte linksonder en u moet het edge-apparaat hebben met de volgende modules geïmplementeerd:

    1. De Live Video Analytics-module genaamd lvaEdge.
    1. Een module genaamd rtspsim die een RTSP-server simuleert en fungeert als de bron van een live videofeed.
    1. Een module genaamd yolov3, die zoals de naam al doet vermoeden, het YOLOv3-objectdetectiemodel is dat computer vision toepast op de afbeeldingen en meerdere klassen objecttypen retourneert.
 
        ![YOLOv3-objectdetectiemodel](./media/quickstarts/yolov3.png)

### <a name="prepare-for-monitoring-events"></a>Bewakingsgebeurtenissen voorbereiden

Klik met de rechtermuisknop op het Live Video Analytics-apparaat en klik op "Bewaking van ingebouwd gebeurteniseindpunt starten". Deze stap is nodig om de IoT Hub-gebeurtenissen te controleren en te bekijken in het uitvoervenster van Visual Studio Code. 

![Bewaking starten](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program"></a>Het voorbeeldprogramma uitvoeren

1. Start een foutopsporingssessie (druk op F5). In het TERMINAL-venster ziet u enkele berichten verschijnen.
1. operations.json begint met het uitvoeren van aanroepen naar de directe methoden GraphTopologyList en GraphInstanceList. Als u resources hebt opgeschoond na vorige quickstarts, worden er lege lijsten geretourneerd, waarna wordt gepauzeerd zodat u op Enter kunt drukken
   ```
   --------------------------------------------------------------------------
   Executing operation GraphTopologyList
   -----------------------  Request: GraphTopologyList  --------------------------------------------------
   {
   "@apiVersion": "1.0"
   }
   ---------------  Response: GraphTopologyList - Status: 200  ---------------
   {
   "value": []
   }
   --------------------------------------------------------------------------
   Executing operation WaitForInput
   Press Enter to continue
   ```

1. Wanneer u op de Enter-toets drukt in het TERMINAL-venster, wordt de volgende set met aanroepen met de directe methode gemaakt
     * Een aanroep van GraphTopologySet met behulp van topologyUrl hierboven
     * Een aanroep van GraphInstanceSet met behulp van de volgende hoofdtekst
     ```
     {
       "@apiVersion": "1.0",
       "name": "Sample-Graph-1",
       "properties": {
         "topologyName": "InferencingWithHttpExtension",
         "description": "Sample graph description",
         "parameters": [
           {
             "name": "rtspUrl",
             "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
           },
           {
             "name": "rtspUserName",
             "value": "testuser"
           },
           {
             "name": "rtspPassword",
             "value": "testpassword"
           }
         ]
       }
     }
     ```
     * Een aanroep van GraphInstanceActivate om het graafexemplaar te starten en de videostream te starten
     * Een tweede aanroep van GraphInstanceList om aan te geven dat het graafexemplaar inderdaad actief is
1. De uitvoer in het TERMINAL-venster wordt nu onderbroken met de prompt Druk op ENTER om door te gaan. Klik nog niet op Enter. U kunt omhoog schuiven om de nettoladingen voor het JSON-antwoord te zien voor de directe methoden die u hebt aangeroepen
1. Als u nu overschakelt naar het uitvoervenster in Visual Studio Code, worden berichten die worden verzonden naar de IoT-hub, weergegeven door de module Live Video Analytics in IoT Edge.
     * Deze berichten worden besproken in de sectie hieronder
1. De mediagrafiek blijft actief en resultaten afdrukken: de RTSP-simulator blijft de bronvideo herhalen. Als u de mediagrafiek wilt stoppen, gaat u terug naar het TERMINAL-venster en klikt u op Enter. De volgende serie aanroepen worden gebruikt om resources op te schonen:
     * Een aanroep van GraphInstanceDeactivate om het graafexemplaar te deactiveren
     * Een aanroep van GraphInstanceDelete om het exemplaar te verwijderen
     * Een aanroep van GraphTopologyDelete om de topologie te verwijderen
     * Een laatste aanroep van GraphTopologyList om aan te geven dat de lijst nu leeg is

## <a name="interpret-results"></a>Resultaten interpreteren

Wanneer u de mediagrafiek uitvoert, worden de resultaten van het knooppunt van de HTTP-extensieprocessor via het knooppunt van de IoT Hub-sink naar de IoT-hub verzonden. De berichten die u ziet in het UITVOER-venster van Visual Studio Code bevatten een sectie 'body' en een sectie 'applicationProperties'. Lees [dit](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct) artikel als u wilt weten wat deze secties inhouden.

In de onderstaande berichten worden de eigenschappen van de toepassing en de inhoud van de bodytekst door de module Live Video Analytics bepaald. 



### <a name="mediasession-established-event"></a>MediaSession Established-gebeurtenis

Wanneer een mediagrafiek wordt geïnstantieerd, probeert het RTSP-bronknooppunt verbinding te maken met de RTSP-server die wordt uitgevoerd op de rtspsim-live55-container. Als dit lukt, wordt deze gebeurtenis afgedrukt. Het gebeurtenistype is Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 nnn.nn.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-04-09T16:42:18.1280000Z"
  }
}
```

Let op het volgende in het bovenstaande bericht:
* Het bericht is een diagnostische gebeurtenis, MediaSessionEstablished, die aangeeft dat het RTSP-bronknooppunt (het subject) verbinding kan maken met de RTSP-simulator en een (gesimuleerde) live-feed kan gaan ontvangen.
* 'subject' in applicationProperties geeft aan dat het bericht is gegenereerd op basis van het knooppunt van de RTSP-bron in de mediagraaf.
* 'eventType' in applicationProperties geeft aan dat dit een diagnostische gebeurtenis is.
* 'eventTime' geeft de tijd aan waarop de gebeurtenis heeft plaatsgevonden.
* 'body' bevat gegevens over de diagnostische gebeurtenis, in dit geval de [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) -details.

### <a name="inference-event"></a>Deductiegebeurtenis

Het HTTP-extensieprocessor-knooppunt ontvangt deductieresultaten van de yolov3-module en verzendt deze via het IoT Hub Sink-knooppunt als deductiegebeurtenissen. In deze gebeurtenissen wordt het type ingesteld op 'entiteit' om aan te geven dat het een entiteit is, zoals een auto of vrachtwagen, en de eventTime vertelt u op welk tijdstip (UTC) het object is gedetecteerd. Hieronder ziet u een voorbeeld waarin twee auto's zijn gedetecteerd met verschillende niveaus van betrouwbaarheid in hetzelfde videoframe.

```
[IoTHubMonitor] [11:37:17 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "entity": {
          "box": {
            "h": 0.0344108157687717,
            "l": 0.5756940841674805,
            "t": 0.5929375966389974,
            "w": 0.04484643936157227
          },
          "tag": {
            "confidence": 0.8714089393615723,
            "value": "car"
          }
        },
        "type": "entity"
      },
      {
        "entity": {
          "box": {
            "h": 0.03960910373263889,
            "l": 0.2750667095184326,
            "t": 0.6102327558729383,
            "w": 0.031027007102966308
          },
          "tag": {
            "confidence": 0.7042660713195801,
            "value": "car"
          }
        },
        "type": "entity"
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-04-23T06:37:16.097Z"
  }
}
```

Let op het volgende in het bovenstaande bericht:

* 'subject' in applicationProperties verwijst naar het knooppunt in de graaftopologie van waaruit het bericht is gegenereerd. 
* 'eventType' in applicationProperties geeft aan dat dit een analytische gebeurtenis is.
* 'eventTime' geeft de tijd aan waarop de gebeurtenis heeft plaatsgevonden.
* 'body' bevat gegevens over de analytische gebeurtenis. In dit geval is de gebeurtenis een deductiegebeurtenis en daarom bevat de hoofdtekst 'inferences'.
* De sectie 'inferences' geeft aan dat het 'type' 'entiteit' is en dat er aanvullende gegevens zijn over de 'entiteit'.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de andere quickstarts wilt proberen, moet u de gemaakte resources bewaren. Anders gaat u naar de Azure-portal, bladert u naar de resourcegroepen, selecteert u de resourcegroep waaronder u deze quickstart hebt uitgevoerd en verwijdert u alle resources.

## <a name="next-steps"></a>Volgende stappen

Bekijk extra uitdagingen voor ervaren gebruikers:

* Gebruik een [IP-camera](https://en.wikipedia.org/wiki/IP_camera) met ondersteuning voor RTSP in plaats van de RTSP-simulator. U kunt zoeken naar IP-camera's met RTSP-ondersteuning op de pagina met [ONVIF-compatibele](https://www.onvif.org/conformant-products/) producten door te zoeken naar apparaten die voldoen aan de profielen G, S of T.
* Gebruik een AMD64-of x64-Linux-apparaat (in plaats van een Azure Linux-VM). Dit apparaat moet zich in hetzelfde netwerk als de IP-camera bevinden. Volg de instructies in [Azure IoT Edge-runtime installeren op Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) en vervolgens die in [Uw eerste IoT Edge-module implementeren op een virtueel Linux-apparaat](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) om het apparaat te registreren bij Azure IoT Hub.

