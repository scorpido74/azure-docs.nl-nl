---
title: Livevideo analyseren met uw eigen model - Azure
description: In deze quickstart gebruikt u computer vision om de livevideofeed van een (gesimuleerde) IP-camera te analyseren.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 73b8f83b9e3c450612c742a831ac800343b3bbbc
ms.sourcegitcommit: 1383842d1ea4044e1e90bd3ca8a7dc9f1b439a54
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/16/2020
ms.locfileid: "84816690"
---
# <a name="quickstart-analyze-live-video-by-using-your-own-model"></a>Quickstart: Livevideo analyseren met uw eigen model

In deze quickstart leert u hoe u Live Video Analytics in IoT Edge kunt gebruiken om de live-videofeed van een (gesimuleerde) IP-camera te analyseren. U ziet hoe u een computer vision-model kunt toepassen om objecten te detecteren. Een subset van de frames in de livevideofeed wordt verzonden naar een deductieservice. De resultaten worden verzonden naar IoT Edge-hub. 

In deze quickstart wordt gebruikgemaakt van een Azure-VM als een IoT Edge-apparaat en van een gesimuleerde livevideostream. De quickstart is gebaseerd op de voorbeeldcode die is geschreven in C# en bouwt voort op de quickstart [Beweging detecteren en gebeurtenissen verzenden](detect-motion-emit-events-quickstart.md). 

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) als u nog geen account hebt.
* [Visual Studio Code](https://code.visualstudio.com/) met de volgende extensies:
    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* Als u niet de quickstart [Beweging detecteren en gebeurtenissen verzenden](detect-motion-emit-events-quickstart.md) hebt voltooid, moet u [Azure-resources instellen](detect-motion-emit-events-quickstart.md#set-up-azure-resources).

> [!TIP]
> Tijdens de installatie van de Azure IoT Tools wordt u mogelijk gevraagd om Docker te installeren. U kunt de vraag negeren.

## <a name="review-the-sample-video"></a>De voorbeeldvideo bekijken
Bij het instellen van de Azure-resources wordt een korte video van wegverkeer gekopieerd naar de virtuele Linux-machine in Azure die u als IoT Edge-apparaat gebruikt. In deze quickstart wordt het videobestand gebruikt voor het simuleren van een livestream.

Open een toepassing als [VLC Media Player](https://www.videolan.org/vlc/). Selecteer CTRL + N en plak vervolgens een link naar [de video](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv) om het afspelen te starten. U ziet beelden van veel voertuigen die over de snelweg rijden.

In deze quickstart gebruikt u Live Video Analytics in IoT Edge om objecten, zoals auto's en personen, te detecteren. U publiceert gekoppelde deductiegebeurtenissen naar IoT Edge-hub.

## <a name="overview"></a>Overzicht

![Overzicht](./media/quickstarts/overview-qs5.png)

Dit diagram laat zien hoe de signalen in deze quickstart stromen. Een [Edge-module](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simuleert een IP-camera die als host fungeert voor een RTSP-server (Real-Time Streaming Protocol). Een [RTSP-bron](media-graph-concept.md#rtsp-source)-knooppunt haalt de videofeed van deze server, en verstuurt videoframes naar het [framefilterprocessor](media-graph-concept.md#frame-rate-filter-processor)-knooppunt. Deze processor beperkt de framesnelheid van de videostream die het knooppunt [HTTP-extensieprocessor](media-graph-concept.md#http-extension-processor) bereikt. 

Het knooppunt HTTP-extensie speelt de rol van een proxy. Het converteert videoframes naar het opgegeven afbeeldingstype. Vervolgens wordt de afbeelding via REST doorgestuurd naar een andere Edge-module die een AI-model achter een HTTP-eindpunt uitvoert. In dit voorbeeld wordt die Edge-module gebouwd met behulp van het [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx)-model, dat vele soorten objecten kan detecteren. Het HTTP-extensieprocessor-knooppunt verzamelt de detectieresultaten en publiceert de gebeurtenissen naar het [IoT Hub Sink](media-graph-concept.md#iot-hub-message-sink)-knooppunt. Het knooppunt verzendt die gebeurtenissen vervolgens naar [IoT Edge-hub](../../iot-edge/iot-edge-glossary.md#iot-edge-hub).

In deze quickstart gaat u het volgende doen:

1. De mediagrafiek maken en implementeren.
1. De resultaten interpreteren.
1. Resources opschonen.



## <a name="create-and-deploy-the-media-graph"></a>De mediagrafiek maken en implementeren
    
### <a name="examine-and-edit-the-sample-files"></a>De voorbeeld bestanden bekijken en bewerken

Als onderdeel van de vereisten hebt u de voorbeeldcode naar een map gedownload. Volg deze stappen om de voorbeeldbestanden te bekijken en te bewerken.

1. Ga in Visual Studio Code naar *src/edge*. U ziet het bestand *.env* en enkele implementatiesjabloonbestanden.

    De implementatiesjabloon verwijst naar het implementatiemanifest voor het edge-apparaat. Deze bevat enkele tijdelijke waarden. Het *.env*-bestand bevat de waarden voor die variabelen.

1. Ga naar de map *src/cloud-to-device-console-app*. Hier ziet u het bestand *appsettings.json* en enkele andere bestanden:

    * ***c2d-console-app.csproj***: het projectbestand voor Visual Studio Code.
    * ***operations.json***: een lijst met de bewerkingen die u het programma wilt laten uitvoeren.
    * ***Program.cs***: de voorbeeldcode van het programma. Deze code:

        * De app-instellingen laden.
        * Roept directe methoden aan die worden weergegeven door de module Live Video Analytics in IoT Edge. U kunt de module gebruiken om livevideostreams te analyseren door de bijbehorende [directe methoden](direct-methods.md) aan te roepen.
        * Pauzeert, zodat u de uitvoer van het programma kunt controleren in het **TERMINAL**-venster en de gebeurtenissen die zijn gegenereerd door de module kunt controleren in het **UITVOER**-venster.
        * Roept directe methoden aan voor het opschonen van resources.


1. Bewerk het bestand *operations.json*:
    * Wijzig de link naar de graaftopologie:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/topology.json"`

    * Bewerk onder `GraphInstanceSet` de naam van de graaftopologie zodat deze overeenkomt met de waarde in de voorgaande link:

      `"topologyName" : "InferencingWithHttpExtension"`

    * Bewerk de naam onder `GraphTopologyDelete`:

      `"name": "InferencingWithHttpExtension"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Het IoT Edge-implementatiemanifest genereren en implementeren

1. Klik met de rechtermuisknop op het bestand *src/edge/ deployment.yolov3.template.json* en klik op **IoT Edge-implementatiemanifest genereren**.

    ![IoT Edge-implementatiemanifest genereren](./media/quickstarts/generate-iot-edge-deployment-manifest-yolov3.png)  

    Het manifestbestand *deployment.yolov3.amd64.json* wordt gemaakt in de map *src/edge/config*.

1. Als u de quickstart [Beweging detecteren en gebeurtenissen verzenden](detect-motion-emit-events-quickstart.md) hebt voltooid, kunt u deze stap overslaan. 

    Als dat niet het geval is, selecteert u in het deelvenster **AZURE IOT HUB** in de linkerbenedenhoek het pictogram **Meer acties** en selecteert u vervolgens **IoT Hub-verbindingsreeks instellen**. U kunt de tekenreeks uit het bestand *appsettings.json* kopiëren. Als u er zeker van wilt zijn dat u de juiste IoT-hub in Visual Studio Code hebt geconfigureerd, gebruikt u de [opdracht IoT Hub selecteren](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub).
    
    ![IoT Hub-verbindingsreeks instellen](./media/quickstarts/set-iotconnection-string.png)

1. Klik met de rechtermuisknop op *src/edge/config/ deployment.yolov3.amd64.json* en selecteer **Implementatie voor één apparaat maken**. 

    ![Implementatie voor één apparaat maken](./media/quickstarts/create-deployment-single-device.png)

1. Wanneer u wordt gevraagd om een IoT Hub-apparaat te selecteren, selecteert u **lva-sample-device**.
1. Vernieuw Azure IoT Hub na ongeveer 30 seconden in de linkerbenedenhoek van het venster. Op het edge-apparaat worden nu de volgende geïmplementeerde modules weergegeven:

    * De Live Video Analytics-module met de naam **lvaEdge**
    * De **rtspsim**-module, die een RTSP-server simuleert en fungeert als de bron van een livevideofeed
    * De **yolov3**-module, het YOLOv3-objectdetectiemodel dat computer vision toepast op de afbeeldingen en meerdere klassen objecttypen retourneert
 
      ![Modules die zijn geïmplementeerd op het edge-apparaat](./media/quickstarts/yolov3.png)

### <a name="prepare-to-monitor-events"></a>Het bewaken van gebeurtenissen voorbereiden

Klik met de rechtermuisknop op het Live Video Analytics-apparaat en selecteer **Bewaking van ingebouwd gebeurteniseindpunt starten**. Deze stap is nodig om de IoT Hub-gebeurtenissen te controleren in het venster **UITVOER** van Visual Studio Code. 

![Bewaking starten](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program"></a>Het voorbeeldprogramma uitvoeren

1. Selecteer de toets F5 om een foutopsporingssessie te starten. U ziet de berichten die worden afgedrukt in het venster **TERMINAL**.
1. De code *operations.json* wordt gestart met aanroepen naar de directe methoden `GraphTopologyList` en `GraphInstanceList`. Als u na het voltooien van vorige quickstarts resources hebt opgeschoond, worden er lege lijsten geretourneerd en wordt het proces gepauzeerd. Selecteer de Enter-toets om door te gaan.

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

    In het **TERMINAL**-venster wordt de volgende set aanroepen van directe methoden weergegeven:

     * Een aanroep van `GraphTopologySet` waarin gebruik wordt gemaakt van de voorgaande `topologyUrl`
     * Een aanroep van `GraphInstanceSet` waarin gebruik wordt gemaakt van de volgende hoofdtekst:

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

     * Een aanroep van `GraphInstanceActivate` waarmee het graafexemplaar en de videostroom worden gestart
     * Een tweede aanroep van `GraphInstanceList` waarmee wordt weergegeven dat het graafexemplaar wordt uitgevoerd
1. De uitvoer in het **TERMINAL**-venster wordt gepauzeerd bij een `Press Enter to continue`-prompt. Selecteer Enter nog niet. Schuif omhoog om de nettoladingen voor het JSON-antwoord te zien voor de directe methoden die u hebt aangeroepen.
1. Schakel naar het **UITVOER**-venster in Visual Studio Code. Er zijn berichten te zien die door de module Live Video Analytics in IoT Edge worden verzonden naar de IoT-hub. In de volgende sectie van deze quickstart worden deze berichten besproken.
1. Het uitvoeren van de mediagraaf wordt vervolgd en de resultaten worden afgedrukt. Via de RTSP-simulator wordt de bronvideo continu herhaald. Als u de mediagraaf wilt stoppen, keert u terug naar het **TERMINAL**-venster en selecteert u Enter. 

    Met de volgende reeks aanroepen worden resources opgeschoond:
      * Met een aanroep van `GraphInstanceDeactivate` wordt het graafexemplaar gedeactiveerd.
      * Met een aanroep van `GraphInstanceDelete` wordt het exemplaar verwijderd.
      * Met een aanroep van `GraphTopologyDelete` wordt de topologie verwijderd.
      * Met een aanroep van `GraphTopologyList` wordt ten slotte aangegeven dat de lijst leeg is.

## <a name="interpret-results"></a>Resultaten interpreteren

Wanneer u de mediagraaf uitvoert, worden de resultaten van het HTTP-extensieprocessor-knooppunt via het IoT Hub-sink-knooppunt naar de IoT-hub doorgevoerd. De berichten in het **UITVOER**-venster bevatten een sectie `body` en een sectie `applicationProperties`. Zie [IoT Hub-berichten maken en lezen](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct) voor meer informatie.

In de volgende berichten worden de eigenschappen van de toepassing en de inhoud van de hoofdtekst bepaald door de module Live Video Analytics. 

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished-gebeurtenis

Wanneer een mediagraaf wordt geïnstantieerd, probeert het RTSP-bronknooppunt verbinding te maken met de RTSP-server die wordt uitgevoerd in de rtspsim-live55-container. Als het lukt om de verbinding tot stand te brengen, wordt de volgende gebeurtenis afgedrukt. Het gebeurtenistype is `Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished`.

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

In dit bericht ziet u deze gegevens:

* Het bericht is een diagnostische gebeurtenis. `MediaSessionEstablished` geeft aan dat het RTSP-bronknooppunt (het onderwerp) is verbonden met de RTSP-simulator en is begonnen met het ontvangen van een (gesimuleerde) livefeed.
* In `applicationProperties` geeft `subject` aan dat het bericht is gegenereerd op basis van het knooppunt van de RTSP-bron in de mediagraaf.
* In `applicationProperties` geeft `eventType` aan dat deze gebeurtenis een diagnostische gebeurtenis is.
* De `eventTime` geeft de tijd aan waarop de gebeurtenis heeft plaatsgevonden.
* De `body` bevat gegevens over de diagnostische gebeurtenis. In dit geval bevatten de gegevens de details van [Session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol).

### <a name="inference-event"></a>Deductiegebeurtenis

Het knooppunt voor de HTTP-extensieprocessor ontvangt de deductieresultaten van de yolov3-module. Vervolgens worden de resultaten door het knooppunt voor de IoT Hub-sink verzonden als deductiegebeurtenissen. 

In deze gebeurtenissen wordt het type ingesteld op `entity` om aan te geven dat het een entiteit is, zoals een auto of vrachtwagen. De waarde `eventTime` is de UTC-tijd waarop het object is gedetecteerd. 

In het volgende voorbeeld zijn twee auto's gedetecteerd in hetzelfde videoframe, met verschillende betrouwbaarheidsniveaus.

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

Let in de berichten op de volgende details:

* In `applicationProperties` verwijst `subject` naar het knooppunt in de graaftopologie vanwaaruit het bericht is gegenereerd. 
* In `applicationProperties` geeft `eventType` aan dat deze gebeurtenis een analytische gebeurtenis is.
* De waarde `eventTime` is het tijdstip waarop de gebeurtenis heeft plaatsgevonden.
* De sectie `body` bevat gegevens over de analytische gebeurtenis. In dit geval is de gebeurtenis een deductiegebeurtenis en bevat de hoofdtekst daarom `inferences`-gegevens.
* De sectie `inferences` geeft aan dat het `type` `entity` is. Deze sectie bevat aanvullende gegevens over de entiteit.

## <a name="clean-up-resources"></a>Resources opschonen

Als u andere quickstarts wilt proberen, moet u de resources die u hebt gemaakt, bewaren. Anders gaat u in de Azure-portal naar de resourcegroepen, selecteert u de resourcegroep waar u deze quickstart hebt uitgevoerd en verwijdert u vervolgens alle resources.

## <a name="next-steps"></a>Volgende stappen

Bekijk extra uitdagingen voor gevorderde gebruikers:

* Gebruik een [IP-camera](https://en.wikipedia.org/wiki/IP_camera) met ondersteuning voor RTSP in plaats van de RTSP-simulator. U kunt zoeken naar IP-camera's die RTSP ondersteunen op de pagina met [ONVIF-compatibele](https://www.onvif.org/conformant-products/) producten. Zoek naar apparaten die voldoen aan de profielen G, S of T.
* Gebruik een AMD64-of x64-Linux-apparaat (in plaats van een Azure Linux-VM). Dit apparaat moet zich in hetzelfde netwerk als de IP-camera bevinden. Volg de instructies in [Azure IoT Edge-runtime installeren op Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux). Registreer vervolgens het apparaat met IoT Hub door de instructies in [Uw eerste IoT Edge-module implementeren op een virtueel Linux-apparaat](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) te volgen.

