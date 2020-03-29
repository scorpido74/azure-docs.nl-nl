---
title: Telemetrie van Azure Media Services | Microsoft Documenten
description: In dit artikel vindt u een overzicht van de telemetrie van Microsoft Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 95c20ec4-c782-4063-8042-b79f95741d28
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: e2cbb36158722a47518f575b391340b5e25bd908
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74895780"
---
# <a name="azure-media-services-telemetry"></a>Telemetrie van Azure Media Services  


> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zie ook [migratierichtlijnen van v2 naar v3](../latest/migrate-from-v2-to-v3.md)

Met Azure Media Services (AMS) heeft u toegang tot telemetrie-/metrische gegevens voor zijn services. Met de huidige versie van AMS u telemetriegegevens verzamelen voor live **kanaal-** en **streamingendpoint-** en live **archiefentiteiten.** 

Telemetrie wordt geschreven naar een opslagtabel in een Azure Storage-account dat u opgeeft (meestal gebruikt u het opslagaccount dat is gekoppeld aan uw AMS-account). 

Het telemetriesysteem beheert geen gegevensbewaring. U de oude telemetriegegevens verwijderen door de opslagtabellen te verwijderen.

In dit onderwerp wordt besproken hoe u de AMS-telemetrie configureren en gebruiken.

## <a name="configuring-telemetry"></a>Telemetrie configureren

U telemetrie configureren op een granulariteit op componentniveau. Er zijn twee detailniveaus "Normaal" en "Verbose". Momenteel geven beide niveaus dezelfde informatie. Het wordt aanbevolen om "Normaal te gebruiken. 

In de volgende onderwerpen wordt uitgelegd hoe u telemetrie inschakelt:

[Telemetrie inschakelen met .NET](media-services-dotnet-telemetry.md) 

[Telemetrie inschakelen met REST](media-services-rest-telemetry.md)

## <a name="consuming-telemetry-information"></a>Telemetrie-informatie consumeren

Telemetrie wordt geschreven naar een Azure Storage Table in het opslagaccount dat u hebt opgegeven toen u telemetrie configureerde voor het Media Services-account. In deze sectie worden de opslagtabellen voor de statistieken beschreven.

U telemetriegegevens op een van de volgende manieren verwerken:

