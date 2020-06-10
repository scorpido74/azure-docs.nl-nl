---
title: Beweging detecteren, video opnemen op Edge-apparaten - Azure
description: In deze Quick start ziet u hoe u live video Analytics op IoT Edge kunt gebruiken om de live video-feed van een (gesimuleerde) IP-camera te analyseren, te detecteren of er beweging is, en als dit het geval is, een MP4-videoclip op te nemen naar het lokale bestandssysteem op het edge-apparaat.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: d824870ea95922bbbdbf01cf2c95692522936f85
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261684"
---
# <a name="quickstart-detect-motion-record-video-on-edge-devices"></a>Quickstart: Beweging detecteren, video opnemen op Edge-apparaten
 
In deze Quick start ziet u hoe u live video Analytics op IoT Edge kunt gebruiken om de live video-feed van een (gesimuleerde) IP-camera te analyseren, te detecteren of er beweging is, en als dit het geval is, een MP4-videoclip op te nemen naar het lokale bestandssysteem op het edge-apparaat. Er wordt gebruikgemaakt van een Azure-VM als een IoT Edge-apparaat en van een gesimuleerde live-videostream. Dit artikel is gebaseerd op voorbeeldcode die is geschreven in C#.

Dit artikel bouwt voort op [deze](detect-motion-emit-events-quickstart.md) quickstart. 

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Visual Studio Code](https://code.visualstudio.com/) op uw computer met de volgende extensies:
    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) geïnstalleerd op uw systeem
