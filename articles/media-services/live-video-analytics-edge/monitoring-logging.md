---
title: Bewaking en logboek registratie-Azure
description: Dit artikel bevat een overzicht van live video analyses op IoT Edge bewaking en logboek registratie.
ms.topic: reference
ms.date: 04/27/2020
ms.openlocfilehash: 807b0623159e0b50285b89da2835e9dd6cb037aa
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261210"
---
# <a name="monitoring-and-logging"></a>Bewaking en registratie

In dit artikel vindt u informatie over hoe u gebeurtenissen kunt ontvangen van de module live video Analytics op IoT Edge voor externe controle. 

U leert ook hoe u de logboeken kunt beheren die door de module worden gegenereerd.

## <a name="taxonomy-of-events"></a>Taxonomie van gebeurtenissen

Live video Analytics op IoT Edge verzendt gebeurtenissen of telemetriegegevens op basis van de volgende taxonomie.

![Live video Analytics op IoT Edge telemetrie-schema](./media/telemetry-schema/taxonomy.png)

* Operationeel: gebeurtenissen die worden gegenereerd als onderdeel van acties die worden uitgevoerd door een gebruiker of tijdens de uitvoering van een [Media grafiek](media-graph-concept.md).
   
   * Volume: er wordt naar verwachting laag (een paar keer per minuut of zelfs lagere frequentie).
   * Voorbeelden:

      Opname is gestart (onder), de opname is gestopt
      
      ```
      {
        "body": {
          "outputType": "assetName",
          "outputLocation": "sampleAssetFromEVR-LVAEdge-20200512T233309Z"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
          "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
          "eventTime": "2020-05-12T23:33:10.392Z",
          "dataVersion": "1.0"
        }
      }
      ```
* Diagnostische gegevens: gebeurtenissen die helpen bij het vaststellen van problemen en/of problemen met prestaties.

   * Volume: kan hoog zijn (enkele keren per minuut).
   * Voorbeelden:
   
      RTSP- [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) -informatie (hieronder) of hiaten in de feed voor binnenkomende video.

      ```
      {
        "body": {
          "sdp": "SDP:\nv=0\r\no=- 1589326384077235 1 IN IP4 XXX.XX.XX.XXX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-73.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sources/rtspSource",
          "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
          "eventTime": "2020-05-12T23:33:04.077Z",
          "dataVersion": "1.0"
        }
      }
      ```
* Analytics: gebeurtenissen die worden gegenereerd als onderdeel van de video analyse.

   * Volume: kan hoog zijn (meermaals een minuut of vaker).
   * Voorbeelden:
      
      Gedetecteerde bewegingen (hieronder), het afleiden van resultaten.
   ```      
   {
     "body": {
       "timestamp": 143039375044290,
       "inferences": [
         {
           "type": "motion",
           "motion": {
             "box": {
               "l": 0.48954,
               "t": 0.140741,
               "w": 0.075,
               "h": 0.058824
             }
           }
         }
       ]
     },
     "applicationProperties": {
       "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
       "subject": "/graphInstances/Sample-Graph-2/processors/md",
       "eventType": "Microsoft.Media.Graph.Analytics.Inference",
       "eventTime": "2020-05-12T23:33:09.381Z",
       "dataVersion": "1.0"
     }
   }
   ```
De gebeurtenissen die door de module worden gegenereerd, worden naar de [IOT Edge-hub](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub)verzonden en van daaruit kan worden doorgestuurd naar andere bestemmingen. 

## <a name="controlling-events"></a>Gebeurtenissen beheren

U kunt de volgende module dubbele eigenschappen, zoals beschreven in [module dubbele JSON-schema](module-twin-configuration-schema.md), gebruiken om de operationele en diagnostische gebeurtenissen te beheren die door de live video Analytics op IOT Edge module worden gepubliceerd.

`diagnosticsEventsOutputName`– Neem een waarde voor deze eigenschap op en geef deze op om diagnostische gebeurtenissen van de module op te halen. Laat deze weg of laat het leeg om te voor komen dat de module diagnostische gebeurtenissen publiceert.
   
`operationalEventsOutputName`– Neem een waarde voor deze eigenschap op en geef deze op om operationele gebeurtenissen van de module op te halen. Laat deze weg of laat het leeg om te voor komen dat de module operationele gebeurtenissen publiceert.
   