- Lees rechtstreeks gegevens uit Azure Table Storage (bijvoorbeeld met behulp van de Storage SDK). Zie de **informatie over het consumeren van telemetrie** in [dit](https://msdn.microsoft.com/library/mt742089.aspx) onderwerp voor de beschrijving van de opslagtabellen voor telemetrie.

of

- Gebruik de ondersteuning in de Media Services .NET SDK voor het lezen van opslaggegevens, zoals beschreven in [dit](media-services-dotnet-telemetry.md) onderwerp. 


Het onderstaande telemetrieschema is ontworpen om goede prestaties te leveren binnen de grenzen van Azure Table Storage:

- Gegevens worden verdeeld door account-ID en service-ID, zodat telemetrie van elke service onafhankelijk kan worden opgevraagd.
- Partities bevatten de datum om een redelijke bovengrens te geven aan de partitiegrootte.
- Rijtoetsen zijn omgekeerd in volgorde om de meest recente telemetrie-items voor een bepaalde service op te vragen.

Hierdoor moeten veel van de algemene query's efficiënt zijn:

- Parallel, onafhankelijk downloaden van gegevens voor afzonderlijke diensten.
- Alle gegevens voor een bepaalde service ophalen in een datumbereik.
- De meest recente gegevens voor een service ophalen.

### <a name="telemetry-table-storage-output-schema"></a>Uitvoerschema voor opslag van telemetrietabel

Telemetriegegevens worden in totaal opgeslagen in één tabel, 'TelemetryMetrics20160321' waarbij '20160321' datum is van de gemaakte tabel. Telemetriesysteem maakt een aparte tabel voor elke nieuwe dag op basis van 00:00 UTC. De tabel wordt gebruikt om terugkerende waarden op te slaan, zoals het innemen van bitrate binnen een bepaald tijdsvenster, verzonden bytes, enz. 

Eigenschap|Waarde|Voorbeelden/notities
---|---|---
PartitionKey|{account-ID}_{entiteits-ID}|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66<br/<br/>De account-id is opgenomen in de partitiesleutel om werkstromen te vereenvoudigen waarin meerdere Media Services-accounts naar hetzelfde opslagaccount worden geschreven.
RowKey|{seconds to midnight}_{random value} {seconds to midnight}_{random value} {seconds to midnight}_{random value} {seconds|01688_00199<br/><br/>De rijsleutel begint met het aantal seconden tot middernacht om topn-stijlquery's in een partitie toe te staan. Zie voor meer informatie [dit](../../cosmos-db/table-storage-design-guide.md#log-tail-pattern) artikel. 
Tijdstempel|Datum/tijd|Automatische tijdstempel uit de Azure-tabel 2016-09-09T22:43:42.241Z
Type|Het type entiteit dat telemetriegegevens verstrekt|Kanaal/StreamingEndpoint/Archief<br/><br/>Gebeurtenistype is slechts een tekenreekswaarde.
Name|De naam van de telemetriegebeurtenis|ChannelHeartbeat/StreamingEndpointRequestLog
WaargenomenTijd|De tijd dat de telemetriegebeurtenis heeft plaatsgevonden (UTC)|2016-09-09T22:42:36.924Z<br/><br/>De waargenomen tijd wordt verstrekt door de entiteit die de telemetrie verzendt (bijvoorbeeld een kanaal). Er kunnen tijdsynchronisatieproblemen zijn tussen componenten, dus deze waarde is bij benadering
ServiceID|{service-ID}|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
Entiteitsspecifieke eigenschappen|Zoals gedefinieerd door de gebeurtenis|StreamName: stream1, Bitrate 10123, ...<br/><br/>De overige eigenschappen worden gedefinieerd voor het opgegeven gebeurtenistype. Azure Table-inhoud is belangrijke waardeparen.  (dat wil zeggen, verschillende rijen in de tabel hebben verschillende sets van eigenschappen).

### <a name="entity-specific-schema"></a>Entiteitsspecifiek schema

Er zijn drie soorten entiteitsspecifieke telemetrische gegevensdie elk met de volgende frequentie worden gepusht:

- Streaming eindpunten: Elke 30 seconden
- Live kanalen: Elke minuut
- Live archief: Elke minuut

**Streaming-eindpunt**

Eigenschap|Waarde|Voorbeelden
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Tijdstempel|Tijdstempel|Automatische tijdstempel van Azure Table 2016-09-09T22:43:42.241Z
Type|Type|StreamingEindpunt
Name|Name|StreamingEndpointRequestLog
WaargenomenTijd|WaargenomenTijd|2016-09-09T22:42:36.924Z
ServiceID|Service-id|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
HostName|Hostname van het eindpunt|builddemoserver.origin.mediaservices.windows.net
Statuscode|HTTP-status records|200
Resultaatcode|Resultaatcodedetail|S_ok
Aantal aanvragen|Totaal verzoek in de aggregatie|3
BytesSent|Geaggregeerde bytes verzonden|2987358
Serverlatentie|Gemiddelde serverlatentie (inclusief opslag)|129
E2ELatentie|Gemiddelde end-to-end latentie|250

**Live kanaal**

Eigenschap|Waarde|Voorbeelden/notities
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Tijdstempel|Tijdstempel|Automatische tijdstempel uit de Azure-tabel 2016-09-09T22:43:42.241Z
Type|Type|Kanaal
Name|Name|ChannelHeartbeat (Kanaalheartbeat)
WaargenomenTijd|WaargenomenTijd|2016-09-09T22:42:36.924Z
ServiceID|Service-id|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
TrackType TrackType|Type trackvideo/audio/tekst|video/audio
TrackName TrackName|Naam van het nummer|video/audio_1
Bitrate|Bitrate bijhouden|785000
Aangepaste kenmerken||   
InkomendeBitrate|Werkelijke inkomende bitrate|784548
Aantal overlappingen|Overlap in de inname|0
Discontinuïteitsaantal|Discontinuïteit voor track|0
LastTimestamp|Laatst ingenomen datatijdstempel|1800488800
Niet-toenemend Aantal|Aantal verwijderde fragmenten vanwege niet-toenemende tijdstempel|2
Niet-uitgelijnde sleutelframes|Of we fragment(s) (over kwaliteitsniveaus) hebben ontvangen waarbij hoofdframes niet |True
Niet-uitgelijnde presentatietijd|Of we fragment(s) (over kwaliteitsniveaus/tracks) hebben ontvangen waarbij de presentatietijd niet is uitgelijnd|True
Onverwacht bitrate|Toegegeven, als berekende/werkelijke bitrate voor audio/video track > 40.000 basispunten en inkomende Bitrate == 0 OF IncomingBitrate en actualBitrate verschillen met 50% |True
Goed|True, als <br/>overlappingTelling, <br/>DiscontinuïteitStelling, <br/>NonIncreasingCount, <br/>Niet-uitgelijnde sleutelframes, <br/>Niet-uitgelijnde Presentatietijd, <br/>Onverwacht bitrate<br/> zijn alle 0|True<br/><br/>Healthy is een samengestelde functie die vals retourneert wanneer een van de volgende voorwaarden standhoudt:<br/><br/>- Overlappingaantal > 0<br/>- Discontinuïteitstelling > 0<br/>- Niet-verhogend Aantal > 0<br/>- UnalignedKeyFrames == True<br/>- UnalignedPresentationTime == True<br/>- Onverwacht bitrate == True

**Live archief**

Eigenschap|Waarde|Voorbeelden/notities
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Tijdstempel|Tijdstempel|Automatische tijdstempel uit de Azure-tabel 2016-09-09T22:43:42.241Z
Type|Type|Archiveren
Name|Name|ArchiefHeartbeat
WaargenomenTijd|WaargenomenTijd|2016-09-09T22:42:36.924Z
ServiceID|Service-id|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
ManifestNaam|Programma-url|asset-eb149703-ed0a-483c-91c4-e4066e72cce3/a0a5cfbf-71ec-4bd2-8c01-a92a2b38c9ba.ism
TrackName TrackName|Naam van het nummer|audio_1
TrackType TrackType|Type van het spoor|Audio/video
Aangepast kenmerk|Hex-tekenreeks die onderscheid maakt tussen verschillende sporen met dezelfde naam en bitrate (multi-camerahoek)|
Bitrate|Bitrate bijhouden|785000
Goed|True, als FragmentDiscardedCount == 0 && ArchiveAcquisitionError == False|True (deze twee waarden zijn niet aanwezig in de statistiek, maar ze zijn aanwezig in de brongebeurtenis)<br/><br/>Healthy is een samengestelde functie die vals retourneert wanneer een van de volgende voorwaarden standhoudt:<br/><br/>- FragmentDiscardedCount > 0<br/>- ArchiveAcquisitionError == True

## <a name="general-qa"></a>Algemene Q&A

### <a name="how-to-consume-metrics-data"></a>Hoe gebruik je metrische gegevens?

Metrische gegevens worden opgeslagen als een reeks Azure-tabellen in het opslagaccount van de klant. Deze gegevens kunnen worden verbruikt met behulp van de volgende tools:

- AMS SDK
- Microsoft Azure Storage Explorer (ondersteunt exporteren naar door komma's gescheiden waardenotatie en verwerkt in Excel)
- REST API

### <a name="how-to-find-average-bandwidth-consumption"></a>Hoe vind je het gemiddelde bandbreedteverbruik?

Het gemiddelde bandbreedteverbruik is het gemiddelde van BytesSent over een tijdsspanne.

### <a name="how-to-define-streaming-unit-count"></a>Hoe het aantal streaming-eenheden te definiëren?

Het aantal streaming-eenheden kan worden gedefinieerd als de piekdoorvoer van de streamingeindpunten van de service, gedeeld door de piekdoorvoer van één streamingeindpunt. De piek bruikbare doorvoer van een streaming eindpunt is 160 Mbps.
Stel dat de piekdoorvoer van de service van een klant 40 MBps is (de maximale waarde van BytesSent over een tijdspanne). Vervolgens is het aantal streaming-eenheden gelijk aan (40 MBps)*(8 bits/byte)/(160 Mbps) = 2 streaming-eenheden.

### <a name="how-to-find-average-requestssecond"></a>Hoe vind je gemiddelde aanvragen/seconde?

Als u het gemiddelde aantal aanvragen per seconde wilt vinden, berekent u het gemiddelde aantal aanvragen (RequestCount) over een tijdsspanne.

### <a name="how-to-define-channel-health"></a>Hoe kanaalstatus definiëren?

Kanaalstatus kan worden gedefinieerd als een samengestelde Booleaanse functie, zodat deze onjuist is wanneer een van de volgende voorwaarden standhoudt:

- Aantal overlappingen > 0
- DiscontinuïteitSaantal > 0
- Niet-toenemend Aantal > 0
- Onuitgelijnde keyframes == True 
- Niet-uitgelijnde presentatietijd == True 
- Onverwachtbitrate == True


### <a name="how-to-detect-discontinuities"></a>Hoe discontinuïteiten detecteren?

Als u discontinuïteiten wilt detecteren, zoekt u alle kanaalgegevensitems waar Discontinuïteitstelling > 0. De overeenkomstige ObservedTime-tijdstempel geeft de tijden aan waarop de discontinuïteiten hebben plaatsgevonden.

### <a name="how-to-detect-timestamp-overlaps"></a>Hoe u overlappingen van tijdstempels detecteren?

Als u overlappingen van tijdstempels wilt detecteren, zoekt u alle kanaalgegevensitems waar OverlapCount > 0. De bijbehorende ObservedTime-tijdstempel geeft de tijden aan waarop de tijdstempel overlapt.

### <a name="how-to-find-streaming-request-failures-and-reasons"></a>Hoe vind je fouten en redenen van streamingaanvragen?

Als u fouten en redenen voor streamingaanvragen wilt vinden, zoekt u alle streaming eindpuntgegevens waarin ResultCode niet gelijk is aan S_OK. Het veld bijbehorende StatusCode geeft de reden voor de aanvraagfout aan.

### <a name="how-to-consume-data-with-external-tools"></a>Hoe gegevens te consumeren met externe tools?

Telemetrische gegevens kunnen worden verwerkt en gevisualiseerd met de volgende tools:

- Power BI
- Application Insights
- Azure Monitor (voorheen Schoenendoos)
- AMS Live Dashboard
- Azure Portal (in afwachting van release)

### <a name="how-to-manage-data-retention"></a>Hoe beheer je het bewaren van gegevens?

Het telemetriesysteem biedt geen beheer van gegevensbewaring of automatische verwijdering van oude records. U moet dus oude records handmatig beheren en verwijderen uit de opslagtabel. U verwijzen naar opslag SDK voor hoe dit te doen.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
