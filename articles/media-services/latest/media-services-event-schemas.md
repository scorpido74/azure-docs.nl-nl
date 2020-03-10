---
title: Azure Event Grid schema's voor Media Services gebeurtenissen
description: Hierin worden de eigenschappen beschreven die worden gegeven voor Media Services gebeurtenissen met Azure Event Grid
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: reference
ms.date: 02/25/2020
ms.author: juliako
ms.openlocfilehash: d4a206bbddedfe9f23a943df27c6ac4b5fe17e8a
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78359281"
---
# <a name="azure-event-grid-schemas-for-media-services-events"></a>Azure Event Grid schema's voor Media Services gebeurtenissen

Dit artikel bevat de schema's en eigenschappen voor Media Services gebeurtenissen.

Zie [Media Services gebeurtenis bron](../../event-grid/event-sources.md#azure-subscriptions)voor een lijst met voorbeeld scripts en zelf studies.

## <a name="job-related-event-types"></a>Aan taken gerelateerde gebeurtenis typen

Media Services worden de aan de **taak** gerelateerde gebeurtenis typen die hieronder worden beschreven. Er zijn twee categorieën voor de **taak** gerelateerde gebeurtenissen: "bewaking van de taak status wijzigingen" en "bewaking van de taak uitvoer status wijzigingen". 

U kunt zich registreren voor alle gebeurtenissen door u te abonneren op de JobStateChange-gebeurtenis. U kunt ook alleen een abonnement nemen voor specifieke gebeurtenissen (bijvoorbeeld de laatste statussen zoals JobErrored, JobFinished en JobCanceled).   

### <a name="monitoring-job-state-changes"></a>Taak status wijzigingen bewaken

| Gebeurtenis type | Beschrijving |
| ---------- | ----------- |
| Microsoft.Media.JobStateChange| Een gebeurtenis ophalen voor alle taak status wijzigingen. |
| Microsoft.Media.JobScheduled| Een gebeurtenis ophalen wanneer taak overgang naar een geplande status heeft. |
| Microsoft.Media.JobProcessing| Een gebeurtenis ophalen wanneer de taak wordt overgezet naar de verwerkings status. |
| Microsoft.Media.JobCanceling| Een gebeurtenis ophalen wanneer de taak wordt overgezet om de status te annuleren. |
| Microsoft.Media.JobFinished| Een gebeurtenis ophalen wanneer de status van de taak verandert in voltooid. Dit is een eind status die taak uitvoer bevat.|
| Microsoft.Media.JobCanceled| Een gebeurtenis ophalen wanneer taak overgang naar geannuleerde status heeft. Dit is een eind status die taak uitvoer bevat.|
| Microsoft.Media.JobErrored| Een gebeurtenis ophalen wanneer de taak overschakelt naar een fout status. Dit is een eind status die taak uitvoer bevat.|

Zie [schema voorbeelden](#event-schema-examples) die volgen.

### <a name="monitoring-job-output-state-changes"></a>Taak uitvoer status wijzigingen bewaken

Een taak kan meerdere taak uitvoer bevatten (als u de trans formatie hebt geconfigureerd om meerdere taak uitvoer te hebben.) Als u de details van de afzonderlijke taak uitvoer wilt bijhouden, moet u Luis teren naar een gebeurtenis voor het wijzigen van de taak uitvoer.

Elke **taak** gaat op een hoger niveau dan **JobOutput**. Daarom worden taak uitvoer gebeurtenissen binnen een bijbehorende taak geactiveerd. 

De fout berichten in `JobFinished`, `JobCanceled``JobError` de cumulatieve resultaten voor elke taak uitvoer worden uitgevoerd, wanneer alle taken zijn voltooid. Overwegende dat de uitvoer gebeurtenissen van de taak worden gestart terwijl elke taak is voltooid. Als u bijvoorbeeld een uitvoer code ring hebt, gevolgd door een video Analytics-uitvoer, worden er twee gebeurtenissen weer gegeven die worden gestart als taak uitvoer gebeurtenissen voordat de laatste JobFinished-gebeurtenis wordt geactiveerd met de geaggregeerde gegevens.

| Gebeurtenis type | Beschrijving |
| ---------- | ----------- |
| Microsoft.Media.JobOutputStateChange| Een gebeurtenis ophalen voor alle wijzigingen in de status van de taak uitvoer. |
| Microsoft.Media.JobOutputScheduled| Een gebeurtenis ophalen wanneer de taak uitvoer overschakelt naar de geplande status. |
| Microsoft.Media.JobOutputProcessing| Een gebeurtenis ophalen wanneer de taak uitvoer overschakelt naar de verwerkings status. |
| Microsoft.Media.JobOutputCanceling| Een gebeurtenis ophalen wanneer de taak uitvoer overgangen heeft om de status te annuleren.|
| Microsoft.Media.JobOutputFinished| Een gebeurtenis ophalen wanneer de taak uitvoer overschakelt naar de status voltooid.|
| Microsoft.Media.JobOutputCanceled| Een gebeurtenis ophalen wanneer taak uitvoer overgaat naar geannuleerde status.|
| Microsoft.Media.JobOutputErrored| Een gebeurtenis ophalen wanneer de taak uitvoer overschakelt naar de fout status.|

Zie [schema voorbeelden](#event-schema-examples) die volgen.

### <a name="monitoring-job-output-progress"></a>Voortgang van taak uitvoer bewaken

| Gebeurtenis type | Beschrijving |
| ---------- | ----------- |
| Microsoft.Media.JobOutputProgress| Deze gebeurtenis weerspiegelt de voortgang van de taak verwerking, van 0% tot 100%. De service probeert een gebeurtenis te verzenden als er 5% of hoger een toename van de voortgangs waarde heeft of meer dan 30 seconden sinds de laatste gebeurtenis (heartbeat). De voortgangs waarde is niet gegarandeerd om 0% te beginnen, of om 100% te bereiken, noch is het gegarandeerd om een constant percentage te verhogen gedurende een bepaalde periode. Deze gebeurtenis mag niet worden gebruikt om te bepalen dat de verwerking is voltooid: gebruik in plaats daarvan de status wijzigings gebeurtenissen.|

Zie [schema voorbeelden](#event-schema-examples) die volgen.

## <a name="live-event-types"></a>Live gebeurtenis typen

Media Services ook de **Live** gebeurtenis typen die hieronder worden beschreven. Er zijn twee categorieën voor **Live** Events: gebeurtenissen op stroom niveau en gebeurtenissen op traceer niveau. 

### <a name="stream-level-events"></a>Gebeurtenissen op stroom niveau

Gebeurtenissen op stroom niveau worden gegenereerd per stroom of verbinding. Elke gebeurtenis heeft een `StreamId` para meter waarmee de verbinding of stroom wordt geïdentificeerd. Elke stroom of verbinding heeft een of meer sporen van verschillende typen. Eén verbinding van een coderings programma kan bijvoorbeeld één audio track en vier video sporen hebben. De stroom gebeurtenis typen zijn:

| Gebeurtenis type | Beschrijving |
| ---------- | ----------- |
| Microsoft.Media.LiveEventConnectionRejected | De verbindings poging van het coderings programma wordt geweigerd. |
| Microsoft.Media.LiveEventEncoderConnected | Encoder maakt verbinding met live event. |
| Microsoft.Media.LiveEventEncoderDisconnected | Encoder verbreekt de verbinding. |

Zie [schema voorbeelden](#event-schema-examples) die volgen.

### <a name="track-level-events"></a>Gebeurtenissen op spoor niveau

Gebeurtenissen op spoor niveau worden verhoogd per spoor. 

> [!NOTE]
> Alle gebeurtenissen op traceer niveau worden gegenereerd nadat een live coderings programma is verbonden.

De gebeurtenis typen op spoor niveau zijn:

| Gebeurtenis type | Beschrijving |
| ---------- | ----------- |
| Microsoft.Media.LiveEventIncomingDataChunkDropped | Media server daalt gegevens segment omdat het te laat is of een overlappende tijds tempel heeft (tijds tempel van nieuw gegevens segment is kleiner dan de eind tijd van het vorige gegevens segment). |
| Microsoft.Media.LiveEventIncomingStreamReceived | Media server ontvangt eerste gegevens segment voor elk spoor in de stroom of verbinding. |
| Microsoft.Media.LiveEventIncomingStreamsOutOfSync | Media server detecteert audio-en video-streams die niet zijn gesynchroniseerd. Gebruik als een waarschuwing omdat de gebruikers ervaring mogelijk niet van invloed is op de gebruiker. |
| Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync | Media server detecteert een van de twee video stromen die afkomstig zijn van externe Encoder, zijn niet synchroon. Gebruik als een waarschuwing omdat de gebruikers ervaring mogelijk niet van invloed is op de gebruiker. |
| Microsoft.Media.LiveEventIngestHeartbeat | Gepubliceerd om de 20 seconden voor elk spoor wanneer Live Event wordt uitgevoerd. Biedt een samen vatting van status opname.<br/><br/>Nadat het coderings programma voor het eerst is verbonden, blijft de heartbeat-gebeurtenis elke 20 sec verzenden, of het coderings programma nog steeds is verbonden of niet. |
| Microsoft.Media.LiveEventTrackDiscontinuityDetected | Media server detecteert de onderbreking in de inkomende track. |

Zie [schema voorbeelden](#event-schema-examples) die volgen.

## <a name="event-schema-examples"></a>Voor beelden van gebeurtenis schema's

### <a name="jobstatechange"></a>JobStateChange

In het volgende voor beeld ziet u het schema van de gebeurtenis **JobStateChange** : 

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

Het gegevens object heeft de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| previousState | tekenreeks | De status van de taak vóór de gebeurtenis. |
| state | tekenreeks | De nieuwe status van de taak die wordt gewaarschuwd bij deze gebeurtenis. Bijvoorbeeld ' gepland: de taak is gereed om te beginnen ' of ' voltooid: de taak is voltooid '.|

Waarbij de taak status kan een van de volgende waarden zijn *: in de wachtrij geplaatst*, *gepland*, *verwerkt*, *voltooid*, *fout*, *geannuleerd*, *Annuleren*

> [!NOTE]
> De *wachtrij* is alleen aanwezig in de eigenschap **previousState** , maar niet in de eigenschap **State** .

### <a name="jobscheduled-jobprocessing-jobcanceling"></a>JobScheduled, JobProcessing, JobCanceling

Voor elke niet-definitieve taak Status wijziging (zoals JobScheduled, JobProcessing, JobCanceling) ziet het voorbeeld schema er ongeveer als volgt uit:

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

Voor elke definitieve wijziging van de taak status (zoals JobFinished, JobCanceled, JobErrored) ziet het voorbeeld schema er ongeveer als volgt uit:

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

Het gegevens object heeft de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| outputs | Matrix | Hiermee haalt u de taak uitvoer op.|

### <a name="joboutputstatechange"></a>JobOutputStateChange

In het volgende voor beeld ziet u het schema van de gebeurtenis **JobOutputStateChange** :

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

Voor elke wijziging van de JobOutput-status ziet het voorbeeld schema er ongeveer als volgt uit:

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

Het voorbeeld schema ziet er ongeveer als volgt uit:

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

### <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

In het volgende voor beeld ziet u het schema van de gebeurtenis **LiveEventConnectionRejected** : 

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

Het gegevens object heeft de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| streamId | tekenreeks | De id van de stroom of de verbinding. Encoder of klant is verantwoordelijk voor het toevoegen van deze ID in de opname-URL. |  
| ingestUrl | tekenreeks | De opname-URL die wordt verschaft door de live gebeurtenis. |  
| encoderIp | tekenreeks | IP-adres van het coderings programma. |
| encoderPort | tekenreeks | Poort van het coderings programma waaruit deze stroom afkomstig is. |
| resultCode | tekenreeks | De reden waarom de verbinding is geweigerd. De resultaat codes worden in de volgende tabel weer gegeven. |

U kunt de fout codes vinden in [fouten met fout codes voor Live-gebeurtenissen](live-event-error-codes.md).

### <a name="liveeventencoderconnected"></a>LiveEventEncoderConnected

In het volgende voor beeld ziet u het schema van de gebeurtenis **LiveEventEncoderConnected** : 

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

Het gegevens object heeft de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| streamId | tekenreeks | De id van de stroom of de verbinding. Encoder of klant is verantwoordelijk voor het opgeven van deze ID in de opname-URL. |
| ingestUrl | tekenreeks | De opname-URL die wordt verschaft door de live gebeurtenis. |
| encoderIp | tekenreeks | IP-adres van het coderings programma. |
| encoderPort | tekenreeks | Poort van het coderings programma waaruit deze stroom afkomstig is. |

### <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

In het volgende voor beeld ziet u het schema van de gebeurtenis **LiveEventEncoderDisconnected** : 

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

Het gegevens object heeft de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| streamId | tekenreeks | De id van de stroom of de verbinding. Encoder of klant is verantwoordelijk voor het toevoegen van deze ID in de opname-URL. |  
| ingestUrl | tekenreeks | De opname-URL die wordt verschaft door de live gebeurtenis. |  
| encoderIp | tekenreeks | IP-adres van het coderings programma. |
| encoderPort | tekenreeks | Poort van het coderings programma waaruit deze stroom afkomstig is. |
| resultCode | tekenreeks | De reden voor het verbreken van de verbinding met het coderings programma. Het kan zijn dat de verbinding wordt verbroken of dat er een fout optreedt. De resultaat codes worden in de volgende tabel weer gegeven. |

U kunt de fout codes vinden in [fouten met fout codes voor Live-gebeurtenissen](live-event-error-codes.md).

De resultaten van de gevolgde verbrekings verbinding zijn:

| Resultaat code | Beschrijving |
| ----------- | ----------- |
| S_OK | Het coderings programma is verbroken. |
| MPE_CLIENT_TERMINATED_SESSION | Het coderings programma is verbroken (RTMP). |
| MPE_CLIENT_DISCONNECTED | Het coderings programma is verbroken (FMP4). |
| MPI_REST_API_CHANNEL_RESET | Opdracht voor kanaal opnieuw instellen is ontvangen. |
| MPI_REST_API_CHANNEL_STOP | Opdracht voor stoppen van kanaal ontvangen. |
| MPI_REST_API_CHANNEL_STOP | Het kanaal ondergaat onderhoud. |
| MPI_STREAM_HIT_EOF | EOF-stroom wordt verzonden door het coderings programma. |

### <a name="liveeventincomingdatachunkdropped"></a>LiveEventIncomingDataChunkDropped

In het volgende voor beeld ziet u het schema van de gebeurtenis **LiveEventIncomingDataChunkDropped** : 

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

Het gegevens object heeft de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| trackType | tekenreeks | Het type van het spoor (audio/video). |
| trackName | tekenreeks | De naam van het spoor. |
| bitrate | geheel getal | De bitrate van het spoor. |
| tijdstempel | tekenreeks | Tijds tempel van het gegevens segment is verwijderd. |
| timescale | tekenreeks | Tijd schaal van de tijds tempel. |
| resultCode | tekenreeks | De reden van de drop van het gegevens segment. **FragmentDrop_OverlapTimestamp** of **FragmentDrop_NonIncreasingTimestamp**. |

### <a name="liveeventincomingstreamreceived"></a>LiveEventIncomingStreamReceived

In het volgende voor beeld ziet u het schema van de gebeurtenis **LiveEventIncomingStreamReceived** : 

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

Het gegevens object heeft de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| trackType | tekenreeks | Het type van het spoor (audio/video). |
| trackName | tekenreeks | De naam van het spoor (dat wordt gegeven door de encoder of, in het geval van RTMP, wordt door de server gegenereerd in *TrackType_Bitrate* -indeling). |
| bitrate | geheel getal | De bitrate van het spoor. |
| ingestUrl | tekenreeks | De opname-URL die wordt verschaft door de live gebeurtenis. |
| encoderIp | tekenreeks  | IP-adres van het coderings programma. |
| encoderPort | tekenreeks | Poort van het coderings programma waaruit deze stroom afkomstig is. |
| tijdstempel | tekenreeks | Eerste tijds tempel van het ontvangen gegevens segment. |
| timescale | tekenreeks | De tijd schaal waarin tijds tempel wordt weer gegeven. |

### <a name="liveeventincomingstreamsoutofsync"></a>LiveEventIncomingStreamsOutOfSync

In het volgende voor beeld ziet u het schema van de gebeurtenis **LiveEventIncomingStreamsOutOfSync** : 

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

Het gegevens object heeft de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| minLastTimestamp | tekenreeks | Mini maal aantal laatste tijds tempels tussen alle sporen (audio of video). |
| typeOfTrackWithMinLastTimestamp | tekenreeks | Het type van het spoor (audio of video) met de minimale laatste tijds tempel. |
| maxLastTimestamp | tekenreeks | Het maximum van alle tijds tempels van alle sporen (audio of video). |
| typeOfTrackWithMaxLastTimestamp | tekenreeks | Het type van het spoor (audio of video) met de maximale laatste tijds tempel. |
| timescaleOfMinLastTimestamp| tekenreeks | Hiermee wordt de tijd schaal opgehaald waarin ' MinLastTimestamp ' wordt weer gegeven.|
| timescaleOfMaxLastTimestamp| tekenreeks | Hiermee wordt de tijd schaal opgehaald waarin ' MaxLastTimestamp ' wordt weer gegeven.|

### <a name="liveeventincomingvideostreamsoutofsync"></a>LiveEventIncomingVideoStreamsOutOfSync

In het volgende voor beeld ziet u het schema van de gebeurtenis **LiveEventIncomingVideoStreamsOutOfSync** : 

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

Het gegevens object heeft de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| firstTimestamp | tekenreeks | Er is een tijds tempel ontvangen voor een van de typen video tracks/kwaliteit. |
| firstDuration | tekenreeks | De duur van het gegevens segment met de eerste time stamp. |
| secondTimestamp | tekenreeks  | Er is een tijds tempel ontvangen voor een ander spoor/kwaliteits niveau van het type video. |
| secondDuration | tekenreeks | De duur van het gegevens segment met de tweede tijds tempel. |
| timescale | tekenreeks | Tijd schaal van tijds tempels en duur.|

### <a name="liveeventingestheartbeat"></a>LiveEventIngestHeartbeat

In het volgende voor beeld ziet u het schema van de gebeurtenis **LiveEventIngestHeartbeat** : 

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

Het gegevens object heeft de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| trackType | tekenreeks | Het type van het spoor (audio/video). |
| trackName | tekenreeks | De naam van het spoor (dat wordt gegeven door de encoder of, in het geval van RTMP, wordt door de server gegenereerd in *TrackType_Bitrate* -indeling). |
| bitrate | geheel getal | De bitrate van het spoor. |
| incomingBitrate | geheel getal | Berekende bitrate op basis van gegevens segmenten die afkomstig zijn van encoder. |
| lastTimestamp | tekenreeks | Laatste ontvangen tijds tempel voor een track in de afgelopen 20 seconden. |
| timescale | tekenreeks | De tijd schaal waarin tijds tempels worden weer gegeven. |
| overlapCount | geheel getal | Het aantal gegevens segmenten heeft de afgelopen 20 seconden overlappende tijds tempels. |
| discontinuityCount | geheel getal | Aantal in de afgelopen 20 seconden waargenomen handelingen. |
| nonIncreasingCount | geheel getal | Het aantal gegevens segmenten met tijds tempels in het verleden zijn in de afgelopen 20 seconden ontvangen. |
| unexpectedBitrate | bool | Als de verwachte en werkelijke bitsnelheid per seconde in de afgelopen 20 seconden verschillen van de toegestane limiet. Het is waar en alleen als, incomingBitrate > = 2 * bitrate of incomingBitrate < = bitrate/2 of IncomingBitrate = 0. |
| state | tekenreeks | De status van de live-gebeurtenis. |
| healthy | bool | Hiermee wordt aangegeven of opname in orde is op basis van de aantallen en vlaggen. In orde is waar als overlapCount = 0 & & discontinuityCount = 0 & & nonIncreasingCount = 0 & & unexpectedBitrate = false. |

### <a name="liveeventtrackdiscontinuitydetected"></a>LiveEventTrackDiscontinuityDetected

In het volgende voor beeld ziet u het schema van de gebeurtenis **LiveEventTrackDiscontinuityDetected** : 

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

Het gegevens object heeft de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| trackType | tekenreeks | Het type van het spoor (audio/video). |
| trackName | tekenreeks | De naam van het spoor (dat wordt gegeven door de encoder of, in het geval van RTMP, wordt door de server gegenereerd in *TrackType_Bitrate* -indeling). |
| bitrate | geheel getal | De bitrate van het spoor. |
| previousTimestamp | tekenreeks | Tijds tempel van het vorige fragment. |
| newTimestamp | tekenreeks | Tijds tempel van het huidige fragment. |
| discontinuityGap | tekenreeks | Tussen ruimte tussen twee tijds tempels. |
| timescale | tekenreeks | De tijd schaal waarin zowel de tijds tempel als de schei ding tussen ruimte worden weer gegeven. |

### <a name="common-event-properties"></a>Algemene gebeurtenis eigenschappen

Een gebeurtenis heeft de volgende gegevens op het hoogste niveau:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| onderwerp | tekenreeks | Het onderwerp EventGrid. Deze eigenschap heeft de resource-ID voor het Media Services-account. |
| subject | tekenreeks | Het bronpad voor het Media Services kanaal onder het Media Services-account. Als u het onderwerp en onderwerp samenvoegt, geeft u de resource-ID voor de taak. |
| eventType | tekenreeks | Een van de geregistreerde gebeurtenis typen voor deze gebeurtenis bron. Bijvoorbeeld ' micro soft. media. JobStateChange '. |
| eventTime | tekenreeks | Het tijdstip waarop de gebeurtenis is gegenereerd op basis van de UTC-tijd van de provider. |
| id | tekenreeks | De unieke id voor de gebeurtenis. |
| data | object | Media Services gebeurtenis gegevens. |
| dataVersion | tekenreeks | De schema versie van het gegevens object. De uitgever definieert de schema versie. |
| metadataVersion | tekenreeks | De schema versie van de meta gegevens van de gebeurtenis. Event Grid definieert het schema van de eigenschappen op het hoogste niveau. Event Grid levert deze waarde. |

## <a name="next-steps"></a>Volgende stappen

[Registreren voor taak status wijzigings gebeurtenissen](job-state-events-cli-how-to.md)

## <a name="see-also"></a>Zie ook

- [EventGrid .NET SDK die media service-gebeurtenissen bevat](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Definities van Media Services gebeurtenissen](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)
- [Fout codes voor Live-gebeurtenissen](live-event-error-codes.md)
