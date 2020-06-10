---
title: Beweging detecteren en gebeurtenissen verzenden - Azure
description: In deze quickstart leert u hoe u Live Video Analytics in IoT Edge kunt gebruiken om beweging te detecteren en gebeurtenissen kunt verzenden door directe methoden programmatisch aan te roepen.
ms.topic: quickstart
ms.date: 05/29/2020
ms.openlocfilehash: 4986ea13bec5382a8e0ef791e75442e4333e4356
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261585"
---
# <a name="quickstart-detect-motion-and-emit-events"></a>Quickstart: Beweging detecteren en gebeurtenissen verzenden

Deze quickstart begeleidt u door de stappen om aan de slag te gaan met Live Video Analytics in IoT Edge. Er wordt gebruikgemaakt van een Azure-VM als een IoT Edge-apparaat en van een gesimuleerde live-videostream. Nadat u de installatie stappen hebt voltooid, kunt u een gesimuleerde live-videostream uitvoeren via een mediagraaf die beweging in die stream detecteert en rapporteert. In het onderstaande diagram ziet u een grafische weergave van die mediagraaf.

![Live Video Analytics op basis van bewegingsdetectie](./media/analyze-live-video/motion-detection.png) 

Dit artikel is gebaseerd op [voorbeeldcode](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp) geschreven in C#.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Visual Studio Code](https://code.visualstudio.com/) op uw computer met de volgende extensies:
    1. [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    2. [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) geïnstalleerd op uw systeem

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

    Als het script is voltooid, ziet u alle resources die hierboven in uw abonnement zijn vermeld.

1. Zodra het script is voltooid, klikt u op de accolades om de mapstructuur zichtbaar te maken. Er zijn enkele bestanden gemaakt in de map ~/clouddrive/lva-sample. Van belang voor deze quickstart zijn:

     * ~/clouddrive/lva-sample/edge-deployment/.env: bevat eigenschappen die door Visual Studio Code worden gebruikt om modules in een edge-apparaat te implementeren
     * ~/clouddrive/lva-sample/appsetting.json: gebruikt door Visual Studio Code voor het uitvoeren van voorbeeldcode
     
U hebt deze nodig om de bestanden in Visual Studio Code later in de quickstart bij te werken. U kunt deze voorlopig naar een lokaal bestand kopiëren.


 ![App-instellingen](./media/quickstarts/clouddrive.png)

## <a name="set-up-your-development-environment"></a>De ontwikkelomgeving instellen

1. Kloon de opslagplaats vanaf hier https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp.
1. Start Visual Studio Code en open de map waar u de opslagplaats hebt gedownload.
1. In Visual Studio Code bladert u naar de map src/cloud-to-device-console-app en maakt u een bestand met de naam appsettings.json. Dit bestand bevat de instellingen die nodig zijn om het programma uit te voeren.
1. Kopieer de inhoud vanuit het bestand ~/clouddrive/lva-sample/appsettings.json dat in de vorige sectie is gegenereerd (zie stap 5)

    De tekst moet er als volgt uitzien:

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
1. Blader vervolgens naar de map src/edge en maak een bestand met de extensie .env.
1. Kopieer de inhoud vanuit het bestand ~/clouddrive/lva-sample/edge-deployment/.env. De tekst moet er als volgt uitzien:

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry username>"      
    ```

## <a name="examine-the-sample-files"></a>De voorbeeldbestanden bekijken

1. Blader in Visual Studio code naar src/edge. U ziet het. env-bestand dat u hebt gemaakt, samen met een paar sjabloonbestanden voor de implementatie.

    De implementatiesjabloon verwijst naar het implementatiemanifest voor het edge-apparaat met enkele tijdelijke waarden. Het. env-bestand bevat de waarden voor die variabelen.
1. Blader vervolgens naar de map src/cloud-to-device-console-app. Hier ziet u het bestand appsettings.json dat u hebt gemaakt, samen met enkele andere bestanden:

    * c2d-console-app.csproj: het projectbestand voor Visual Studio Code.
    * operations.json: dit bestand bevat de verschillende bewerkingen die u het programma wilt laten uitvoeren.
    * Program.cs: de voorbeeldcode van het programma. Deze doet het volgende:
    
        * Laadt de app-instellingen
        * Roept directe methoden aan die worden weergegeven door de module Live Video Analytics in IoT Edge. U kunt de module gebruiken om live-videostreams te analyseren door de bijbehorende [directe methoden](direct-methods.md) aan te roepen 
        * Wordt onderbroken zodat u een controle kunt uitvoeren op de uitvoer van het programma in het TERMINAL-venster en de gebeurtenissen die worden gegenereerd door de module in het UITVOER-venster
        * Roept directe methoden op om resources op te schonen   

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Het IoT Edge-implementatiemanifest genereren en implementeren

Het implementatiemanifest definieert welke modules worden geïmplementeerd op een edge-apparaat en definieert tevens de configuratie-instellingen voor deze modules. Volg deze stappen om een dergelijk manifest op basis van het sjabloonbestand te genereren en implementeer het vervolgens op het edge-apparaat.

1. Open Visual Studio Code
1. Stel de IoT Hub-verbindingsreeks in door naast het deelvenster AZURE IOT HUB in de linkerbenedenhoek te klikken op het pictogram Meer acties. U kunt de tekenreeks kopiëren vanuit het bestand src/cloud-to-device-console-app/appsettings.json. 

    ![IoT-verbindingsreeks instellen](./media/quickstarts/set-iotconnection-string.png)
1. Klik vervolgens met de rechtermuisknop op het bestand src/edge/deployment.template.json en klik op IoT Edge-implementatiemanifest genereren.
    ![IoT Edge-implementatiemanifest genereren](./media/quickstarts/generate-iot-edge-deployment-manifest.png)

    Hiermee maakt u een manifestbestand in de map src/edge/config met de naam deployment.amd64.json.
1. Klik met de rechtermuisknop op src/edge/config/deployment.amd64.json en klik op Implementatie voor één apparaat maken en selecteer de naam van uw edge-apparaat.

    ![Implementatie voor één apparaat maken](./media/quickstarts/create-deployment-single-device.png)
1. Vervolgens wordt u gevraagd om een IoT Hub-apparaat te selecteren. Selecteer lva-sample-device in de vervolgkeuzelijst.
1. Na ongeveer dertig seconden kunt u de Azure IOT-hub vernieuwen in het gedeelte linksonder en ziet u dat op het edge-apparaat de volgende modules zijn geïmplementeerd:

    * Live Video Analytics in IoT Edge (modulenaam lvaEdge)
    * RTSP-simulator (modulenaam rtspsim)

In de RTSP-simulatormodule wordt een live-videostream gesimuleerd met behulp van een videobestand dat is gekopieerd naar uw edge-apparaat tijdens het uitvoeren van het [installatiescript voor Live Video Analytics-resources](https://github.com/Azure/live-video-analytics/tree/master/edge/setup). In deze fase hebt u de modules geïmplementeerd, maar er zijn geen mediagrafen actief.

## <a name="prepare-for-monitoring-events"></a>Bewakingsgebeurtenissen voorbereiden

U gebruikt de module Live Video Analytics in IoT Edge om beweging in de inkomende live-videostream te detecteren en gebeurtenissen naar de IoT Hub te verzenden. Volg deze stappen om deze gebeurtenissen te bekijken:

1. Open het deelvenster Explorer in Visual Studio Code en zoek Azure IoT Hub in de linkerbenedenhoek.
1. Vouw het apparaatknooppunt uit.
1. Klik met de rechtermuisknop op lva-sample-device en kies de optie Bewaking van ingebouwde gebeurtenisbewaking starten.

    ![Bewaking van ingebouwd gebeurteniseindpunt starten](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-sample-program"></a>Het voorbeeldprogramma uitvoeren

Volg de onderstaande stappen om de voorbeeldcode uit te voeren.
1. Ga in Visual Studio Code naar src/cloud-to-device-console-app/operations.json.
1. Controleer het volgende onder het knooppunt GraphTopologySet:

    ` "topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/motion-detection/topology.json"`
1. Controleer vervolgens of op de knooppunten GraphInstanceSet en GraphTopologyDelete de waarde van topologyName overeenkomt met de waarde van de eigenschap 'name' in de bovenstaande graaftopologie:

    `"topologyName" : "MotionDetection"`
    
1. Start een foutopsporingssessie (druk op F5). In het TERMINAL-venster ziet u enkele berichten verschijnen.
1. operations.json begint met het uitvoeren van aanroepen naar GraphTopologyList en GraphInstanceList. Als u resources hebt opgeschoond na vorige quickstarts, worden er lege lijsten geretourneerd, waarna wordt gepauzeerd zodat u op Enter kunt drukken.

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
         "topologyName": "MotionDetection",
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
     
     * Een aanroep van GraphInstanceActivate om het graafexemplaar te starten en de videostroom te starten.
     * Een tweede aanroep van GraphInstanceList om aan te geven dat het graafexemplaar inderdaad actief is.
1. De uitvoer in het TERMINAL-venster wordt nu onderbroken met de prompt 'Druk op Enter om door te gaan'. Klik nog niet op Enter. U kunt omhoog schuiven om de nettoladingen voor het JSON-antwoord te zien voor de directe methoden die u hebt aangeroepen
1. Als u nu overschakelt naar het UITVOER-venster in Visual Studio Code, worden berichten die worden verzonden naar de IoT-hub, weergegeven door de module Live Video Analytics in IoT Edge.
     * Deze berichten worden besproken in de sectie hieronder
1. De mediagraaf blijft actief en resultaten afdrukken: de RTSP-simulator blijft de bronvideo herhalen. Als u de mediagraaf wilt stoppen, gaat u terug naar het TERMINAL-venster en klikt u op Enter. De volgende serie aanroepen worden gedaan om resources op te schonen:
     * Een aanroep van GraphInstanceDeactivate om het graafexemplaar te deactiveren
     * Een aanroep van GraphInstanceDelete om het exemplaar te verwijderen
     * Een aanroep van GraphTopologyDelete om de topologie te verwijderen
     * Een laatste aanroep van GraphTopologyList om aan te geven dat de lijst nu leeg is

## <a name="interpret-results"></a>Resultaten interpreteren

Wanneer u de mediagraaf uitvoert, worden de resultaten van het knooppunt van de bewegingsdetectorprocessor via het knooppunt van de IoT Hub-sink verzonden naar de IoT-hub. De berichten die u ziet in het UITVOER-venster van Visual Studio Code bevatten een sectie 'body' en een sectie 'applicationProperties'. Lees [dit](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct) artikel als u wilt weten wat deze secties inhouden.

In de onderstaande berichten worden de eigenschappen van de toepassing en de inhoud van de hoofdtekst door de module Live Video Analytics bepaald.

## <a name="mediasession-established-event"></a>MediaSession Established-gebeurtenis

Wanneer een mediagraaf wordt geïnstantieerd, probeert het RTSP-bronknooppunt verbinding te maken met de RTSP-server die wordt uitgevoerd op de rtspsim-live555-container. Als dit lukt, wordt deze gebeurtenis afgedrukt:

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:  
{  
"body": {
"sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"  
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

* Het bericht is een diagnostische gebeurtenis, MediaSessionEstablished, die aangeeft dat het RTSP-bronknooppunt (het subject) verbinding kan maken met de RTSP-simulator en een (gesimuleerde) live-feed kan gaan ontvangen.
* 'subject' in applicationProperties verwijst naar het knooppunt in de graaftopologie van waaruit het bericht is gegenereerd. In dit geval is het bericht afkomstig van het RTSP-bronknooppunt.
* 'eventType' in applicationProperties geeft aan dat dit een diagnostische gebeurtenis is.
* 'eventTime' geeft de tijd aan waarop de gebeurtenis heeft plaatsgevonden.
* 'body' bevat gegevens over de diagnostische gebeurtenis, in dit geval de [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol)-details.


## <a name="motion-detection-event"></a>Bewegingsdetectiegebeurtenis

Wanneer er beweging wordt gedetecteerd, verzendt de module Live Video Analytics Edge een deductiegebeurtenis. Het type wordt ingesteld op 'motion' om aan te geven dat het een resultaat is van de bewegingsdetectieprocessor en eventTime vertelt u op welke tijd (UTC) beweging heeft plaatsgevonden. Hieronder ziet u een voorbeeld:

```
  {  
  "body": {  
    "timestamp": 142843967343090,
    "inferences": [  
      {  
        "type": "motion",  
        "motion": {  
          "box": {  
            "l": 0.573222,  
            "t": 0.492537,  
            "w": 0.141667,  
            "h": 0.074074  
          }  
        }  
      }  
    ]  
  },  
  "applicationProperties": {  
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/GRAPHINSTANCENAME/processors/md",  
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",  
    "eventTime": "2020-04-17T20:26:32.7010000Z",
    "dataVersion": "1.0"  
  }  
}  
```

* 'subject' in applicationProperties verwijst naar het knooppunt in de mediagraaf waaruit het bericht is gegenereerd. In dit geval is het bericht afkomstig van de bewegingsdetectieprocessor.
* 'eventType' in applicationProperties geeft aan dat dit een analytische gebeurtenis is.
* 'eventTime' geeft de tijd aan waarop de gebeurtenis heeft plaatsgevonden.
'body' bevat gegevens over de analytische gebeurtenis. In dit geval is de gebeurtenis een deductiegebeurtenis en daarom bevat de hoofdtekst ('body') de gegevens 'timestamp' en 'inferences'.
* Het gegeven 'inferences' geeft aan dat het 'type' 'motion' is en dat er aanvullende gegevens zijn over de gebeurtenis 'motion'.
* De sectie 'box' bevat de coördinaten voor een begrenzingsvak rond het bewegende object. De waarden worden genormaliseerd door de breedte en hoogte van de video in pixels (bijvoorbeeld een breedte van 1920 en een hoogte van 1080).

    ```
    l - distance from left of image
    t - distance from top of image
    w - width of bounding box
    h - height of bounding box
    ```
    
## <a name="cleanup-resources"></a>Resources opruimen

Als u de andere quickstarts wilt proberen, moet u de gemaakte resources bewaren. Anders gaat u naar de Azure-portal, bladert u naar de resourcegroepen, selecteert u de resourcegroep waaronder u deze quickstart hebt uitgevoerd en verwijdert u alle resources.

## <a name="next-steps"></a>Volgende stappen

Voer de andere quickstarts uit, zoals het detecteren van een object in een live-videofeed.        
