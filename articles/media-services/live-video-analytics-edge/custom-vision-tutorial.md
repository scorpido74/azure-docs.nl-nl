---
title: Live video analyseren met Live Video Analytics op IoT Edge en Azure Custom Vision
description: Meer informatie over het gebruik van Custom Vision om een containermodel te bouwen dat een speelgoedwagen kan detecteren en de AI-uitbreidbaarheid van Live Video Analytics op IoT Edge (LVA) kan gebruiken om het model op de rand te implementeren om speelgoedwagens te detecteren in een live-videostream.
ms.topic: tutorial
ms.date: 09/08/2020
ms.openlocfilehash: 0e980ac73d77b6fbbfdb8178f285904d3bf29920
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90929308"
---
# <a name="tutorial-analyze-live-video-with-live-video-analytics-on-iot-edge-and-azure-custom-vision"></a>Zelfstudie: Live video analyseren met Live Video Analytics op IoT Edge en Azure Custom Vision

In deze zelfstudie leert u [Custom Vision](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) te gebruiken om een containermodel te bouwen dat een speelgoedwagen kan detecteren en de [AI-uitbreidbaarheid](analyze-live-video-concept.md#analyzing-video-using-a-custom-vision-model) van Live Video Analytics op IoT Edge kan gebruiken om het model op de rand te implementeren om speelgoedwagens te detecteren in een live-videostream.

We laten u zien hoe u de kracht van Custom Vision - waarmee iedereen een Computer Vision-model kan bouwen en trainen door simpelweg een paar afbeeldingen te uploaden en te labelen, zonder enige kennis van gegevenswetenschap, ML of AI - combineert met de mogelijkheden van Live Video Analytics om eenvoudig een aangepast model te implementeren als een container op de rand en een gesimuleerde live-videofeed te analyseren. Deze zelfstudie gebruikt een Azure VM als IoT Edge-apparaat, is gebaseerd op de voorbeeldcode die is geschreven in C# en bouwt voort op de quickstart [Beweging detecteren en gebeurtenissen verzenden](detect-motion-emit-events-quickstart.md).

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De relevante resources instellen.
> * Een Custom Vision-model bouwen in de cloud om speelgoedwagens te detecteren en te implementeren op de rand
> * Een mediagrafiek met een http-extensie maken en implementeren in een aangepast Vision-model
> * De voorbeeldcode uitvoeren.
> * De resultaten onderzoeken en interpreteren.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Aanbevolen om te lezen  

We raden u aan om de volgende artikelen te lezen voordat u begint: 

* [Overzicht van Live Video Analytics in IoT Edge](overview.md)
* [Azure Custom Vision-overzicht](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/home)
* [Terminologie van Live Video Analytics in IoT Edge](terminology.md)
* [Mediagrafiekconcepten](media-graph-concept.md)
* [Live Video Analytics zonder video-opname](analyze-live-video-concept.md)
* [Live Video Analytics uitvoeren met uw eigen model](use-your-model-quickstart.md)
* [Zelfstudie: Een IoT Edge-module ontwikkelen](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux)
* [Implementatie .*.template.json bewerken](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)

## <a name="prerequisites"></a>Vereisten

Vereisten voor deze zelfstudie:

* [Visual Studio Code](https://code.visualstudio.com/) op uw ontwikkelcomputer met de extensies [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) en [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).

    > [!TIP]
    > U wordt mogelijk gevraagd om Docker te installeren. U kunt deze prompt negeren.
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) op uw ontwikkelcomputer.
* Zorg ervoor dat u:
    
    * [Azure-resources heeft ingesteld](detect-motion-emit-events-quickstart.md#set-up-azure-resources)
    * [De ontwikkelomgeving instellen](detect-motion-emit-events-quickstart.md#set-up-your-development-environment)

## <a name="review-the-sample-video"></a>De voorbeeldvideo bekijken


In deze zelfstudie wordt gebruikgemaakt van een bestand met een [video van een speelgoedwagen](https://lvamedia.blob.core.windows.net/public/t2.mkv/) om een livestream te simuleren. U kunt de video bekijken via een toepassing zoals [VLC Media Player](https://www.videolan.org/vlc/). Selecteer CTRL + N en plak vervolgens een link naar [de video van een speelgoedwagen](https://lvamedia.blob.core.windows.net/public/t2.mkv) om het afspelen te starten. Merk op dat rond seconde 36 van de video een speelgoedwagen verschijnt. Het aangepaste model is getraind om deze specifieke speelgoedwagen te detecteren. In deze zelfstudie gebruikt u Live Video Analytics op IoT Edge om dergelijke speelgoedwagens te detecteren en bijbehorende deductiegebeurtenissen te publiceren naar IoT Edge Hub.

## <a name="overview"></a>Overzicht

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/topology-custom-vision.svg" alt-text="Custom Vision-overzicht":::

Dit diagram laat zien hoe de signalen in deze zelfstudie stromen. Een [Edge-module](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simuleert een IP-camera die als host fungeert voor een RTSP-server (Real-Time Streaming Protocol). Een [RTSP-bron](media-graph-concept.md#rtsp-source)-knooppunt haalt de videofeed van deze server, en verstuurt videoframes naar het [framefilterprocessor](media-graph-concept.md#frame-rate-filter-processor)-knooppunt. Deze processor beperkt de framesnelheid van de videostream die het knooppunt [HTTP-extensieprocessor](media-graph-concept.md#http-extension-processor) bereikt.
Het knooppunt HTTP-extensie speelt de rol van een proxy. Het converteert videoframes naar het opgegeven afbeeldingstype. Vervolgens wordt de afbeelding via REST doorgestuurd naar een andere Edge-module die een AI-model achter een HTTP-eindpunt uitvoert. In dit voorbeeld is die Edge-module het detectormodel van de speelgoedwagen, gebouwd met behulp van Custom Vision. Het HTTP-extensieprocessor-knooppunt verzamelt de detectieresultaten en publiceert de gebeurtenissen naar het [IoT Hub Sink](media-graph-concept.md#iot-hub-message-sink)-knooppunt. Het knooppunt verzendt die gebeurtenissen vervolgens naar [IoT Edge-hub](https://docs.microsoft.com/azure/iot-edge/iot-edge-glossary#iot-edge-hub).

## <a name="build-and-deploy-a-custom-vision-toy-detection-model"></a>Een Custom Vision-detectiemodel voor speelgoed bouwen en implementeren 

Zoals de naam Custom Vision doet vermoeden, kunt u deze gebruiken om uw eigen aangepaste objectdetector of classificatie te bouwen in de cloud. Het biedt een eenvoudige, gebruiksvriendelijke en intuïtieve interface voor het bouwen van aangepaste Vision-modellen die kunnen worden geïmplementeerd in de cloud of op de rand via containers. 

Als u een detector voor een speelgoedtruck wilt maken, raden we u aan dit [quickstart-artikel](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/get-started-build-detector) 'Een objectdetector bouwen via een webportaal' van Custom Vision te volgen.

Aanvullende opmerkingen:
 
* Gebruik voor deze zelfstudie niet de voorbeeldafbeeldingen uit de [sectie vereisten](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/get-started-build-detector#prerequisites) van het quickstart-artikel. In plaats daarvan hebben we een bepaalde afbeeldingenset gebruikt om een Custom Vision-detectiemodel te bouwen. We raden u aan om [deze afbeeldingen](https://lvamedia.blob.core.windows.net/public/ToyCarTrainingImages.zip) te gebruiken wanneer u gevraagd wordt om [uw trainingsafbeeldingen te kiezen](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/get-started-build-detector#choose-training-images) in de quickstart.
* Zorg er in de sectie afbeelding labelen van de quickstart voor dat u de speelgoedwagen in de foto labelt als 'bestelwagen'.

Als het model klaar is voor gebruik, kunt u het exporteren naar een Docker-container met behulp van de knop Exporteren op het tabblad Prestaties. Zorg ervoor dat u Linux kiest als containerplatformtype. Dit is het platform waarop de container wordt uitgevoerd. De computer waarop u de container downloadt, kan Windows of Linux zijn. De volgende instructies zijn gebaseerd op een containerbestand dat op een Windows-computer gedownload is.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/docker-file.png" alt-text="Dockerfile":::
 
1. U moet een zip-bestand downloaden naar de lokale computer met de naam `<projectname>.DockerFile.Linux.zip`. 
1. Controleer of Docker is geïnstalleerd. Zo niet, installeer dan [Docker](https://docs.docker.com/get-docker/) voor uw Windows-computer.
1. Pak het gedownloade bestand uit op een locatie naar keuze. Gebruik de opdrachtregel om naar de uitgepakte map te gaan.
    
    Voer de volgende opdrachten uit 
    
    1. `docker build -t cvtruck` 
    
        Met deze opdracht worden een aantal pakketten gedownload en wordt de docker-installatiekopie gebouwd en gelabeld als `cvtruck:latest`. 
    
        > [!NOTE]
        > U ziet het volgende als het succesvol is `- Successfully built <docker image id> and Successfully tagged cvtruck:latest.` Als de build-opdracht mislukt, probeer het dan opnieuw. Soms worden afhankelijke pakketten de eerste keer niet gedownload.
    1. `docker  image ls`

        Met deze opdracht wordt gecontroleerd of de nieuwe installatiekopie zich in het lokale register bevindt.
    1. `docker run -p 127.0.0.1:80:80 -d cvtruck`
    
        Deze opdracht publiceert de opengestelde poort (80) van de docker naar de poort (80) van uw lokale machine.
    1. `docker container ls`
    
        Deze opdracht controleert de toewijzing van de poorten, en of de docker-container correct wordt uitgevoerd op uw machine. De uitvoer moet er ongeveer als volgt uitzien:

        ```
        CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                      NAMES
        8b7505398367        cvtruck             "/bin/sh -c 'python …"   13 hours ago        Up 25 seconds       127.0.0.1:80->80/tcp   practical_cohen
        ```
      1. `curl -X POST http://127.0.0.1:80/image -F imageData=@<path to any image file that has the toy delivery truck in it>`
            
            Met deze opdracht wordt de container op de lokale computer getest, en als de afbeelding dezelfde bestelwagen is als die van het model, dan ziet de uitvoer er ongeveer als volgt uit. Dit suggereert dat de bestelwagen met een zekerheidsgraad van 90,12% gedetecteerd werd.
    
            ```
            {"created":"2020-03-20T07:10:47.827673","id":"","iteration":"","predictions":[{"boundingBox":{"height":0.66167289,"left":-0.03923762,"top":0.12781593,"width":0.70003178},"probability":0.90128148,"tagId":0,"tagName":"delivery truck"},{"boundingBox":{"height":0.63733053,"left":0.25220079,"top":0.0876643,"width":0.53331227},"probability":0.59745145,"tagId":0,"tagName":"delivery truck"}],"project":""}
            ```

<!--## Create and deploy a media graph with http extension to custom vision model-->

## <a name="examine-the-sample-files"></a>De voorbeeldbestanden bekijken

1. Ga in VSCode naar "src/edge". U ziet het. env-bestand dat u hebt gemaakt, samen met een paar sjabloonbestanden voor de implementatie.

    De implementatiesjabloon verwijst naar het implementatiemanifest voor het edge-apparaat met enkele tijdelijke waarden. Het. env-bestand bevat de waarden voor die variabelen.
1. Blader vervolgens naar de map 'src/cloud-to-device-console-app'. Hier ziet u het bestand appsettings.json dat u hebt gemaakt, samen met enkele andere bestanden:

    * c2d-console-app.csproj: dit is het projectbestand voor VSCode.
    * operations.json: dit bestand bevat de verschillende bewerkingen die u het programma wilt laten uitvoeren.
    * Program.cs: dit is de voorbeeldcode van het programma. Deze doet het volgende:

        * De app-instellingen laden.
        * De Live video Analytics aanroepen op de directe methodes van de IoT Edge-module om een topologie te maken, de grafiek te instantiëren en de grafiek te activeren.
        * Pauzeren zodat u de uitvoer van de grafiek in het TERMINAL-venster en de gebeurtenissen die naar de IoT-hub zijn verzonden in het OUTPUT-venster kunt onderzoeken.
        * De grafiekinstantie deactiveren, de grafiekinstantie verwijderen en de grafiektopologie verwijderen.
        
## <a name="generate-and-deploy-the-deployment-manifest"></a>Het implementatiemanifest genereren en implementeren

1. Navigeer in VSCode naar  "src/cloud-to-device-console-app/operations.json"

1. Controleer of het volgende onder GraphTopologySet waar is:<br/>`"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/topology.json"`
1. Zorg ervoor dat onder GraphInstanceSet: 
    1. "topologyName" : "InferencingWithHttpExtension"
    1. En voeg het volgende toe bovenaan de matrix met parameters: `{"name": "inferencingUrl","value": "http://cv:80/image"},`
    1. Wijzig de waarde van de parameter rtspUrl naar – "rtsp://rtspsim:554/media/t2.mkv"    
1. Zorg ervoor dat "name" onder GraphTopologyDelete: "InferencingWithHttpExtension"
1. Klik met de rechtermuisknop op het bestand "src/edge/ deployment.customvision.template.json" en klik op **IoT Edge-implementatiemanifest genereren**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-template-json.png" alt-text="IoT Edge-implementatiemanifest genereren":::
  
    Hiermee maakt u een manifestbestand in de map src/edge/config met de naam " deployment.customvision.amd64.json".
1. Open het bestand "src/edge/ deployment.customvision.template.json" en zoek het JSON-blok registryCredentials. In dit blok vindt u het adres van uw Azure-containerregister samen met de gebruikersnaam en het wachtwoord.
1. Push de lokale Custom Vision-container naar uw Azure-containerregister door op de opdrachtregel te volgen.

    1. Meld u aan bij het register met de volgende opdracht:
    
        `docker login <address>`
    
        Voer de gebruikersnaam en het wachtwoord in als u om verificatie gevraagd wordt. 
        
        > [!NOTE]
        > Het wachtwoord is niet zichtbaar in de opdrachtregel.
    1. Label uw afbeelding met:<br/>`docker tag cvtruck   <address>/cvtruck`
    1. Push uw afbeelding met:<br/>`docker push <address>/cvtruck`

        Als het goed is, wordt 'Pushed' in de opdrachtregel weergegeven samen met de SHA voor de installatie kopie. 
    1. U kunt ook bevestigen door uw Azure-containerregister te controleren in het Azure-portaal. Hier ziet u de naam van de opslagplaats samen met het label. 
1. Stel de IoT Hub-verbindingsreeks in door naast het deelvenster AZURE IOT HUB in de linkerbenedenhoek te klikken op het pictogram "Meer acties". U kunt de tekenreeks uit het bestand appsettings.json kopiëren. (Hier volgt een andere aanbevolen methode om ervoor te zorgen dat u de juiste IoT Hub hebt geconfigureerd in VScode via de opdracht [IOT hub selecteren](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub)).

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/connection-string.png" alt-text="Verbindingsreeks":::
1. Klik vervolgens met de rechtermuisknop op "src/edge/config/ deployment.customvision.amd64.json" en klik op **Implementatie voor één apparaat maken**. 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-amd64-json.png" alt-text="Implementatie voor één apparaat maken":::
1. Vervolgens wordt u gevraagd om een IoT Hub-apparaat te selecteren. Selecteer lva-sample-device in de vervolgkeuzelijst.
1. Vernieuw in ongeveer 30 seconden de Azure IOT-hub in het gedeelte linksonder en u moet het edge-apparaat hebben met de volgende modules geïmplementeerd:

    * De module Live Video Analytics in IoT Edge, genaamd "lvaEdge".
    * Een module met de naam `rtspsim`, die een RTSP-server simuleert en fungeert als de bron van een live-videofeed.
    * Een module met de naam `cv` die, zoals de naam doet vermoeden, het Custom Vision-detectiemodel voor de speelgoedwagen is dat Custom Vision toepast op de afbeeldingen en meerdere labeltypes retourneert. (Ons model is op slechts één label getraind: "bestelwagen").

## <a name="prepare-for-monitoring-events"></a>Bewakingsgebeurtenissen voorbereiden

Klik met de rechtermuisknop op het Live Video Analytics-apparaat en selecteer **Bewaking van ingebouwd gebeurteniseindpunt starten**. Deze stap is nodig om de IoT Hub-gebeurtenissen te controleren in het venster UITVOER van Visual Studio Code.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/start-monitoring.png" alt-text="Bewaking van ingebouwd gebeurteniseindpunt starten":::

## <a name="run-the-sample-program"></a>Het voorbeeldprogramma uitvoeren

Als u de grafiektopologie voor deze zelfstudie in een browser opent, ziet u dat de waarde van InferencingUrl is ingesteld op http://cv:80/image, wat betekent dat de deductieserver resultaten in de live video weergeeft na het detecteren van speelgoedwagens, indien aanwezig.

1. Selecteer de toets F5 om een foutopsporingssessie te starten. U ziet de berichten die worden afgedrukt in het venster TERMINAL.
1. De code operations.json begint met het uitvoeren van aanroepen naar de directe methoden GraphTopologyList en GraphInstanceList. Als u na het voltooien van vorige quickstarts resources hebt opgeschoond, worden er lege lijsten geretourneerd en wordt het proces gepauzeerd. Selecteer de Enter-toets om door te gaan.
    
   In het TERMINAL-venster wordt de volgende set aanroepen van directe methoden weergegeven:
    
   * Een aanroep van GraphTopologySet waarin gebruik wordt gemaakt van de voorgaande topologyUrl.
   * Een aanroep van GraphInstanceSet met behulp van de volgende hoofdtekst:
        
   ```
        {
          "@apiVersion": "1.0",
          "name": "Sample-Graph-1",
          "properties": {
            "topologyName": "CustomVisionWithHttpExtension",
            "description": "Sample graph description",
            "parameters": [
              { 
                "name": "inferencingUrl",
                "value": "http://cv:80/image"
              },
              {
                "name": "rtspUrl",
                "value": "rtsp://rtspsim:554/media/t2.mkv"
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
    
   * Een aanroep van GraphInstanceActivate om het grafiekexemplaar en de videostream te starten.
   * Een tweede aanroep van GraphInstanceList om aan te geven dat het graafexemplaar actief is.
    
1. De uitvoer in het TERMINAL-venster wordt onderbroken met de prompt ‘Druk op Enter om door te gaan’. Selecteer Enter nog niet. Schuif omhoog om de nettoladingen voor het JSON-antwoord te zien voor de directe methoden die u hebt aangeroepen.
1. Schakel naar het UITVOER-venster in Visual Studio Code. Er zijn berichten te zien die door de module Live Video Analytics in IoT Edge worden verzonden naar de IoT-hub. In de volgende sectie van deze zelfstudie worden deze berichten besproken.
1. Het uitvoeren van de mediagraaf wordt vervolgd en de resultaten worden afgedrukt. Via de RTSP-simulator wordt de bronvideo continu herhaald. Als u de mediagraaf wilt stoppen, keert u terug naar het TERMINAL-venster en selecteert u Enter.
Met de volgende reeks aanroepen worden resources opgeschoond:
    
   * Een aanroep van GraphInstanceDeactivate deactiveert het graafexemplaar.
   * Een aanroep van GraphInstanceDelete verwijdert het exemplaar.
   * Een aanroep van GraphTopologyDelete verwijdert de topologie.
   * Een laatste aanroep van GraphTopologyList laat zien dat de lijst leeg is.
    
## <a name="interpret-the-results"></a>De resultaten interpreteren

Wanneer u de mediagraaf uitvoert, worden de resultaten van het HTTP-extensieprocessor-knooppunt via het IoT Hub-sink-knooppunt naar de IoT-hub doorgevoerd. De berichten die u ziet in het UITVOER-venster bevatten een sectie body en een sectie applicationProperties. Zie [IoT Hub-berichten maken en lezen](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct) voor meer informatie.

In de volgende berichten worden de eigenschappen van de toepassing en de inhoud van de hoofdtekst bepaald door de module Live Video Analytics.

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished-gebeurtenis

Wanneer een mediagraaf wordt geïnstantieerd, probeert het RTSP-bronknooppunt verbinding te maken met de RTSP-server die wordt uitgevoerd in de rtspsim-live555-container. Als het lukt om de verbinding tot stand te brengen, wordt de volgende gebeurtenis afgedrukt. Het gebeurtenistype is Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished.

```
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1599412849822466 1 IN IP4 172.18.0.3\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/t2.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-78.357\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/t2.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=42C01F;sprop-parameter-sets=Z0LAH9kAUAW6EAAAAwAQAAADAwDxgySA,aMuBcsg=\r\na=control:track1\r\nm=audio 0 RTP/AVP 97\r\nc=IN IP4 0.0.0.0\r\nb=AS:96\r\na=rtpmap:97 MPEG4-GENERIC/44100/2\r\na=fmtp:97 streamtype=5;profile-level-id=1;mode=AAC-hbr;sizelength=13;indexlength=3;indexdeltalength=3;config=121056E500\r\na=control:track2\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/lvaavi_0827/providers/microsoft.media/mediaservices/lvasampleulf2rv2x5msy2",
    "subject": "/graphInstances/ GRAPHINSTANCENAMEHERE /sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-09-06T17:20:49.823Z",
    "dataVersion": "1.0"
  }
```

In dit bericht ziet u deze gegevens:

* Het bericht is een diagnostische gebeurtenis. MediaSessionEstablished geeft aan dat het RTSP-bronknooppunt (het onderwerp) is verbonden met de RTSP-simulator en is begonnen met het ontvangen van een (gesimuleerde) livefeed.
* In applicationProperties geeft 'subject' aan dat het bericht is gegenereerd op basis van het knooppunt van de RTSP-bron in de mediagraaf.
* In applicationProperties geeft 'eventType' aan dat dit een diagnostische gebeurtenis is.
* De eventTime geeft de tijd aan waarop de gebeurtenis heeft plaatsgevonden.
* De hoofdtekst bevat gegevens over de diagnostische gebeurtenis. In dit geval bevatten de gegevens de details van [Session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol).

### <a name="inference-event"></a>Deductiegebeurtenis

Het knooppunt van de HTTP-extensieprocessor ontvangt deductieresultaten van de Custom Vision-container en verzendt de resultaten via het IoT Hub Sink-knooppunt als deductiegebeurtenissen.

```
{
  "body": {
    "created": "2020-05-18T01:08:34.483Z",
    "id": "",
    "iteration": "",
    "predictions": [
      {
        "boundingBox": {
          "height": 0.06219418,
          "left": 0.14977954,
          "top": 0.65847444,
          "width": 0.01879117
        },
        "probability": 0.69806677,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.1148454,
          "left": 0.77430711,
          "top": 0.54488315,
          "width": 0.11315252
        },
        "probability": 0.29394844,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.03187029,
          "left": 0.62644471,
          "top": 0.59640052,
          "width": 0.01582896
        },
        "probability": 0.14435098,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.11421723,
          "left": 0.72737214,
          "top": 0.55907888,
          "width": 0.12627538
        },
        "probability": 0.1141128,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.04717135,
          "left": 0.66516746,
          "top": 0.34617995,
          "width": 0.03802613
        },
        "probability": 0.10461298,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.20650843,
          "left": 0.56349564,
          "top": 0.51482571,
          "width": 0.35045707
        },
        "probability": 0.10056595,
        "tagId": 0,
        "tagName": "delivery truck"
      }
    ],
    "project": ""
  },
  "applicationProperties": {
    "topic": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/lsravi/providers/microsoft.media/mediaservices/lvasamplerc3he6a7uhire",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/httpExtension",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-05-18T01:08:34.038Z"
  }
}
```

Let op het volgende in het bovenstaande bericht:

* Het subject in applicationProperties verwijst naar het knooppunt in MediaGraph van waaruit het bericht is gegenereerd. In dit geval is het bericht afkomstig van de HTTP-extensieprocessor.
* Het eventType in applicationProperties geeft aan dat dit een analytische deductiegebeurtenis is.
* De eventTime geeft de tijd aan waarop de gebeurtenis heeft plaatsgevonden.
* 'body' bevat gegevens over de analytische gebeurtenis. In dit geval is de gebeurtenis een deductiegebeurtenis en daarom bevat de hoofdtekst een matrix met deducties, genaamd "voorspellingen".
* De sectie "voorspellingen" bevat een lijst met voorspellingen over waar de speelgoedbestelwagen (label=bestelwagen) zich in het frame bevindt. Zoals u misschien nog weet, is bestelwagen het aangepaste label dat u heeft gegevens aan het getrainde model voor de speelgoedwagen. Het model deduceert en identificeert de speelgoedwagen in de invoervideo met verschillende betrouwbaarheidsscores.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de andere zelfstudies of quickstarts wilt proberen, moet u de gemaakte resources bewaren. Anders gaat u naar de Azure-portal, bladert u naar de resourcegroepen, selecteert u de resourcegroep waaronder u deze zelfstudie hebt uitgevoerd en verwijdert u alle resources.

## <a name="next-steps"></a>Volgende stappen

Bekijk extra uitdagingen voor gevorderde gebruikers:

* Gebruik een [IP-camera](https://en.wikipedia.org/wiki/IP_camera) met ondersteuning voor RTSP in plaats van de RTSP-simulator. U kunt zoeken naar IP-camera's die RTSP ondersteunen op de pagina met [ONVIF-compatibele](https://www.onvif.org/conformant-products/) producten. Zoek naar apparaten die voldoen aan de profielen G, S of T.
* Gebruik een AMD64-of x64-Linux-apparaat (in plaats van een Azure Linux-VM). Dit apparaat moet zich in hetzelfde netwerk als de IP-camera bevinden. Volg de instructies in [Azure IoT Edge-runtime installeren op Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux). 

Registreer vervolgens het apparaat met IoT Hub door de instructies in [Uw eerste IoT Edge-module implementeren op een virtueel Linux-apparaat](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) te volgen.