* Als u [deze](detect-motion-emit-events-quickstart.md) quickstart nog niet eerder hebt gedaan, voer dan de volgende stappen uit:
     * [Azure-resources instellen](detect-motion-emit-events-quickstart.md#set-up-azure-resources)
     * [De ontwikkelomgeving instellen](detect-motion-emit-events-quickstart.md#set-up-your-development-environment)
     * [Het IoT Edge-implementatiemanifest genereren en implementeren](detect-motion-emit-events-quickstart.md#generate-and-deploy-the-iot-edge-deployment-manifest)
     * [Bewakingsgebeurtenissen voorbereiden](detect-motion-emit-events-quickstart.md#prepare-for-monitoring-events)

> [!TIP]
> Tijdens de installatie van de Azure IoT Tools wordt u mogelijk gevraagd om Docker te installeren. U kunt deze vraag negeren.

## <a name="review-the-sample-video"></a>De voorbeeldvideo bekijken
Als onderdeel van de bovenstaande stappen voor het instellen van de Azure-resources, wordt een (korte) video van een parkeerplaats gekopieerd naar de virtuele Linux-machine in Azure die wordt gebruikt als het IoT Edge-apparaat. Dit videobestand wordt gebruikt voor het simuleren van een livestream voor deze zelfstudie.

U kunt een toepassing als [VLC Player](https://www.videolan.org/vlc/) gebruiken, deze starten, op Control+N drukken en [deze](https://lvamedia.blob.core.windows.net/public/lots_015.mkv) koppeling naar de video over de parkeerplaats plakken om met afspelen te beginnen. Na ongeveer 5 seconden ziet u een witte auto over de parkeerplaats rijden.

Wanneer u de onderstaande stappen hebt uitgevoerd, hebt u Live Video Analytics in IoT Edge gebruikt om de beweging van de auto te detecteren en een videoclip op te nemen die na ongeveer 5 seconden begint.

## <a name="overview"></a>Overzicht

![overview](./media/quickstarts/overview-qs4.png)

Het bovenstaande diagram laat zien hoe de signalen in deze quickstart stromen. Een edge-module (details [hier](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)) simuleert een IP-camera die als host fungeert voor een RTSP-server. Een [RTSP-bron](media-graph-concept.md#rtsp-source)knooppunt haalt de video-feed van deze server, en verstuurt videoframes naar het [bewegingsdetectieprocessor](media-graph-concept.md#motion-detection-processor)knooppunt. De RTSP-bron verzendt diezelfde videoframes naar een [signaalpoortprocessor](media-graph-concept.md#signal-gate-processor)knoop punt, dat gesloten blijft totdat het wordt geactiveerd door een gebeurtenis.

Wanneer de bewegingsdetectieprocessor bepaalt dat er beweging in de video aanwezig is, verzendt deze een gebeurtenis naar het signaalpoortprocessorknooppunt waardoor dit wordt geactiveerd. De poort wordt geopend gedurende de geconfigureerde tijd, waarin videoframes worden verzonden naar het [bestandssink](media-graph-concept.md#file-sink)knooppunt. Dit sinkknooppunt registreert de video als een MP4-bestand op de geconfigureerde locatie in het lokale bestandssysteem van uw edge-apparaat.

In deze snelstart gaat u het volgende doen:

1. De mediagraaf maken en implementeren
1. De resultaten interpreteren
1. Resources opschonen

## <a name="examine-and-edit-the-sample-files"></a>De voorbeeld bestanden bekijken en bewerken
Als onderdeel van de vereisten hebt u de voorbeeldcode naar een map gedownload. Start Visual Studio Code en open de map.

1. Blader in Visual Studio code naar 'src/edge'. U ziet het. env-bestand dat u hebt gemaakt, samen met een paar sjabloonbestanden voor de implementatie
    * De implementatiesjabloon verwijst naar het implementatiemanifest voor het edge-apparaat met enkele tijdelijke waarden. Het. env-bestand bevat de waarden voor die variabelen.
1. Blader vervolgens naar de map src/cloud-to-device-console-app. Hier ziet u het bestand appsettings.json dat u hebt gemaakt, samen met enkele andere bestanden:
    * c2d-console-app.csproj: dit is het projectbestand voor Visual Studio Code
    * operations.json: dit bestand bevat de verschillende bewerkingen die u het programma wilt laten uitvoeren
    * Program.cs: dit is de voorbeeldcode van het programma. Deze doet het volgende:

        * Laadt de app-instellingen
        * Roept directe methoden aan die worden weergegeven door de module Live Video Analytics in IoT Edge. U kunt de module gebruiken om live-videostreams te analyseren door de bijbehorende [directe methoden](direct-methods.md) aan te roepen 
        * Wordt onderbroken zodat u een controle kunt uitvoeren op de uitvoer van het programma in het TERMINAL-venster en de gebeurtenissen die worden gegenereerd door de module in het UITVOER-venster
        * Hiermee worden directe methoden voor het opschonen van resources aangeroepen   

1. Breng de volgende wijzigingen aan in het bestand operations.json
    * Wijzig de koppeling naar de graaftopologie: `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-motion-files/topology.json"`
    * Bewerk onder GraphInstanceSet de naam van de graaftopologie zodat deze overeenkomt met de waarde in de bovenstaande koppeling `"topologyName" : "EVRToFilesOnMotionDetection"`
    * Bewerk ook de RTSP-URL zodat deze naar het gewenste videobestand wijst `"value": "rtsp://rtspsim:554/media/lots_015.mkv"`
    * Bewerk de naam onder GraphTopologyDelete `"name": "EVRToFilesOnMotionDetection"`

## <a name="review---check-status-of-the-modules"></a>Controle: bekijk de status van de modules
Als u bij de stap [Het IoT Edge-implementatiemanifest genereren en implementeren](detect-motion-emit-events-quickstart.md#generate-and-deploy-the-iot-edge-deployment-manifest) in Visual Studio Code het knooppunt 'lva-sample-device' onder AZURE IOT HUB uitvouwt (in de sectie linksonder), ziet u de volgende geïmplementeerde modules

    1. De Live Video Analytics-module met de naam 'lvaEdge'
    1. Een module met de naam 'rtspsim', die een RTSP-server simuleert en fungeert als de bron van een live videofeed

        ![Modules](./media/quickstarts/lva-sample-device-node.png)


## <a name="review---prepare-for-monitoring-events"></a>Controle: bewakingsgebeurtenissen voorbereiden
Controleer of u de stappen bij [Bewakingsgebeurtenissen voorbereiden](detect-motion-emit-events-quickstart.md#prepare-for-monitoring-events) hebt voltooid

![Bewaking van ingebouwd gebeurteniseindpunt starten](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-sample-program"></a>Het voorbeeldprogramma uitvoeren

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
       "name": "Sample-Graph",
       "properties": {
         "topologyName": "EVRToFilesOnMotionDetection",
         "description": "Sample graph description",
         "parameters": [
           {
             "name": "rtspUrl",
             "value": "rtsp://rtspsim:554/media/lots_015.mkv"
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
     * Een aanroep van GraphInstanceActivate om het graafexemplaar te starten en de videostroom te starten
     * Een tweede aanroep van GraphInstanceList om aan te geven dat het graafexemplaar inderdaad actief is
1. De uitvoer in het TERMINAL-venster wordt nu onderbroken met de prompt 'Druk op ENTER om door te gaan'. Druk nog niet op Enter. U kunt omhoog schuiven om de nettoladingen voor het JSON-antwoord te zien voor de directe methoden die u hebt aangeroepen
1. Als u nu overschakelt naar het UITVOER-venster in Visual Studio Code, worden berichten die worden verzonden naar de IoT Hub, weergegeven door de module Live Video Analytics in IoT Edge.
     * Deze berichten worden besproken in de sectie hieronder
1. De mediagraaf blijft actief en resultaten afdrukken: de RTSP-simulator blijft de bronvideo herhalen. Als u de mediagraaf wilt stoppen, gaat u terug naar het TERMINAL-venster en klikt u op Enter. De volgende serie aanroepen wordt gedaan om resources op te schonen:
     * Een aanroep van GraphInstanceDeactivate om het graafexemplaar te deactiveren
     * Een aanroep van GraphInstanceDelete om het exemplaar te verwijderen
     * Een aanroep van GraphTopologyDelete om de topologie te verwijderen
     * Een laatste aanroep van GraphTopologyList om te laten zien dat de lijst nu leeg is

## <a name="interpret-results"></a>Resultaten interpreteren 
Wanneer u de mediagraaf uitvoert, worden de resultaten van het knooppunt van de bewegingsdetectorprocessor via het knooppunt van de IoT Hub-sink verzonden naar de IoT-hub. De berichten die u ziet in het UITVOER-venster van Visual Studio Code bevatten een sectie 'body' en een sectie 'applicationProperties'. Lees [dit](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct) artikel als u wilt weten wat deze secties inhouden.

In de onderstaande berichten worden de eigenschappen van de toepassing en de inhoud van de hoofdtekst door de module Live Video Analytics bepaald.

## <a name="mediasession-established-event"></a>MediaSession Established-gebeurtenis

Wanneer een mediagraaf wordt geïnstantieerd, probeert het RTSP-bronknooppunt verbinding te maken met de RTSP-server die wordt uitgevoerd op de rtspsim-live555-container. Als dit lukt, wordt deze gebeurtenis afgedrukt:

```
[IoTHubMonitor] [05:37:21 AM] Message received from [lva-sample-device/lvaEdge]:
{  
"body": {
"sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"  
},  
"applicationProperties": {  
    "dataVersion": "1.0",  
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",  
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",  
    "eventTime": "2020-05-21T05:37:21.398Z",
    }  
}
```

* Het bericht is een diagnostische gebeurtenis, MediaSessionEstablished, die aangeeft dat het RTSP-bronknooppunt (het subject) verbinding kan maken met de RTSP-simulator en een (gesimuleerde) live-feed kan gaan ontvangen.
* Het 'subject' in applicationProperties verwijst naar het knooppunt in de graaftopologie van waaruit het bericht is gegenereerd. In dit geval is het bericht afkomstig van het RTSP-bronknooppunt.
* 'eventType' in applicationProperties geeft aan dat dit een diagnostische gebeurtenis is.
* 'eventTime' geeft de tijd aan waarop de gebeurtenis heeft plaatsgevonden.
* 'body' bevat gegevens over de diagnostische gebeurtenis, in dit geval de [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol)-details.


## <a name="recording-started-event"></a>Gebeurtenis RecordingStarted (opname gestart)

Zoals [hier](#overview) wordt uitgelegd, wordt het signaalpoortprocessorknooppunt geactiveerd wanneer er beweging wordt gedetecteerd, en begint het bestandssinkknooppunt in de mediagraaf met het schrijven van een MP4-bestand. Het bestandssinkknooppunt verzendt een operationele gebeurtenis. Het type wordt ingesteld op 'motion' om aan te geven dat het een resultaat is van de bewegingsdetectieprocessor en eventTime vertelt u op welke tijd (UTC) beweging heeft plaatsgevonden. Hieronder ziet u een voorbeeld:

```
[IoTHubMonitor] [05:37:27 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "filePath",
    "outputLocation": "/var/media/sampleFilesFromEVR-filesinkOutput-20200521T053726Z.mp4"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sinks/fileSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-05-21T05:37:27.713Z",
    "dataVersion": "1.0"
  }
}
```

* "subject" in applicationProperties verwijst naar het knooppunt in de mediagraaf waaruit het bericht is gegenereerd. In dit geval is het bericht afkomstig van het bestandssinkknooppunt.
* "eventType" in applicationProperties geeft aan dat dit een operationele gebeurtenis is.
* "eventTime" geeft de tijd aan waarop de gebeurtenis heeft plaatsgevonden. U ziet dat dit 5-6 seconden later is dan het tijdstip van MediaSessionEstablished en het begin van de videostroom. Dit komt overeen met het punt op 5-6 seconden toen de [auto het parkeerterrein kwam oprijden](#review-the-sample-video)
* "body" bevat gegevens over de operationele gebeurtenis, in dit geval "outputType"- en "outputLocation"-gegevens.
* "outputType" geeft aan dat dit gegevens zijn over het bestandspad
* "outputLocation" geeft de locatie van het MP4-bestand vanuit de edge-module

## <a name="recording-stopped-and-available-events"></a>Gebeurtenissen RecordingStopped (opname gestopt) en RecordingAvailable (opname beschikbaar)

Als u de eigenschappen van het signaalpoortprocessorknoopunt in de [graaftopologie](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-files/topology.json) bekijkt, ziet u dat de activeringstijden zijn ingesteld op 5 seconden. Ongeveer 5 seconden nadat de RecordingStarted-gebeurtenis is ontvangen, krijgt u dus een
* RecordingStopped-gebeurtenis, waarmee wordt aangegeven dat de opname is gestopt
* RecordingAvailable-event, waarmee wordt aangegeven dat het MP4-bestand nu kan worden bekeken

De twee gebeurtenissen worden doorgaans enkele seconden na elkaar verzonden.

### <a name="playing-back-the-mp4-clip"></a>De MP4-clip afspelen

1. De MP4-bestanden worden naar een map op het edge-apparaat geschreven die u in het .env-bestand hebt geconfigureerd met deze sleutel: OUTPUT_VIDEO_FOLDER_ON_DEVICE. Als u deze op de standaardwaarde hebt laten staan, bevinden de resultaten zich in /home/lvaadmin/samples/output/
1. Ga naar uw resourcegroep, zoek de VM en maak verbinding met behulp van Bastion

    ![Resourcegroep](./media/quickstarts/resource-group.png)
 
    ![VM](./media/quickstarts/virtual-machine.png)
1. Als u bent aangemeld (met referenties die zijn gegenereerd tijdens [deze](detect-motion-emit-events-quickstart.md#set-up-azure-resources) stap), gaat u bij de opdrachtprompt naar de relevante map (standaard: /home/lvaadmin/samples/output) en ziet u de MP4-bestanden daar. U kunt de [bestanden met SCP overbrengen](https://docs.microsoft.com/azure/virtual-machines/linux/copy-files-to-linux-vm-using-scp) naar uw lokale computer, en ze afspelen met [VLC player](https://www.videolan.org/vlc/) of een andere MP4-speler.

    ![Uitvoer](./media/quickstarts/samples-output.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u de andere quickstarts wilt proberen, moet u de gemaakte resources bewaren. Anders gaat u naar Azure Portal, bladert u naar de resourcegroepen, selecteert u de resourcegroep waaronder u deze quickstart hebt uitgevoerd en verwijdert u alle resources.

## <a name="next-steps"></a>Volgende stappen

* Voer de quickstart [Live Video Analytics uitvoeren met uw eigen model](use-your-model-quickstart.md) uit, die laat zien hoe u AI kunt toepassen op live video-feeds.
* Bekijk extra uitdagingen voor gevorderde gebruikers:

    * Gebruik een [IP-camera](https://en.wikipedia.org/wiki/IP_camera) met ondersteuning voor RTSP in plaats van de RTSP-simulator. U kunt zoeken naar IP-camera's met RTSP-ondersteuning op de pagina met [ONVIF-compatibele](https://en.wikipedia.org/wiki/IP_camera) producten door te zoeken naar apparaten die voldoen aan de profielen G, S of T.
    * Gebruik een AMD64-of x64-Linux-apparaat (in plaats van een Azure Linux-VM). Dit apparaat moet zich in hetzelfde netwerk als de IP-camera bevinden. Volg de instructies in [Azure IoT Edge-runtime installeren op Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) en vervolgens die in [Uw eerste IoT Edge-module implementeren op een virtueel Linux-apparaat](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) om het apparaat te registreren bij Azure IoT Hub.
