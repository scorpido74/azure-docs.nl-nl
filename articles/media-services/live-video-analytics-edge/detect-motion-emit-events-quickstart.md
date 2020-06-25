---
title: Beweging detecteren en gebeurtenissen verzenden - Azure
description: In deze quickstart leert u hoe u Live Video Analytics in IoT Edge kunt gebruiken om beweging te detecteren en gebeurtenissen kunt verzenden door directe methoden programmatisch aan te roepen.
ms.topic: quickstart
ms.date: 05/29/2020
ms.openlocfilehash: 69486515125c624b3ef5d44aba6e6d8f7694a3cc
ms.sourcegitcommit: 1383842d1ea4044e1e90bd3ca8a7dc9f1b439a54
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/16/2020
ms.locfileid: "84816711"
---
# <a name="quickstart-detect-motion-and-emit-events"></a>Quickstart: Beweging detecteren en gebeurtenissen verzenden

Deze quickstart begeleidt u door de stappen om aan de slag te gaan met Live Video Analytics in IoT Edge. Er wordt gebruikgemaakt van een Azure-VM als een IoT Edge-apparaat en van een gesimuleerde live-videostream. Nadat u de installatiestappen hebt voltooid, kunt u een gesimuleerde livevideostream uitvoeren via een mediagraaf die beweging in die stream detecteert en rapporteert. In het volgende diagram ziet u een grafische weergave van die mediagraaf.

![Live Video Analytics op basis van bewegingsdetectie](./media/analyze-live-video/motion-detection.png) 

Dit artikel is gebaseerd op [voorbeeldcode](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp) geschreven in C#.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) als u nog geen account hebt.
* [Visual Studio Code](https://code.visualstudio.com/) met de volgende extensies:
    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1). 

> [!TIP]
> U wordt mogelijk gevraagd om Docker te installeren terwijl u de Azure IoT Tools-extensie installeert. U kunt de vraag negeren.

## <a name="set-up-azure-resources"></a>Azure-resources instellen

Voor deze zelfstudie hebt u de volgende Azure-resources nodig:

* IoT Hub
* Storage-account
* Azure Media Services-account
* Linux-VM in Azure, met [IoT Edge-runtime](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) geïnstalleerd

Voor deze quickstart wordt u aangeraden gebruik te maken van het [installatiescript voor Live Video Analytics-resources](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) om de vereiste Azure-resources in uw Azure-abonnement te implementeren. Voer hiervoor de volgende stappen uit:

