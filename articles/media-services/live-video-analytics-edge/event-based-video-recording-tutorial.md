---
title: Zelfstudie voor Video-opname op basis van gebeurtenissen in de cloud en afspelen vanuit de Cloud - Azure
description: In deze zelfstudie leert u hoe u Live Video Analytics in IoT Edge kunt gebruiken om een video-opname op basis van gebeurtenissen uit te voeren in de cloud en af te spelen vanuit de cloud.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: 92367634a2f5785ecbb102db1e03f3d5f12d744e
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/02/2020
ms.locfileid: "84300821"
---
# <a name="tutorial-event-based-video-recording-to-cloud-and-playback-from-cloud"></a>Zelfstudie: Video-opname op basis van gebeurtenissen in de cloud en afspelen vanuit de Cloud

In deze zelfstudie leert u hoe u Live Video Analytics in IoT Edge kunt gebruiken om selectief delen van een live-videobron op te nemen naar mediaservices in de cloud. Deze use case wordt in deze zelfstudie aangeduid als [video-opname op basis van gebeurtenissen](event-based-video-recording-concept.md) (EVR). Hiervoor gebruikt u een AI-model voor objectdetectie om naar objecten in de video te zoeken en videoclips alleen op te nemen wanneer een bepaald type object wordt gedetecteerd. U leert ook hoe u de opgenomen videoclips kunt afspelen met behulp van Media Services. Dit is handig voor een aantal scenario's waarbij een archief met belangrijke videoclips moet worden bewaard.

> [!div class="checklist"]
> * De relevante resources instellen
> * Controleer de code die EVR uitvoert
> * De voorbeeldcode uitvoeren
> * Controleer de resultaten en bekijk de video

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Aanbevolen om te lezen  

U wordt aangeraden de volgende documentatie te lezen

* [Overzicht van Live Video Analytics in IoT Edge](overview.md)
* [Terminologie van Live Video Analytics in IoT Edge](terminology.md)
* [Mediagrafiekconcepten](media-graph-concept.md) 
* [Video-opname op basis van gebeurtenissen](event-based-video-recording-concept.md)
* [Zelfstudie: een IoT Edge-module ontwikkelen](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux)
* [Implementatie .*.template.json bewerken](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)
* Sectie over [routes declareren in het IoT Edge-distributiemanifest](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes)

## <a name="prerequisites"></a>Vereisten

De vereisten voor deze zelfstudie zijn als volgt:

