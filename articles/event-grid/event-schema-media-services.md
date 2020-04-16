---
title: Azure Media Services als gebeurtenisrasterbron
description: Beschrijft de eigenschappen die worden geleverd voor Media Services-gebeurtenissen met Azure Event Grid
services: media-services
documentationcenter: ''
author: spelluru
editor: ''
ms.service: event-grid
ms.workload: ''
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: spelluru
ms.openlocfilehash: d5d50bbde927efd4aee0cedd69486a52ab8c328b
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394330"
---
# <a name="azure-media-services-as-an-event-grid-source"></a>Azure Media Services als gebeurtenisrasterbron

In dit artikel vindt u de schema's en eigenschappen van gebeurtenissen in Media Services.

## <a name="job-related-event-types"></a>Functiegerelateerde gebeurtenistypen

Media Services zendt de onderstaande **functiegerelateerde** gebeurtenistypen uit. Er zijn twee categorieën voor de **taakgerelateerde** gebeurtenissen: 'Wijzigingen in taakstatussen controleren' en 'Wijzigingen in de functiestatus controleren'. 

U zich inschrijven voor alle evenementen door u te abonneren op het JobStateChange evenement. U zich ook abonneren op specifieke gebeurtenissen (bijvoorbeeld definitieve statussen zoals JobErrored, JobFinished en JobCanceled).   

### <a name="monitoring-job-state-changes"></a>Wijzigingen in taakstatus controleren

| Gebeurtenistype | Beschrijving |
| ---------- | ----------- |
| Microsoft.Media.JobStateChange| Ontvang een evenement voor alle wijzigingen in de functiestatus. |
| Microsoft.Media.JobGepland| Ontvang een gebeurtenis wanneer taak overgaat naar de geplande status. |
| Microsoft.Media.JobProcessing| Ontvang een gebeurtenis wanneer taak overgaat naar verwerkingsstatus. |
| Microsoft.Media.JobAnnuleren| Ontvang een gebeurtenis wanneer taak overschakelt naar de status annuleren. |
| Microsoft.Media.JobFinished| Ontvang een gebeurtenis wanneer taak wordt overgezet naar de status voltooid. Dit is een laatste status die taakuitvoer bevat.|
| Microsoft.Media.JobGeannuleerd| Ontvang een gebeurtenis wanneer taak overgaat naar geannuleerde status. Dit is een laatste status die taakuitvoer bevat.|
| Microsoft.Media.JobErrored| Ontvang een gebeurtenis wanneer taak overgaat in de foutstatus. Dit is een laatste status die taakuitvoer bevat.|