De analyse gebeurtenissen worden gegenereerd door knoop punten zoals de bewegings detectie processor of de HTTP-extensie processor, en de IoT hub-sink wordt gebruikt om ze naar de IoT Edge hub te verzenden. 

U kunt de [route ring van alle bovenstaande gebeurtenissen](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) beheren via een gewenste eigenschap van de $edgeHub module (in het implementatie manifest):

```
 "$edgeHub": {
   "properties.desired": {
     "schemaVersion": "1.0",
     "routes": {
       "moduleToHub": "FROM /messages/modules/lvaEdge/outputs/* INTO $upstream"
     },
     "storeAndForwardConfiguration": {
       "timeToLiveSecs": 7200
     }
   }
 }
```

In het bovenstaande is lvaEdge de naam voor de live video Analytics op IoT Edge module en volgt de routerings regel het schema dat is gedefinieerd in de [Declareer routes](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes).

> [!NOTE]
> Om ervoor te zorgen dat Analytics-gebeurtenissen de IoT Edge hub bereiken, moet er een-Sink-knoop punt van de IoT-hub worden aangetroffen in het knoop punt van een wille keurig bewegings detectie processor en/of een HTTP extension-processor knooppunt.

## <a name="event-schema"></a>Gebeurtenisschema

Gebeurtenissen zijn afkomstig van het apparaat aan de rand en kunnen worden gebruikt op de rand of in de Cloud. Gebeurtenissen die worden gegenereerd door live video Analytics op IoT Edge voldoen aan het [streaming-berichten patroon](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct) dat is vastgesteld door Azure IOT hub, met systeem eigenschappen, toepassings eigenschappen en een hoofd tekst.

### <a name="summary"></a>Samenvatting

Bij elke gebeurtenis, wanneer deze via de IoT Hub wordt waargenomen, wordt een aantal algemene eigenschappen weer gegeven, zoals hieronder wordt beschreven.

|Eigenschap   |Eigenschapstype| Gegevenstype   |Beschrijving|
|---|---|---|---|
|bericht-id |systeem |guid|  Unieke gebeurtenis-ID.|
|onderwerp| applicationProperty |tekenreeks|    Azure Resource Manager pad voor het Media Services-account.|
|Onderwerp|   applicationProperty |tekenreeks|    Subpad van de entiteit die de gebeurtenis verstuurt.|
|eventTime| applicationProperty|    tekenreeks| Tijdstip waarop de gebeurtenis is gegenereerd.|
|eventType| applicationProperty |tekenreeks|    Gebeurtenis type-id (zie hieronder).|
|body|body  |object|    Bepaalde gebeurtenis gegevens.|
|dataVersion    |applicationProperty|   tekenreeks  |{Major}. Secundair|

### <a name="properties"></a>Eigenschappen

#### <a name="message-id"></a>bericht-id

Gebeurtenis Globally Unique Identifier (GUID)

#### <a name="topic"></a>onderwerp

Vertegenwoordigt het Azure media service-account dat is gekoppeld aan de grafiek.

`/subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.Media/mediaServices/{accountName}`

#### <a name="subject"></a>Onderwerp

Entiteit die de gebeurtenis verzendt:

`/graphInstances/{graphInstanceName}`<br/>
`/graphInstances/{graphInstanceName}/sources/{sourceName}`<br/>
`/graphInstances/{graphInstanceName}/processors/{processorName}`<br/>
`/graphInstances/{graphInstanceName}/sinks/{sinkName}`

De eigenschap subject kan algemene gebeurtenissen toewijzen aan de bijbehorende genererende module. Als er bijvoorbeeld een ongeldige RTSP-gebruikers naam of wacht woord is opgegeven, zou de gegenereerde gebeurtenis zich `Microsoft.Media.Graph.Diagnostics.ProtocolError` op het knoop punt bevindt `/graphInstances/myGraph/sources/myRtspSource` .

#### <a name="event-types"></a>Gebeurtenistypen

Gebeurtenis typen worden toegewezen aan een naam ruimte op basis van het volgende schema:

`Microsoft.Media.Graph.{EventClass}.{EventType}`

#### <a name="event-classes"></a>Gebeurtenis klassen