* [Visual Studio Code](https://code.visualstudio.com/) op uw ontwikkelcomputer met de extensie [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) en de extensie [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).

    > [!TIP]
    > U wordt mogelijk gevraagd om docker te installeren. U kunt deze vraag negeren.
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) op uw ontwikkelcomputer.
* Voltooi [Installatiescript voor Live Video Analytics-resources](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) en [De omgeving instellen](https://review.docs.microsoft.com/en-us/azure/media-services/live-video-analytics-edge/detect-motion-emit-events-quickstart?branch=release-preview-media-services-lva#set-up-the-environment)

Na het uitvoeren van de bovenstaande stappen hebt u een aantal Azure-resources geïmplementeerd in het Azure-abonnement, waaronder:

* IoT Hub
* Storage-account
* Azure Media Services-account
* Linux-VM in Azure, met [IoT Edge-runtime](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) geïnstalleerd

## <a name="concepts"></a>Concepten

Video-opname op basis van gebeurtenissen (EVR) verwijst naar het proces van het opnemen van video geactiveerd door een gebeurtenis. Deze gebeurtenis kan worden gegenereerd op basis van de verwerking van het videosignaal zelf (bijvoorbeeld bij het detecteren van een bewegend object in de video) of vanuit een onafhankelijke bron (bijvoorbeeld het openen van een deur). U kunt de opname alleen activeren wanneer een deductieservice detecteert dat er een bepaalde gebeurtenis is opgetreden.  In deze zelfstudie gebruikt u een video van voertuigen die op een snelweg rijden en neemt u videoclips op wanneer een truck wordt gedetecteerd.

![Mediagrafiek](./media/event-based-video-recording-tutorial/overview.png)

In het bovenstaande diagram ziet u een afbeelding van een [mediagrafiek](media-graph-concept.md) en aanvullende modules die helpen het gewenste scenario bereiken. Er worden vier IoT Edge-modules gebruikt:

* Live Video Analytics in IoT Edge-module
* Een Edge-module met een AI-model achter een HTTP-eindpunt. Deze AI-module maakt gebruik van het [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx)-model, waarmee vele soorten objecten kunnen worden gedetecteerd.
* Een aangepaste module om objecten te tellen en te filteren (hierboven Objectteller genoemd) die u in deze zelfstudie gaat bouwen en implementeren.
* Een [RTSP Simulator-module](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) om een RTSP-camera te simuleren.
    
    Zoals in het diagram wordt getoond, gebruikt u een [RTSP-bron](media-graph-concept.md#rtsp-source)knooppunt in de mediagrafiek om de gesimuleerde live video (van verkeer op een snelweg) vast te leggen en deze video naar twee paden te verzenden.

* Het eerste pad gaat naar een [framesnelheidfilterprocessor](media-graph-concept.md#frame-rate-filter-processor)-knooppunt dat videoframes uitvoert met de opgegeven (verlaagde) framesnelheid. Deze videoframes worden verzonden naar een HTTP-extensie-knooppunt, dat vervolgens de frames (als afbeeldingen) doorstuurt naar de AI-module (YOLO v3 – dit is een objectdetector) en de resultaten ontvangt. Dit zijn de objecten (voertuigen in het verkeer) die door het model worden gedetecteerd. Het HTTP-extensie-knooppunt publiceert vervolgens de resultaten via het IoT Hub Message Sink-knooppunt naar de IoT Edge-hub.
* De objecttellermodule is ingesteld om berichten te ontvangen van de IoT Edge-hub, waaronder de objectdetectieresultaten (voertuigen in het verkeer). Deze berichten worden gecontroleerd op objecten van een bepaald type (geconfigureerd via een instelling). Wanneer een dergelijk object wordt gevonden, stuurt deze module een bericht naar de IoT Edge-hub. Deze berichten met 'object gevonden' worden vervolgens doorgestuurd naar het IoT Hub-bronknooppunt van de mediagrafiek. Na ontvangst van een dergelijk bericht activeert het IoT Hub-bronknooppunt in de mediagrafiek het [signaalpoortprocessor](media-graph-concept.md#signal-gate-processor)-knooppunt, waardoor deze laatste gedurende een geconfigureerde tijd wordt geopend. De video stroomt gedurende die tijd door de poort naar het Asset Sink-knooppunt. Dat gedeelte van de live stream wordt vervolgens opgenomen via het [Asset Sink](media-graph-concept.md#asset-sink)-knooppunt naar een [Asset](terminology.md#asset) in uw Azure Media Service-account.

## <a name="set-up-your-development-environment"></a>De ontwikkelomgeving instellen

Controleer voordat u begint of u de derde stap hebt voltooid in [Vereisten](#prerequisites). Zodra het script voor de installatie van de resource is voltooid, klikt u op de accolades om de mapstructuur zichtbaar te maken. Er zijn enkele bestanden gemaakt in de map ~/clouddrive/lva-sample.

![App-instellingen](./media/quickstarts/clouddrive.png)

In deze zelfstudie zijn de volgende dingen van belang:

* ~/clouddrive/lva-sample/edge-deployment/.env: bevat eigenschappen die Visual Studio Code gebruikt om modules te implementeren op een Edge-apparaat.
* ~/clouddrive/lva-sample/appsetting.json: wordt gebruikt door Visual Studio Code voor het uitvoeren van de voorbeeldcode.

U hebt deze bestanden nodig voor de volgende stappen.

1. Kloon de opslagplaats hier https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp.
1. Start Visual Studio Code en open de map waar u de opslagplaats hebt gedownload.
1. In Visual Studio Code bladert u naar de map 'src/cloud-to-device-console-app' en maakt u een bestand met de naam 'appSettings. json'. Dit bestand bevat de instellingen die nodig zijn om het programma uit te voeren.
1. Kopieer de inhoud uit ~/clouddrive/lva-sample/appSettings.JSON-bestand. De tekst moet er als volgt uitzien:

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```

    Met de IoT Hub-verbindingsreeks kunt u Visual Studio Code gebruiken om opdrachten naar de Edge-modules te verzenden via Azure IoT Hub.
    
1. Blader vervolgens naar de map '.src/edge' en maak een bestand met de naam '.env'.
1. Kopieer de inhoud uit ~/clouddrive/lva-sample/.env-bestand. De tekst moet er als volgt uitzien:

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/output"  
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry username>"      
    ```

## <a name="examine-the-template-file"></a>Controleer het sjabloonbestand 

In de vorige stap hebt u Visual Studio Code gestart en de map met de voorbeeldcode geopend.

Blader in Visual Studio code naar src/edge. U ziet het. env-bestand dat u hebt gemaakt, evenals een paar sjabloonbestanden voor de implementatie. Met deze sjabloon wordt gedefinieerd welke Edge-modules u op het Edge-apparaat implementeert (de Azure Linux-VM). Het .env-bestand bevat waarden voor de variabelen die in deze sjablonen worden gebruikt, zoals de aanmeldingsgegevens voor Media Service.

Open src/edge/deployment.objectCounter.template.json. Er zijn vier vermeldingen onder de sectie Modules, deze komen overeen met de bovenstaande items (in de sectie Concepten):

* lvaEdge: dit is de module Live Video Analytics in IoT Edge
* yolov3: dit is de AI-module die is gemaakt met behulp van het YOLO v3-model
* rtspsim: dit is de RTSP-Simulator
* objectCounter: dit is de module die zoekt naar specifieke objecten in de resultaten van yolov3

Zie voor de module objectCounter de teken reeks (${MODULES.objectCounter}) die wordt gebruikt voor de waarde 'image'. Dit is gebaseerd op de [zelfstudie](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux) voor het ontwikkelen van een IoT Edge-module. Visual Studio code herkent automatisch dat de code voor de module Objectteller onder src/edge/modules/objectCounter staat. 

Lees [deze](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) sectie over het declareren van routes in het IoT Edge-distributiemanifest en controleer de routes in het JSON-bestand van de sjabloon. Let op het volgende:

* LVAToObjectCounter wordt gebruikt voor het verzenden van specifieke gebeurtenissen naar een bepaald eindpunt in de objectCounter-module.
* ObjectCounterToLVA wordt gebruikt voor het verzenden van een triggergebeurtenis naar een bepaald eindpunt (dit moet het IoT Hub-bronknooppunt zijn) in de lvaEdge-module.
* objectCounterToIoTHub wordt gebruikt als foutopsporingsprogramma, zodat u de uitvoer van objectCounter kunt zien wanneer u deze zelfstudie uitvoert.

> [!NOTE]
> Controleer de gewenste eigenschappen voor de objectCounter-module, die zijn ingesteld om te zoeken naar objecten die zijn gelabeld als 'vrachtwagen' en een betrouwbaarheidsniveau van ten minste 50% hebben.

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Het IoT Edge-distributiemanifest genereren en implementeren 

Het distributiemanifest definieert welke modules op een Edge-apparaat worden geïmplementeerd en de configuratie-instellingen voor deze modules. Volg deze stappen om een dergelijk manifest van het sjabloonbestand te genereren en vervolgens te implementeren op het Edge-apparaat.

Gebruik Visual Studio Code en volg [deze](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux#build-and-push-your-solution) instructies om u aan te melden bij Docker en IoT Edge-oplossingen te bouwen en te pushen. Gebruik voor deze stap src/edge/deployment.objectCounter.template.json.

![IoT Edge-oplossingen bouwen en pushen](./media/event-based-video-recording-tutorial/build-push.png)

Hiermee maakt u de objectCounter-module voor het tellen van objecten en pusht u de kopie naar uw Azure Container Registry (ACR).

* Controleer of u de omgevingsvariabelen CONTAINER_REGISTRY_USERNAME_myacr en CONTAINER_REGISTRY_PASSWORD_myacr zijn gedefinieerd in het .env-bestand.

Met de bovenstaande stap maakt u het IoT Edge-distributiemanifest op src/edge/config/deployment.objectCounter.amd64.json. Klik met de rechtermuisknop op het bestand en klik op Implementatie maken voor één apparaat.

![Implementatie voor één apparaat maken](./media/quickstarts/create-deployment-single-device.png)

Als dit uw eerste zelfstudie met Live Video Analytics in IoT Edge is, wordt u door Visual Studio Code gevraagd om de IoTHub-verbindingsreeks in te voeren. U kunt deze kopiëren uit het bestand appsettings.json.

Vervolgens wordt u door Visual Studio Code gevraagd om een IoT hub-apparaat te selecteren. Selecteer uw IoT Edge-apparaat (dit moet 'lva-sample-device' zijn).

Nu is de implementatie van Edge-modules op uw IoT Edge-apparaat gestart.
Vernieuw in ongeveer 30 seconden de Azure IoT-hub in het gedeelte linksonder in Visual Studio Code en u zult zien dat er vier modules zijn geïmplementeerd (lvaEdge, rtspsim, yolov3 en objectCounter).

![4 modules geïmplementeerd](./media/event-based-video-recording-tutorial/iot-hub.png)

## <a name="prepare-for-monitoring-events"></a>Bewakingsgebeurtenissen voorbereiden

Volg de volgende stappen om de gebeurtenissen van de objecttellermodule en van de Live Video Analytics in IoT Edge-module te bekijken:

1. Open het deelvenster Explorer in Visual Studio Code en zoek Azure IoT Hub in de linkerbenedenhoek.
1. Vouw het knooppunt Apparaten uit.
1. Klik met de rechtermuisknop op lva-sample-device en kies de optie **Bewaking van ingebouwde gebeurtenisbewaking starten**.

![Bewaking van ingebouwd gebeurteniseindpunt starten](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-program"></a>Het programma uitvoeren

1. Visual Studio Code, navigeer naar src/cloud-to-device-console-app/operations.json

1. Bewerk het volgende onder het knooppunt GraphTopologySet:

    `"topologyUrl" : "https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json"`
    
1. Bewerk vervolgens onder de knooppunten GraphInstanceSet en GraphTopologyDelete

    `"topologyName" : "EVRtoAssetsOnObjDetect"`
1. Start een foutopsporingssessie (druk op F5). In het TERMINAL-venster ziet u enkele berichten verschijnen.

1. De operations.json begint met het uitvoeren van aanroepen naar GraphTopologyList en GraphInstanceList. Als u resources hebt opgeschoond na vorige quickstarts of zelfstudies, worden er lege lijsten geretourneerd, waarna wordt gepauzeerd zodat u op ENTER kunt drukken, zoals hieronder:

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
    1. Wanneer u op de Enter-toets drukt in het TERMINAL-venster, wordt de volgende set met aanroepen met de directe methode gemaakt.
     * Een aanroep van GraphTopologySet met behulp van topologyUrl hierboven.
     * Een aanroep van GraphInstanceSet met behulp van de volgende bodytekst.
     
        ```
        {
          "@apiVersion": "1.0",
          "name": "Sample-Graph-1",
          "properties": {
            "topologyName": "EVRtoAssetsOnObjDetect",
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
     
1. De uitvoer in het TERMINAL-venster wordt nu onderbroken met de prompt Druk op ENTER om door te gaan. Klik nog niet op Enter. U kunt omhoog schuiven om de nettoladingen voor het JSON-antwoord te zien voor de directe methoden die u hebt aangeroepen.

1. Als u nu overschakelt naar het uitvoervenster in Visual Studio Code, worden berichten die worden verzonden naar de IoT-hub, weergegeven door de module Live Video Analytics in IoT Edge.

     * Deze berichten worden beschreven in de volgende sectie.
     
1. Het exemplaar van de grafiek blijft actief en de video wordt opgenomen. De RTSP-simulator blijft de bronvideo herhalen. Bekijk de berichten zoals beschreven in de sectie hieronder en ga vervolgens terug naar het TERMINAL-venster en druk op Enter om het exemplaar te stoppen. De volgende serie aanroepen worden gebruikt om resources op te schonen:

     * Een aanroep van GraphInstanceDeactivate om het graafexemplaar te deactiveren
     * Een aanroep van GraphInstanceDelete om het exemplaar te verwijderen
     * Een aanroep van GraphTopologyDelete om de topologie te verwijderen
     * Een laatste aanroep van GraphTopologyList om aan te geven dat de lijst nu leeg is

## <a name="interpret-the-results"></a>De resultaten interpreteren 

Bij het uitvoeren van de mediagrafiek stuurt de module Live Video Analytics in IoT Edge bepaalde diagnostische en operationele gebeurtenissen naar de IoT Edge-hub. Deze gebeurtenissen zijn de berichten die u ziet in het uitvoervenster van Visual Studio Code. De berichten bestaan uit een sectie 'bodytekst' en een sectie 'applicationProperties'. Zie [IoT Hub-berichten maken en lezen](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct)als u wilt weten wat deze secties inhouden.

In de onderstaande berichten worden de eigenschappen van de toepassing en de inhoud van de bodytekst door de module Live Video Analytics bepaald.

## <a name="diagnostic-events"></a>Diagnostische gebeurtenissen

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished-gebeurtenis 

Wanneer een mediagrafiek wordt geïnstantieerd, probeert het RTSP-bronknooppunt verbinding te maken met de RTSP-server die wordt uitgevoerd op de RTSP Simulator-container. Als dit lukt, wordt deze gebeurtenis afgedrukt. Het gebeurtenistype is Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished.

```
[IoTHubMonitor] [5:53:17 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-17T17:53:16.981Z",
    "dataVersion": "1.0"
  }
}
```


* Het bericht is een diagnostische gebeurtenis, MediaSessionEstablished, die aangeeft dat het RTSP-bronknooppunt (het subject) verbinding kan maken met de RTSP-simulator en een (gesimuleerde) live-feed kan gaan ontvangen.

* Het 'subject' in applicationProperties verwijst naar het knooppunt in de graaftopologie van waaruit het bericht is gegenereerd. In dit geval is het bericht afkomstig van het RTSP-bronknooppunt.

* 'eventType' in applicationProperties geeft aan dat dit een diagnostische gebeurtenis is.

* ' eventTime ' geeft het tijdstip aan waarop de gebeurtenis heeft plaatsgevonden. Dit is het tijdstip waarop de verkeersvideo (MKV-bestand) als livestream in de module arriveerde.

* 'body' bevat gegevens over de diagnostische gebeurtenis, in dit geval de [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) -details.


## <a name="operational-events"></a>Operationele gebeurtenissen

Nadat de mediagrafiek een tijdje heeft gedraaid, ontvangt u uiteindelijk een gebeurtenis uit de objecttellermodule. 

```
[IoTHubMonitor] [5:53:44 PM] Message received from [lva-sample-device/objectCounter]:
{
  "body": {
    "count": 2
  },
  "applicationProperties": {
    "eventTime": "2020-05-17T17:53:44.062Z"
  }
}
```

De applicationProperties bevat de eventTime, het tijdstip waarop de objecttellermodule constateerde dat de resultaten van de YOLO v3-module interessante objecten (vrachtwagens) bevatten.

Mogelijk ziet u meer van deze gebeurtenissen verschijnen naarmate andere vrachtwagens worden gedetecteerd in de video.

### <a name="recordingstarted-event"></a>RecordingStarted-gebeurtenis

Bijna onmiddellijk nadat de objectteller de gebeurtenis heeft verzonden, ziet u een gebeurtenis van het type Microsoft.Media.Graph.Operational.RecordingStarted

```
[IoTHubMonitor] [5:53:46 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": " 2020-05-17T17:53:46.132Z",
    "dataVersion": "1.0"
  }
}
```

Het 'subject' in applicationProperties verwijst naar het knooppunt van de assetsink in de graaf, waardoor dit bericht is gegenereerd. De hoofdtekst bevat informatie over de uitvoerlocatie, in dit geval de naam van de Azure Media Services-asset waarin de video is opgenomen. U moet deze waarde noteren.

### <a name="recordingavailable-event"></a>RecordingAvailable-gebeurtenis

Wanneer het Asset Sink-knooppunt de video naar de asset heeft geüpload, wordt de gebeurtenis Microsoft.Media.Graph.Operational.RecordingAvailable verzonden

```
[IoTHubMonitor] [5:54:15 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-05-17T17:54:15.808Z",
    "dataVersion": "1.0"
  }
}
```

Deze gebeurtenis geeft aan dat er voldoende gegevens zijn weggeschreven naar de asset opdat afspeelapparaten of clients de video kunnen gaan afspelen. Het 'subject' in applicationProperties verwijst naar het knooppunt AssetSink in de graaf, waardoor dit bericht is gegenereerd. De hoofdtekst bevat informatie over de uitvoerlocatie, in dit geval de naam van de Azure Media Services-asset waarin de video is opgenomen.

### <a name="recordingstopped-event"></a>RecordingStopped-gebeurtenis

Als u de activeringsinstellingen (maximumActivationTime) voor het signaalpoortproces-knooppunt in de [topologie](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json)bekijkt, ziet u dat de poort is ingesteld om te worden gesloten na het verzenden van 30 seconden video. Ongeveer 30 seconden na de RecordingStarted-gebeurtenis ziet u een gebeurtenis van het type Microsoft.Media.Graph.Operational.RecordingStopped, waarmee wordt aangegeven dat het Asset Sink-knooppunt is gestopt met het opnemen van video op de asset.

```
[IoTHubMonitor] [5:54:15 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-05-17T17:54:15.040Z",
    "dataVersion": "1.0"
  }
}
```

Deze gebeurtenis geeft aan dat de opname is gestopt. Het 'subject' in applicationProperties verwijst naar het knooppunt AssetSink in de graaf, waardoor dit bericht is gegenereerd. De hoofdtekst bevat informatie over de uitvoerlocatie, in dit geval de naam van de Azure Media Services-asset waarin de video is opgenomen.

## <a name="media-services-asset"></a>Media Services-asset  

U kunt de Media Services-assset die door de grafiek is gemaakt, controleren door u aan te melden bij de Azure-portal en de video te bekijken.

1. Open uw webbrowser en ga naar de [Azure-portal](https://portal.azure.com/). Voer uw referenties in om u aan te melden bij de portal. De standaardweergave is uw service-dashboard.
1. Zoek uw Media Services-account op tussen de resources die u in uw abonnement hebt en open de blade Account
1. Klik op Assets in de Media Services-vermelding.

    ![Assets](./media/continuous-video-recording-tutorial/assets.png)
1. U ziet hier een asset met de naam sampleAssetFromEVR-LVAEdge-{DateTime}. Dit is de naam die wordt weergegeven in de eigenschap outputLocation van de gebeurtenis RecordingStarted. De assetNamePattern in de topologie bepaalt hoe deze naam wordt gegenereerd.
1. Klik op de asset.
1. Klik op de pagina met assetdetails op **Nieuwe maken** onder het tekstvak Streaming-URL.

    ![Nieuwe asset](./media/continuous-video-recording-tutorial/new-asset.png)

1. Accepteer in de wizard die wordt geopend de standaardopties en klik op Toevoegen. Zie [video afspelen](video-playback-concept.md) voor meer informatie.

    > [!TIP]
    > Zorg ervoor dat uw [streaming-eindpunt](../latest/streaming-endpoint-concept.md) wordt uitgevoerd.
1. De speler zou nu de video moeten laden en u zou op **Play** moeten kunnen drukken om deze weer te geven.

> [!NOTE]
> Omdat de bron van de video een container is die een camerafeed simuleert, zijn de tijdstempels in de video gerelateerd aan het moment waarop u het graafexemplaar hebt geactiveerd en uitgeschakeld. Als u de afspeelknoppen gebruikt die u hebt ingebouwd in de tutorial [ Meerdaagse opnamen afspelen ](playback-multi-day-recordings-tutorial.md), kunt u de tijdstempels zien in de video die op het scherm wordt weergegeven.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de andere zelfstudies wilt proberen, moet u de gemaakte resources bewaren. Anders gaat u naar de Azure-portal, bladert u naar de resourcegroepen, selecteert u de resourcegroep waaronder u deze zelfstudie hebt uitgevoerd en verwijdert u de resourcegroep.

## <a name="next-steps"></a>Volgende stappen

* Gebruik een [IP-camera](https://en.wikipedia.org/wiki/IP_camera) met ondersteuning voor RTSP in plaats van de RTSP-simulator. U kunt zoeken naar IP-camera's met RTSP-ondersteuning op de pagina met [ONVIF-compatibele](https://www.onvif.org/conformant-products/) producten door te zoeken naar apparaten die voldoen aan de profielen G, S of T.
* Gebruik een AMD64-of x64-Linux-apparaat (in plaats van een Azure Linux-VM). Dit apparaat moet zich in hetzelfde netwerk als de IP-camera bevinden. Volg de instructies in [Azure IoT Edge-runtime installeren op Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) en vervolgens die in [Uw eerste IoT Edge-module implementeren op een virtueel Linux-apparaat](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) om het apparaat te registreren bij Azure IoT Hub.
