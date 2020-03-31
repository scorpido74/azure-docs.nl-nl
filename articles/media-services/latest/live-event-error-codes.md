---
title: Foutcodes voor live-gebeurtenissen in Azure Media Services | Microsoft Documenten
description: In dit artikel worden foutcodes voor live-gebeurtenissen weergegeven.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2020
ms.author: juliako
ms.openlocfilehash: f9067562f67190b8bc04392f33078d4d3262f986
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654576"
---
# <a name="media-services-live-event-error-codes"></a>Foutcodes voor Live Event media Services

De tabellen in deze sectie bevatten de foutcodes [live-evenement.](live-events-outputs-concept.md)

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionafgewezen

Wanneer u zich abonneert op de [gebeurtenis gebeurtenis gebeurtenis net](https://docs.microsoft.com/azure/event-grid/) voor een live-evenement, ziet u mogelijk een van de volgende fouten van de Gebeurtenis [LiveEventConnectionRejected.](media-services-event-schemas.md#liveeventconnectionrejected)

| Resultaatcode | Beschrijving |
| ----------- | ----------- |
| MPE_RTMP_APPID_AUTH_FAILURE | Onjuiste inname-URL |
| MPE_INGEST_ENCODER_CONNECTION_DENIED | Encoder IP is niet aanwezig in IP-lijst met toegestane lijst geconfigureerd |
| MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED | De RTMP-encoder heeft de opdracht SetDataFrame niet verzonden. |
| MPE_INGEST_CODEC_NOT_SUPPORTED | Codec-opgegeven wordt niet ondersteund. |
| MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED |De informatie over de beschrijving van de media is niet ontvangen voordat de werkelijke mediagegevens werden geleverd.|
| MPE_INGEST_MEDIA_QUALITIES_EXCEEDED |Het aantal kwaliteiten voor audio- of videotype overschreed de maximaal toegestane limiet.|
| MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED |De totale inkomende bitrate in een live-evenement of kanaalservice heeft de maximaal toegestane limiet overschreden.|
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | De tijdstempel voor video of audio FLVTag is ongeldig van RTMP-encoder. |
| MPE_INGEST_FRAMERATE_EXCEEDED | De inkomende encoder opgenomen streams met framerates overschreden de maximaal toegestane 30fps voor het coderen van live evenementen / kanalen.|
| MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED | De inkomende encoder opgenomen streams overschreden de volgende toegestane resoluties: 1920x1088 voor het coderen van live evenementen / kanalen en 4096 x 2160 voor pass-through live evenementen / kanalen.|

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

Mogelijk ziet u een van de volgende fouten van de gebeurtenis [LiveEventEncoderDisconnected.](media-services-event-schemas.md#liveeventencoderdisconnected)

|Resultaatcode|Beschrijving|
|---|---|
|MPE_RTMP_SESSION_IDLE_TIMEOUT|Rtmp-sessie time-out na niet-actief voor toegestane tijdslimiet.|
|MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID|De tijdstempel voor video of audio FLVTag is ongeldig van RTMP-encoder.|
|MPE_CAPACITY_LIMIT_REACHED|Encoder die gegevens te snel verzendt.|
|Onbekende foutcodes|Deze foutcodes kunnen variëren van geheugenfout tot dubbele vermeldingen in hashmap.|


## <a name="see-also"></a>Zie ook

[Foutcodes voor streamingeindpunt (Origin)](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>Volgende stappen

[Zelfstudie: Live streamen met Media Services](stream-live-tutorial-with-api.md)