1. Open [Azure Cloud Shell](https://shell.azure.com).
1. Als u Cloud Shell voor het eerst gebruikt, wordt u gevraagd een abonnement te selecteren voor het maken van een opslagaccount en een Microsoft Azure-bestandsshare. Selecteer **Opslag maken** om een opslagaccount te maken voor de gegevens van uw Cloud Shell-sessie. Dit opslagaccount is gescheiden van het account dat door het script wordt gemaakt voor gebruik bij uw Azure Media Services-account.
1. Selecteer in de vervolgkeuzelijst aan de linkerkant van het Cloud Shell-venster **Bash** als uw omgeving.

    ![Omgevingsselector](./media/quickstarts/env-selector.png)

1. Voer de volgende opdracht uit.

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```

    Als het script is voltooid, ziet u alle vereiste resources in uw abonnement.

1. Als het script is voltooid, selecteert u de accolades om de mapstructuur zichtbaar te maken. Er bevinden zich enkele bestanden in de map *~/clouddrive/lva-sample*. Van belang voor deze quickstart zijn:

     * ***~/clouddrive/lva-sample/edge-deployment/.env***: dit bestand bevat eigenschappen die Visual Studio Code gebruikt om modules te implementeren op een edge-apparaat.
     * ***~/clouddrive/lva-sample/appsetting.json***: dit bestand wordt gebruikt door Visual Studio Code voor het uitvoeren van de voorbeeldcode.
     
U hebt deze bestanden nodig wanneer u uw ontwikkelomgeving in Visual Studio Code instelt in de volgende sectie. U kunt deze voorlopig in een lokaal bestand kopiëren.

 ![App-instellingen](./media/quickstarts/clouddrive.png)

## <a name="set-up-your-development-environment"></a>De ontwikkelomgeving instellen

1. Kloon de opslagplaats vanuit deze locatie: https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp.
1. Open in Visual Studio Code de map waarin de opslagplaats is gedownload.
1. Ga in Visual Studio Code naar de map *src/cloud-to-device-console-app*. Maak daar een bestand en geef het de naam *appsettings.json*. Dit bestand bevat de instellingen die nodig zijn om het programma uit te voeren.
1. Kopieer de inhoud van het bestand *~/clouddrive/lva-sample/appsettings.json* dat u eerder in deze quickstart hebt gegenereerd.

    De tekst moet lijken op de volgende uitvoer.

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
1. Ga naar de map *src/edge* en maak een bestand met de naam *.env*.
1. Kopieer de inhoud van het bestand */clouddrive/lva-sample/edge-deployment/.env*. De tekst moet lijken op de volgende code.

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

1. Ga in Visual Studio Code naar *src/edge*. U ziet het bestand *.env* en enkele implementatiesjabloonbestanden.

    De implementatiesjabloon verwijst naar het implementatiemanifest voor het edge-apparaat, waarbij variabelen zijn gebruikt voor bepaalde eigenschappen. Het bestand *.env* bevat de waarden voor die variabelen.
1. Ga naar de map *src/cloud-to-device-console-app*. Hier ziet u het bestand *appsettings.json* en enkele andere bestanden:

    * ***c2d-console-app.csproj***: het projectbestand voor Visual Studio Code.
    * ***operations.json***: een lijst met de bewerkingen die u het programma wilt laten uitvoeren.
    * ***Program.cs***: de voorbeeldcode van het programma. Deze code:
    
      * De app-instellingen laden.
      * Roept directe methoden aan die worden weergegeven door de module Live Video Analytics in IoT Edge. U kunt de module gebruiken om livevideostreams te analyseren door de bijbehorende [directe methoden](direct-methods.md) aan te roepen.
      * Pauzeert, zodat u de uitvoer van het programma kunt controleren in het **TERMINAL**-venster en de gebeurtenissen die zijn gegenereerd door de module kunt controleren in het **UITVOER**-venster.
      * Roept directe methoden aan voor het opschonen van resources.   

## <a name="generate-and-deploy-the-deployment-manifest"></a>Het implementatiemanifest genereren en implementeren

Het implementatiemanifest geeft aan welke modules op een edge-apparaat worden geïmplementeerd. Ook bevat het de configuratie-instellingen voor deze modules. 

Volg deze stappen om het manifest te genereren op basis van het sjabloonbestand en het vervolgens te implementeren op het edge-apparaat.

1. Open Visual Studio Code.
1. Selecteer naast het deelvenster **AZURE IOT HUB** het pictogram **Meer acties** om de IoT Hub-verbindingsreeks in te stellen. U kunt de tekenreeks kopiëren uit het bestand *src/cloud-to-device-console-app/appsettings.json*. 

    ![IoT Hub-verbindingsreeks instellen](./media/quickstarts/set-iotconnection-string.png)

1. Klik met de rechtermuisknop op **src/edge/deployment.template.json** en selecteer **IoT Edge-implementatiemanifest genereren**.

    ![Het IoT Edge-implementatiemanifest genereren](./media/quickstarts/generate-iot-edge-deployment-manifest.png)

    Met deze actie maakt u een manifestbestand met de naam *deployment.amd64.json* in de map *src/edge/config*.
1. Klik met de rechtermuisknop op **src/edge/config/deployment.amd64.json**, selecteer **Implementatie voor één apparaat maken** en selecteer vervolgens de naam van uw edge-apparaat.

    ![Implementatie voor één apparaat maken](./media/quickstarts/create-deployment-single-device.png)

1. Wanneer u wordt gevraagd om een IoT Hub-apparaat te selecteren, kiest u **lva-sample-device** in de vervolgkeuzelijst.
1. Vernieuw Azure IoT Hub na ongeveer 30 seconden in de linkerbenedenhoek van het venster. Op het edge-apparaat worden nu de volgende geïmplementeerde modules weergegeven:

    * Live Video Analytics in IoT Edge (modulenaam `lvaEdge`)
    * RTSP-simulator (Real-Time Streaming Protocol) (modulenaam `rtspsim`)

In de RTSP-simulatormodule wordt een livevideostream gesimuleerd met behulp van een videobestand dat is gekopieerd naar uw edge-apparaat toen u het [installatiescript voor Live Video Analytics-resources](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) uitvoerde. 

In deze fase zijn de modules geïmplementeerd, maar zijn er geen mediagrafen actief.

## <a name="prepare-to-monitor-events"></a>Het bewaken van gebeurtenissen voorbereiden

U gaat de module Live Video Analytics in IoT Edge gebruiken om beweging in de inkomende livevideostream te detecteren en gebeurtenissen naar IoT Hub te verzenden. Volg deze stappen om deze gebeurtenissen te bekijken:

1. Open het deelvenster Explorer in Visual Studio Code en zoek Azure IoT Hub in de linkerbenedenhoek.
1. Vouw het knooppunt **Apparaten** uit.
1. Klik met de rechtermuisknop op **Iva-sample-device** en selecteer **Bewaking van ingebouwd gebeurteniseindpunt starten**.

    ![Bewaking van ingebouwd gebeurteniseindpunt starten](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-sample-program"></a>Het voorbeeldprogramma uitvoeren

Volg deze stappen om de voorbeeldcode uit te voeren:

1. Ga in Visual Studio Code naar *src/cloud-to-device-console-app/operations.json*.
1. Controleer op het knooppunt **GraphTopologySet** of u de volgende waarde ziet:

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/motion-detection/topology.json"`
1. Controleer of op de knooppunten **GraphInstanceSet** en **GraphTopologyDelete** de waarde van `topologyName` overeenkomt met de waarde van de eigenschap `name` in de graaftopologie:

    `"topologyName" : "MotionDetection"`
    
1. Start een foutopsporingssessie door de toets F5 te selecteren. In het **TERMINAL**-venster worden enkele berichten weergegeven.
1. Het bestand *operations.json-* wordt gestart met aanroepen naar `GraphTopologyList` en `GraphInstanceList`. Als u na het voltooien van vorige quickstarts resources hebt opgeschoond, worden er lege lijsten geretourneerd en wordt het proces gepauzeerd. Selecteer de Enter-toets om door te gaan.

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
     
     * Een aanroep van `GraphInstanceActivate` waarmee het graafexemplaar en de videostroom worden gestart
     * Een tweede aanroep van `GraphInstanceList` waarmee wordt weergegeven dat het graafexemplaar wordt uitgevoerd
1. De uitvoer in het **TERMINAL**-venster wordt gepauzeerd bij `Press Enter to continue`. Selecteer Enter nog niet. Schuif omhoog om de nettoladingen voor het JSON-antwoord te zien voor de directe methoden die u hebt aangeroepen.
1. Schakel naar het **UITVOER**-venster in Visual Studio Code. Er zijn berichten te zien die door de module Live Video Analytics in IoT Edge worden verzonden naar de IoT-hub. In de volgende sectie van deze quickstart worden deze berichten besproken.
1. Het uitvoeren van de mediagraaf wordt vervolgd en de resultaten worden afgedrukt. Via de RTSP-simulator wordt de bronvideo continu herhaald. Als u de mediagraaf wilt stoppen, keert u terug naar het **TERMINAL**-venster en selecteert u Enter. 

    Met de volgende reeks aanroepen worden resources opgeschoond:
     * Met een aanroep van `GraphInstanceDeactivate` wordt het graafexemplaar gedeactiveerd.
     * Met een aanroep van `GraphInstanceDelete` wordt het exemplaar verwijderd.
     * Met een aanroep van `GraphTopologyDelete` wordt de topologie verwijderd.
     * Met een aanroep van `GraphTopologyList` wordt ten slotte aangegeven dat de lijst leeg is.

## <a name="interpret-results"></a>Resultaten interpreteren

Wanneer u de mediagraaf uitvoert, gaan de resultaten van het knooppunt van de bewegingsdetectorprocessor via het knooppunt van de IoT Hub-sink naar de IoT-hub. De berichten die u ziet in het **UITVOER**-venster van Visual Studio Code bevatten een sectie `body` en een sectie `applicationProperties`. Zie [IoT Hub-berichten maken en lezen](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct) voor meer informatie.

In de volgende berichten worden de eigenschappen van de toepassing en de inhoud van de hoofdtekst bepaald door de module Live Video Analytics.

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished-gebeurtenis

Wanneer een mediagraaf wordt geïnstantieerd, probeert het RTSP-bronknooppunt verbinding te maken met de RTSP-server die wordt uitgevoerd in de rtspsim-live555-container. Als het lukt om de verbinding tot stand te brengen, wordt de volgende gebeurtenis afgedrukt.

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

In de voorgaande uitvoer geldt het volgende: 
* Het bericht is een diagnostische gebeurtenis, `MediaSessionEstablished`. Het geeft aan dat het RTSP-bronknooppunt (het onderwerp) is verbonden met de RTSP-simulator en is begonnen met het ontvangen van een (gesimuleerde) livefeed.
* In `applicationProperties` verwijst `subject` naar het knooppunt in de graaftopologie vanwaaruit het bericht is gegenereerd. In dit geval is het bericht afkomstig van het RTSP-bronknooppunt.
* In `applicationProperties` geeft `eventType` aan dat deze gebeurtenis een diagnostische gebeurtenis is.
* `eventTime` geeft het tijdstip aan waarop de gebeurtenis heeft plaatsgevonden.
* De `body`-sectie bevat gegevens over de diagnostische gebeurtenis. In dit geval bevatten de gegevens de details van [Session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol).


### <a name="motiondetection-event"></a>MotionDetection-gebeurtenis

Wanneer er beweging wordt gedetecteerd, verzendt de module Live Video Analytics in IoT Edge een deductiegebeurtenis. Het `type` is ingesteld op `motion` om aan te geven dat het om een resultaat van de bewegingsdetectieprocessor gaat. De waarde `eventTime` geeft aan wanneer de beweging heeft plaatsgevonden (in UTC). 

Hier volgt een voorbeeld van dit bericht:

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

In dit voorbeeld geldt het volgende: 

* In `applicationProperties` verwijst `subject` naar het knooppunt in de mediagraaf vanwaaruit het bericht is gegenereerd. In dit geval is het bericht afkomstig van de bewegingsdetectieprocessor.
* In `applicationProperties` geeft `eventType` aan dat deze gebeurtenis een analytische gebeurtenis is.
* De waarde `eventTime` is het tijdstip waarop de gebeurtenis heeft plaatsgevonden.
* De waarde `body` bevat gegevens over de analytische gebeurtenis. In dit geval is de gebeurtenis een deductiegebeurtenis en bevat de hoofdtekst daarom `timestamp`- en `inferences`-gegevens.
* De `inferences`-gegevens geven aan dat het `type` `motion` is. Deze bevatten aanvullende gegevens over die `motion`-gebeurtenis.
* De sectie `box` bevat de coördinaten voor een begrenzingsvak rond het bewegende object. De waarden worden genormaliseerd door de breedte en hoogte van de video in pixels. De breedte is bijvoorbeeld 1920 en de hoogte 1080.

    ```
    l - distance from left of image
    t - distance from top of image
    w - width of bounding box
    h - height of bounding box
    ```
    
## <a name="clean-up-resources"></a>Resources opschonen

Als u de andere quickstarts wilt proberen, moet u de resources die u hebt gemaakt, bewaren. Anders gaat u in Azure Portal naar de resourcegroepen, selecteert u de resourcegroep waar u deze quickstart hebt uitgevoerd en verwijdert u vervolgens alle resources.

## <a name="next-steps"></a>Volgende stappen

Voer de andere quickstarts uit, zoals het detecteren van een object in een livevideofeed.        