|Klassenaam|Beschrijving|
|---|---|
|Analyse  |Gebeurtenissen die worden gegenereerd als onderdeel van inhouds analyse.|
|Diagnostiek    |Gebeurtenissen die hulp bieden bij diagnose van problemen en prestaties.|
|Functioneren    |Gebeurtenissen die worden gegenereerd als onderdeel van de resource bewerking.|

De gebeurtenis typen zijn specifiek voor elke gebeurtenis klasse.

Voorbeelden:

* Micro soft. media. Graph. Analytics. deinterferentie
* Micro soft. media. Graph. Diagnostics. AuthorizationError
* Micro soft. media. Graph. Operational. GraphInstanceStarted

### <a name="event-time"></a>Tijd van gebeurtenis

De tijd van de gebeurtenis wordt beschreven in de ISO8601-teken reeks en het tijdstip waarop de gebeurtenis heeft plaatsgevonden.

## <a name="logging"></a>Logboekregistratie

Net als bij andere IoT Edge modules kunt u ook [de container logboeken](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-container-logs-for-issues) op het apparaat Edge bekijken. De informatie die naar de logboeken wordt geschreven, kan worden beheerd met de [volgende module dubbele](module-twin-configuration-schema.md) eigenschappen:

* logLevel

   * Toegestane waarden zijn uitgebreid, informatie, waarschuwing, fout, geen.
   * De standaard waarde is informatie: de logboeken bevatten fout, waarschuwing en informatie. meldingen.
   * Als u de waarde instelt op waarschuwing, bevatten de logboeken fout-en waarschuwings berichten
   * Als u de waarde instelt op fout, bevatten de Logboeken alleen fout berichten.
   * Als u de waarde instelt op geen, worden er geen logboeken gegenereerd (dit wordt niet aanbevolen).
   * U moet alleen uitgebreider gebruiken als u logboeken wilt delen met Azure-ondersteuning voor het vaststellen van een probleem.
* logCategories

   * Een door komma's gescheiden lijst met een of meer van de volgende items: toepassing, gebeurtenissen, MediaPipeline.
   * Standaard: toepassing, gebeurtenissen.
   * Toepassing: dit is informatie op hoog niveau van de module, zoals opstart berichten van een module, omgevings fouten en directe-methode aanroepen.
   * Gebeurtenissen: Dit zijn alle gebeurtenissen die eerder in dit artikel zijn beschreven.
   * MediaPipeline: Dit zijn enkele logboeken op laag niveau die inzicht kunnen bieden bij het oplossen van problemen, zoals problemen met het maken van een verbinding met een RTSP-compatibele camera.
   
### <a name="generating-debug-logs"></a>Logboeken voor fout opsporing genereren

In bepaalde gevallen moet u mogelijk gedetailleerdere logboeken genereren dan hierboven beschreven, om ondersteuning van Azure te helpen bij het oplossen van een probleem. Er zijn twee stappen om dit te bereiken.

Eerst [koppelt u de module opslag aan de opslag van het apparaat](https://docs.microsoft.com/azure/iot-edge/how-to-access-host-storage-from-module#link-module-storage-to-device-storage) via createOptions. Als u een sjabloon voor de [implementatie manifest](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp/blob/master/src/edge/deployment.template.json) van snel starten onderzoekt, ziet u het volgende:

```
"createOptions": {
   …
   "Binds": [
     "/var/local/mediaservices/:/var/lib/azuremediaservices/"
   ]
 }
```

Hierboven kunt u met de module Edge logboeken schrijven naar het opslagpad (apparaat) '/var/Local/mediaservices/'. Als u de volgende gewenste eigenschap aan de module toevoegt:

`"debugLogsDirectory": "/var/lib/azuremediaservices/debuglogs/",`

Vervolgens schrijft de module Logboeken voor fout opsporing in een binaire indeling naar het opslagpad (apparaat)/var/Local/mediaservices/debuglogs/, dat u met de ondersteuning van Azure kunt delen.

## <a name="faq"></a>Veelgestelde vragen

[Veelgestelde vragen](faq.md#monitoring-and-metrics)

## <a name="next-steps"></a>Volgende stappen

[Continue video-opname](continuous-video-recording-tutorial.md)