Zie [Schema voorbeelden](#event-schema-examples) die volgen.

### <a name="monitoring-job-output-state-changes"></a>Wijzigingen in de uitvoerstatus van de taak controleren

Een taak kan meerdere taakuitvoerbevatten (als u de transformatie hebt geconfigureerd om meerdere taakuitvoertehebben.) Als u de details van de afzonderlijke taakuitvoer wilt bijhouden, luistert u naar een gebeurtenis voor het wijzigen van de taakuitvoer.

Elke **taak** zal op een hoger niveau dan **JobOutput,** dus job output gebeurtenissen worden ontslagen binnenkant van een overeenkomstige baan. 

De foutberichten `JobFinished`in `JobCanceled` `JobError` , , output van de geaggregeerde resultaten voor elke taak uitvoer - wanneer ze allemaal klaar zijn. Terwijl, de taak output gebeurtenissen brand als elke taak eindigt. Als u bijvoorbeeld een coderingsuitvoer hebt, gevolgd door een Video Analytics-uitvoer, worden twee gebeurtenissen geactiveerd als taakuitvoergebeurtenissen voordat de laatste Gebeurtenis JobFinished wordt geactiveerd met de samengevoegde gegevens.

| Gebeurtenistype | Beschrijving |
| ---------- | ----------- |
| Microsoft.Media.JobOutputStateChange| Ontvang een gebeurtenis voor alle wijzigingen in de taakuitvoerstatus. |
| Microsoft.Media.JobOutputGepland| Ontvang een gebeurtenis wanneer taakuitvoer overgaat naar de geplande status. |
| Microsoft.Media.JobOutputProcessing| Ontvang een gebeurtenis wanneer taakuitvoer overgaat naar verwerkingsstatus. |
| Microsoft.Media.JobOutputAnnuleren| Ontvang een gebeurtenis wanneer de taakuitvoer overgaat op de status annuleren.|
| Microsoft.Media.JobOutputFinished| Ontvang een gebeurtenis wanneer de taakuitvoer overgaat naar de status voltooid.|
| Microsoft.Media.JobOutputgeannuleerd| Ontvang een gebeurtenis wanneer de taakuitvoer overgaat naar de geannuleerde status.|
| Microsoft.Media.JobOutputErrored| Ontvang een gebeurtenis wanneer taakuitvoer overgaat naar foutstatus.|

Zie [Schema voorbeelden](#event-schema-examples) die volgen.

### <a name="monitoring-job-output-progress"></a>Voortgang van de taakuitvoer controleren

| Gebeurtenistype | Beschrijving |
| ---------- | ----------- |
| Microsoft.Media.JobOutputProgress| Deze gebeurtenis weerspiegelt de voortgang van de taakverwerking, van 0% tot 100%. De service probeert een gebeurtenis te verzenden als de voortgangswaarde met 5% of meer is verhoogd of als de laatste gebeurtenis (heartbeat) meer dan 30 seconden is gestegen. De voortgangswaarde begint gegarandeerd niet bij 0%, of 100%, noch zal deze in de loop van de tijd met een constant tempo stijgen. Deze gebeurtenis mag niet worden gebruikt om te bepalen of de verwerking is voltooid , u moet in plaats daarvan de statuswijzigingsgebeurtenissen gebruiken.|

Zie [Schema voorbeelden](#event-schema-examples) die volgen.

## <a name="live-event-types"></a>Typen live gebeurtenissen

Media Services zendt **Live** ook de hieronder beschreven Live-gebeurtenistypen uit. Er zijn twee **Live** categorieën voor de Live-evenementen: evenementen op streamniveau en evenementen op trackniveau. 

### <a name="stream-level-events"></a>Gebeurtenissen op streamniveau

Gebeurtenissen op streamniveau worden verhoogd per stream of verbinding. Elke gebeurtenis `StreamId` heeft een parameter die de verbinding of stream identificeert. Elke stream of verbinding heeft een of meer tracks van verschillende typen. Eén verbinding van een encoder kan bijvoorbeeld één audiotrack en vier videotracks hebben. De typen streamgebeurtenissen zijn:

| Gebeurtenistype | Beschrijving |
| ---------- | ----------- |
| Microsoft.Media.LiveEventConnectionafgewezen | Encoder's verbindingspoging wordt afgewezen. |
| Microsoft.Media.LiveEventEncoderConnected | Encoder legt verbinding met live event. |
| Microsoft.Media.LiveEventEncoderLosgekoppeld | Encoder verbreekt de verbinding. |

Zie [Schema voorbeelden](#event-schema-examples) die volgen.

### <a name="track-level-events"></a>Gebeurtenissen op trackniveau

Gebeurtenissen op trackniveau worden per track verhoogd. 

> [!NOTE]
> Alle gebeurtenissen op trackniveau worden verhoogd nadat een live encoder is aangesloten.

De gebeurtenistypen op trackniveau zijn:

| Gebeurtenistype | Beschrijving |
| ---------- | ----------- |
| Microsoft.Media.LiveEventIncomingDataChunkDropped | Mediaserver laat gegevenssegment vallen omdat het te laat is of een overlappende tijdstempel heeft (timestamp van nieuwe gegevensbrok is minder dan de eindtijd van de vorige gegevensbrok). |
| Microsoft.Media.LiveEventIncomingStreamOntvangen | Mediaserver ontvangt de eerste gegevensbrok voor elk nummer in de stream of verbinding. |
| Microsoft.Media.LiveEventIncomingStreamsOutOfSync | Mediaserver detecteert dat audio- en videostreams niet synchroon lopen. Als waarschuwing gebruiken omdat de gebruikerservaring mogelijk niet wordt beïnvloed. |
| Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync | Mediaserver detecteert dat een van de twee videostreams afkomstig van externe encoder niet synchroon loopt. Als waarschuwing gebruiken omdat de gebruikerservaring mogelijk niet wordt beïnvloed. |
| Microsoft.Media.LiveEventIngestHeartbeat | Elke 20 seconden gepubliceerd voor elk nummer wanneer het live-evenement wordt uitgevoerd. Biedt een samenvatting van de inname van de gezondheid.<br/><br/>Nadat de encoder in eerste instantie was aangesloten, blijft de heartbeat-gebeurtenis elke 20 sec uitzenden of de encoder nog steeds verbonden is of niet. |
| Microsoft.Media.LiveEventTrackDiscontinuityDetected | Mediaserver detecteert discontinuïteit in het binnenkomende nummer. |

Zie [Schema voorbeelden](#event-schema-examples) die volgen.

## <a name="event-schema-examples"></a>Voorbeelden van gebeurtenisschema's

### <a name="jobstatechange"></a>JobStateChange JobStateChange

In het volgende voorbeeld wordt het schema van de gebeurtenis **JobStateChange** weergegeven: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
    "eventType": "Microsoft.Media.JobStateChange",
    "eventTime": "2018-04-20T21:26:13.8978772",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "previousState": "Processing",
      "state": "Finished"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Het gegevensobject heeft de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| vorigeStaat | tekenreeks | De stand van zaken vóór het evenement. |
| state | tekenreeks | De nieuwe status van de taak die in dit geval wordt aangemeld. Bijvoorbeeld 'Gepland: de taak is klaar om te starten' of 'Voltooid: de taak is voltooid'.|

Waar de taakstatus een van de waarden kan zijn: *Wachtrijen*, *Gepland*, *Verwerking*, *Voltooid*, *Fout*, *Geannuleerd*, *Annuleren*

> [!NOTE]
> *In de wachtrij* zal alleen aanwezig zijn in de **vorigeState-eigenschap,** maar niet in het **staatseigendom.**

### <a name="jobscheduled-jobprocessing-jobcanceling"></a>JobScheduled, JobProcessing, JobCanceling

Voor elke niet-definitieve taakstatuswijziging (zoals JobScheduled, JobProcessing, JobCanceling) ziet het voorbeeldschema er als volgt uit:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobProcessing",
  "eventTime": "2018-10-12T16:12:18.0839935",
  "id": "a0a6efc8-f647-4fc2-be73-861fa25ba2db",
  "data": {
    "previousState": "Scheduled",
    "state": "Processing",
    "correlationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="jobfinished-jobcanceled-joberrored"></a>JobFinished, JobCanceled, JobErrored

Voor elke laatste wijziging van de taakstatus (zoals JobFinished, JobCanceled, JobErrored) ziet het voorbeeldschema er als volgt uit:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobFinished",
  "eventTime": "2018-10-12T16:25:56.4115495",
  "id": "9e07e83a-dd6e-466b-a62f-27521b216f2a",
  "data": {
    "outputs": [
      {
        "@odata.type": "#Microsoft.Media.JobOutputAsset",
        "assetName": "output-7640689F",
        "error": null,
        "label": "VideoAnalyzerPreset_0",
        "progress": 100,
        "state": "Finished"
      }
    ],
    "previousState": "Processing",
    "state": "Finished",
    "correlationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Het gegevensobject heeft de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| Uitgangen | Matrix | Krijgt de taakuitgangen.|

### <a name="joboutputstatechange"></a>JobOutputStateChange

In het volgende voorbeeld wordt het schema van de gebeurtenis **JobOutputStateChange** weergegeven:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobOutputStateChange",
  "eventTime": "2018-10-12T16:25:56.0242854",
  "id": "dde85f46-b459-4775-b5c7-befe8e32cf90",
  "data": {
    "previousState": "Processing",
    "output": {
      "@odata.type": "#Microsoft.Media.JobOutputAsset",
      "assetName": "output-7640689F",
      "error": null,
      "label": "VideoAnalyzerPreset_0",
      "progress": 100,
      "state": "Finished"
    },
    "jobCorrelationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="joboutputscheduled-joboutputprocessing-joboutputfinished-joboutputcanceling-joboutputcanceled-joboutputerrored"></a>JobOutputScheduled, JobOutputProcessing, JobOutputFinished, JobOutputCanceling, JobOutputCanceled, JobOutputErrored

Voor elke statuswijziging van JobOutput ziet het voorbeeldschema er als volgt uit:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobOutputProcessing",
  "eventTime": "2018-10-12T16:12:18.0061141",
  "id": "f1fd5338-1b6c-4e31-83c9-cd7c88d2aedb",
  "data": {
    "previousState": "Scheduled",
    "output": {
      "@odata.type": "#Microsoft.Media.JobOutputAsset",
      "assetName": "output-7640689F",
      "error": null,
      "label": "VideoAnalyzerPreset_0",
      "progress": 0,
      "state": "Processing"
    },
    "jobCorrelationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```
### <a name="joboutputprogress"></a>JobOutputProgress

Het voorbeeldschema lijkt op het volgende:

 ```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/belohGroup/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/job-5AB6DE32",
  "eventType": "Microsoft.Media.JobOutputProgress",
  "eventTime": "2018-12-10T18:20:12.1514867",
  "id": "00000000-0000-0000-0000-000000000000",
  "data": {
    "jobCorrelationData": {
      "TestKey1": "TestValue1",
      "testKey2": "testValue2"
    },
    "label": "VideoAnalyzerPreset_0",
    "progress": 86
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="liveeventconnectionrejected"></a>LiveEventConnectionafgewezen

In het volgende voorbeeld wordt het schema van de gebeurtenis **LiveEventConnectionRejected weergegeven:** 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventConnectionRejected",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "b303db59-d5c1-47eb-927a-3650875fded1",
    "data": { 
      "streamId":"Mystream1",
      "ingestUrl": "http://abc.ingest.isml",
      "encoderIp": "118.238.251.xxx",
      "encoderPort": 52859,
      "resultCode": "MPE_INGEST_CODEC_NOT_SUPPORTED"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Het gegevensobject heeft de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| streamId (streamId) | tekenreeks | Id van de stream of verbinding. Encoder of klant is verantwoordelijk om deze ID toe te voegen in de inname URL. |  
| innameUrl | tekenreeks | Inname URL die door het live evenement. |  
| encoderIp | tekenreeks | IP van de encoder. |
| encoderPort | tekenreeks | Haven van de encoder van waar deze stroom vandaan komt. |
| resultaatCode | tekenreeks | De reden waarom de verbinding werd afgewezen. De resultaatcodes worden vermeld in de volgende tabel. |

U de foutresultaatcodes vinden in [foutcodes voor live gebeurtenissen](../media-services/latest/live-event-error-codes.md).

### <a name="liveeventencoderconnected"></a>LiveEventEncoderConnected

In het volgende voorbeeld wordt het schema van de gebeurtenis **LiveEventEncoderConnected weergegeven:** 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderConnected",
    "eventTime": "2018-08-07T23:08:09.1710643",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Het gegevensobject heeft de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| streamId (streamId) | tekenreeks | Id van de stream of verbinding. Encoder of klant is verantwoordelijk voor het verstrekken van deze ID in de inname URL. |
| innameUrl | tekenreeks | Inname URL die door het live evenement. |
| encoderIp | tekenreeks | IP van de encoder. |
| encoderPort | tekenreeks | Haven van de encoder van waar deze stroom vandaan komt. |

### <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

In het volgende voorbeeld wordt het schema van de gebeurtenis **LiveEventEncoderDisconnected weergegeven:** 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderDisconnected",
    "eventTime": "2018-08-07T23:08:09.1710872",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "resultCode": "S_OK"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Het gegevensobject heeft de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| streamId (streamId) | tekenreeks | Id van de stream of verbinding. Encoder of klant is verantwoordelijk om deze ID toe te voegen in de inname URL. |  
| innameUrl | tekenreeks | Inname URL die door het live evenement. |  
| encoderIp | tekenreeks | IP van de encoder. |
| encoderPort | tekenreeks | Haven van de encoder van waar deze stroom vandaan komt. |
| resultaatCode | tekenreeks | De reden voor de encoder loskoppelen. Het kan sierlijk loskoppelen of van een fout. De resultaatcodes worden vermeld in de volgende tabel. |

U de foutresultaatcodes vinden in [foutcodes voor live gebeurtenissen](../media-services/latest/live-event-error-codes.md).

De sierlijke loskoppelen resultaatcodes zijn:

| Resultaatcode | Beschrijving |
| ----------- | ----------- |
| S_ok | Encoder is met succes losgekoppeld. |
| MPE_CLIENT_TERMINATED_SESSION | Encoder losgekoppeld (RTMP). |
| MPE_CLIENT_DISCONNECTED | Encoder losgekoppeld (FMP4). |
| MPI_REST_API_CHANNEL_RESET | Kanaalreset opdracht is ontvangen. |
| MPI_REST_API_CHANNEL_STOP | Kanaalstopopdracht ontvangen. |
| MPI_REST_API_CHANNEL_STOP | Kanaal dat onderhoud ondergaat. |
| MPI_STREAM_HIT_EOF | EOF stream wordt verzonden door de encoder. |

### <a name="liveeventincomingdatachunkdropped"></a>LiveEventIncomingDataChunkDropped

In het volgende voorbeeld wordt het schema van de gebeurtenis **LiveEventIncomingDataChunkDropped weergegeven:** 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingDataChunkDropped",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "03da9c10-fde7-48e1-80d8-49936f2c3e7d",
    "data": { 
      "trackType": "Video",
      "trackName": "Video",
      "bitrate": 300000,
      "timestamp": 36656620000,
      "timescale": 10000000,
      "resultCode": "FragmentDrop_OverlapTimestamp"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Het gegevensobject heeft de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| trackType | tekenreeks | Type van de track (Audio / Video). |
| trackName | tekenreeks | Naam van het spoor. |
| Bitrate | geheel getal | Bitrate van het spoor. |
| tijdstempel | tekenreeks | Tijdstempel van de gegevens brok gedaald. |
| Tijdschaal | tekenreeks | Tijdschaal van de tijdstempel. |
| resultaatCode | tekenreeks | Reden van de daling van de gegevensbrok. **FragmentDrop_OverlapTimestamp** of **FragmentDrop_NonIncreasingTimestamp**. |

### <a name="liveeventincomingstreamreceived"></a>LiveEventIncomingStreamOntvangen

In het volgende voorbeeld wordt het schema van de gebeurtenis **LiveEventIncomingStreamReceived weergegeven:** 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamReceived",
    "eventTime": "2018-08-07T23:08:10.5069288Z",
    "id": "7f939a08-320c-47e7-8250-43dcfc04ab4d",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml/Streams(15864-stream0)15864-stream0",
      "trackType": "video",
      "trackName": "video",
      "bitrate": 2962000,
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "timestamp": "15336831655032322",
      "duration": "20000000",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Het gegevensobject heeft de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| trackType | tekenreeks | Type van de track (Audio / Video). |
| trackName | tekenreeks | Naam van de track (ofwel verstrekt door de encoder of, in het geval van RTMP, server genereert in *TrackType_Bitrate* formaat). |
| Bitrate | geheel getal | Bitrate van het spoor. |
| innameUrl | tekenreeks | Inname URL die door het live evenement. |
| encoderIp | tekenreeks  | IP van de encoder. |
| encoderPort | tekenreeks | Haven van de encoder van waar deze stroom vandaan komt. |
| tijdstempel | tekenreeks | Eerste timestamp van de ontvangen gegevensbrok. |
| Tijdschaal | tekenreeks | Tijdschaal waarin tijdstempel is weergegeven. |

### <a name="liveeventincomingstreamsoutofsync"></a>LiveEventIncomingStreamsOutOfSync

In het volgende voorbeeld wordt het schema van de gebeurtenis **LiveEventIncomingStreamsOutOutSync** weergegeven: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamsOutOfSync",
    "eventTime": "2018-08-10T02:26:20.6269183Z",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "minLastTimestamp": "319996",
      "typeOfStreamWithMinLastTimestamp": "Audio",
      "maxLastTimestamp": "366000",
      "typeOfStreamWithMaxLastTimestamp": "Video",
      "timescaleOfMinLastTimestamp": "10000000", 
      "timescaleOfMaxLastTimestamp": "10000000"       
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Het gegevensobject heeft de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| minLastTimestamp | tekenreeks | Minimum van de laatste tijdstempels tussen alle tracks (audio of video). |
| typeOfTrackWithMinLastTimestamp | tekenreeks | Type van de track (audio of video) met minimale laatste tijdstempel. |
| maxLastTimestamp | tekenreeks | Maximaal alle tijdstempels tussen alle tracks (audio of video). |
| typeOfTrackWithMaxLastTimestamp | tekenreeks | Type van de track (audio of video) met maximale laatste tijdstempel. |
| tijdschaalOfMinLastTimestamp| tekenreeks | Krijgt de tijdschaal waarin "MinLastTimestamp" is vertegenwoordigd.|
| tijdschaalOfMaxLastTimestamp| tekenreeks | Krijgt de tijdschaal waarin "MaxLastTimestamp" is vertegenwoordigd.|

### <a name="liveeventincomingvideostreamsoutofsync"></a>LiveEventIncomingVideoStreamsOutOfSync

In het volgende voorbeeld wordt het schema van de gebeurtenis **LiveEventIncomingVideoStreamsOutOutSync** weergegeven: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/LiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "6dd4d862-d442-40a0-b9f3-fc14bcf6d750",
    "data": {
      "firstTimestamp": "2162058216",
      "firstDuration": "2000",
      "secondTimestamp": "2162057216",
      "secondDuration": "2000",
      "timescale": "10000000"      
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Het gegevensobject heeft de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| firstTimestamp | tekenreeks | Timestamp ontvangen voor een van de tracks / kwaliteit niveaus van het type video. |
| firstDuur | tekenreeks | Duur van de gegevensbrok met de eerste tijdstempel. |
| secondTimestamp | tekenreeks  | Timestamp ontvangen voor een aantal andere track / kwaliteit niveau van het type video. |
| tweedeDuur | tekenreeks | Duur van de gegevensbrok met tweede tijdstempel. |
| Tijdschaal | tekenreeks | Tijdschaal van tijdstempels en duur.|

### <a name="liveeventingestheartbeat"></a>LiveEventIngestHeartbeat

In het volgende voorbeeld wordt het schema van de gebeurtenis **LiveEventIngestHeartbeat** weergegeven: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIngestHeartbeat",
    "eventTime": "2018-08-07T23:17:57.4610506",
    "id": "7f450938-491f-41e1-b06f-c6cd3965d786",
    "data": {
      "trackType": "audio",
      "trackName": "audio",
      "bitrate": 160000,
      "incomingBitrate": 155903,
      "lastTimestamp": "15336837535253637",
      "timescale": "10000000",
      "overlapCount": 0,
      "discontinuityCount": 0,
      "nonincreasingCount": 0,
      "unexpectedBitrate": false,
      "state": "Running",
      "healthy": true
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Het gegevensobject heeft de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| trackType | tekenreeks | Type van de track (Audio / Video). |
| trackName | tekenreeks | Naam van de track (ofwel verstrekt door de encoder of, in het geval van RTMP, server genereert in *TrackType_Bitrate* formaat). |
| Bitrate | geheel getal | Bitrate van het spoor. |
| inkomendeBitrate | geheel getal | Berekende bitrate op basis van gegevensbrokken afkomstig van encoder. |
| laatsteTimestamp | tekenreeks | Laatste tijdstempel ontvangen voor een track in de laatste 20 seconden. |
| Tijdschaal | tekenreeks | Tijdschaal waarin tijdstempels worden uitgedrukt. |
| overlappingTelling | geheel getal | Het aantal gegevensbrokken had in de afgelopen 20 seconden tijdstempels overlapt. |
| discontinuïteitTelling | geheel getal | Aantal discontinuïteiten waargenomen in de laatste 20 seconden. |
| niet Toenemend aantal | geheel getal | Het aantal gegevensbrokken met tijdstempels in het verleden werd ontvangen in de laatste 20 seconden. |
| onverwachtBitrate | Booleaanse waarde | Als verwachte en werkelijke bitrates verschillen met meer dan toegestaan limiet in de laatste 20 seconden. Het is waar als en alleen als, inkomendeBitrate >= 2 * bitrate OF inkomendeBitrate <= bitrate/2 OF IncomingBitrate = 0. |
| state | tekenreeks | Staat van het live-evenement. |
| Gezonde | Booleaanse waarde | Geeft aan of inname gezond is op basis van de tellingen en vlaggen. Gezond is waar als overlapCount = 0 && discontinuïteitTelling = 0 && niet-toenameaantal = 0 && onverwachte Bitrate = false. |

### <a name="liveeventtrackdiscontinuitydetected"></a>LiveEventTrackDiscontinuityDetected

In het volgende voorbeeld wordt het schema van de gebeurtenis **LiveEventTrackDiscontinuityDetected weergegeven:** 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventTrackDiscontinuityDetected",
    "eventTime": "2018-08-07T23:18:06.1270405Z",
    "id": "5f4c510d-5be7-4bef-baf0-64b828be9c9b",
    "data": {
      "trackName": "video",
      "previousTimestamp": "15336837615032322",
      "trackType": "video",
      "bitrate": 2962000,
      "newTimestamp": "15336837619774273",
      "discontinuityGap": "575284",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Het gegevensobject heeft de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| trackType | tekenreeks | Type van de track (Audio / Video). |
| trackName | tekenreeks | Naam van de track (ofwel verstrekt door de encoder of, in het geval van RTMP, server genereert in *TrackType_Bitrate* formaat). |
| Bitrate | geheel getal | Bitrate van het spoor. |
| vorige Timestamp | tekenreeks | Tijdstempel van het vorige fragment. |
| newTimestamp | tekenreeks | Tijdstempel van het huidige fragment. |
| discontinuïteitGap | tekenreeks | Kloof tussen de twee tijdstempels. |
| Tijdschaal | tekenreeks | Tijdschaal waarin zowel tijdstempel als discontinuïteitskloof worden weergegeven. |

### <a name="common-event-properties"></a>Algemene gebeurteniseigenschappen

Een gebeurtenis heeft de volgende gegevens op het hoogste niveau:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| onderwerp | tekenreeks | Het EventGrid-onderwerp. Deze eigenschap heeft de resource-id voor het Media Services-account. |
| Onderwerp | tekenreeks | Het resourcepad voor het kanaal MediaServices onder het Media Services-account. Het toevoegen van het onderwerp en onderwerp geven u de resource-ID voor de taak. |
| eventType | tekenreeks | Een van de geregistreerde gebeurtenistypen voor deze gebeurtenisbron. Bijvoorbeeld "Microsoft.Media.JobStateChange". |
| eventTime | tekenreeks | De tijd dat de gebeurtenis wordt gegenereerd op basis van de UTC-tijd van de provider. |
| id | tekenreeks | Unieke id voor de gebeurtenis. |
| data | object | Gegevens over mediaservices-gebeurtenissen. |
| dataVersion | tekenreeks | De schemaversie van het gegevensobject. De uitgever definieert de schemaversie. |
| metadataVersion | tekenreeks | De schemaversie van de metagegevens van de gebeurtenis. Event Grid definieert het schema voor de eigenschappen op het hoogste niveau. Event Grid biedt deze waarde. |

## <a name="next-steps"></a>Volgende stappen

[Registreren voor gebeurtenisvan taakstatuswijziging](../media-services/latest/job-state-events-cli-how-to.md)

## <a name="see-also"></a>Zie ook

- [EventGrid .NET SDK met gebeurtenissen in Media Service](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Definities van gebeurtenissen in Media Services](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)
- [Foutcodes voor Live-gebeurtenissen](../media-services/latest/live-event-error-codes.md)
