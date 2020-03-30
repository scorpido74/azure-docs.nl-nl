---
title: Media Services terminologie en concepten
titleSuffix: Azure Media Services
description: Meer informatie over terminologie en concepten voor Azure Media Services.
services: media-servicesgit
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/18/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 02d0897774261a25a2fccb70a31d0f264c458740
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500083"
---
# <a name="media-services-terminology-and-concepts"></a>Media Services terminologie en concepten

Dit onderwerp geeft een kort overzicht van de terminologie en concepten van Azure Media Services. Het artikel biedt ook links naar artikelen met een diepgaande uitleg van Media Services v3 concepten en functionaliteit.

De fundamentele concepten die in deze onderwerpen worden beschreven, moeten worden herzien voordat met de ontwikkeling wordt begonnen.

> [!NOTE]
> Momenteel u de [Azure-portal](https://portal.azure.com/) gebruiken om: Media Services v3 [Live Events](live-events-outputs-concept.md)beheren, [v3-elementen](assets-concept.md)weergeven (niet beheren) en [informatie krijgen over toegang tot API's](access-api-portal.md).
> Voor alle andere beheertaken (bijvoorbeeld [Transformaties en Taken](transforms-jobs-concept.md) en [Contentbescherming),](content-protection-overview.md)gebruikt u de [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref)of een van de ondersteunde [SDK's](media-services-apis-overview.md#sdks).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="media-services-v3-terminology"></a>Media Services v3 terminologie

|Termijn|Beschrijving|
|---|---|
|Live-evenement|Een **Live Event** vertegenwoordigt een pijplijn voor het innemen, transcoderen (optioneel) en het verpakken van live streams van video, audio en real-time metadata.<br/><br/>Voor klanten die migreren vanuit Media Services v2 API's, vervangt de **Live Event** de **kanaalentiteit** in v2. Zie [Migreren van v2 naar v3](migrate-from-v2-to-v3.md)voor meer informatie.|
|Streaming Endpoint/Packaging/Origin|Een **Streaming Endpoint** vertegenwoordigt een dynamische (just-in-time) verpakkings- en herkomstservice die uw live en on-demand content rechtstreeks aan een clientplayer-toepassing kan leveren. Het maakt gebruik van een van de gemeenschappelijke streaming media protocollen (HLS of DASH). Daarnaast biedt het **Streaming Endpoint** dynamische (just-in-time) encryptie aan toonaangevende digital rights management systemen (DRM's).<br/><br/>In de media streaming industrie, wordt deze dienst algemeen aangeduid als een **Packager** of **Origin**.  Andere veel voorkomende termen in de industrie voor deze mogelijkheid zijn JITP (just-in-time-verpakker) of JITE (just-in-time-encryptie).

## <a name="media-services-v3-concepts"></a>Media Services v3 concepten

|Concepten|Beschrijving|Koppelingen|
|---|---|---|
|Assets en het uploaden van inhoud|Als u wilt beginnen met het beheren, versleutelen, coderen, analyseren en streamen van media-inhoud in Azure, moet u een Media Services-account maken en uw digitale bestanden uploaden naar **Assets.**|[Uploaden naar en opslaan in de cloud](storage-account-concept.md)<br/><br/>[Het concept van activa](assets-concept.md)|
|Inhoud versleutelen|Zodra u uw digitale mediabestanden van hoge kwaliteit uploadt naar Assets, u ze coderen in indelingen die kunnen worden afgespeeld op een breed scala aan browsers en apparaten. <br/><br/>Als u wilt coderen met Media Services v3, moet u **transformaties** en **taken maken.**|[Transformaties en taken](transforms-jobs-concept.md)<br/><br/>[Coderen met Media Services](encoding-concept.md)|
|Inhoud wordt geanalyseerd (Video Indexer)|Met Media Services v3 u inzichten uit uw video- en audiobestanden halen met behulp van Media Services v3-voorinstellingen. Als u uw inhoud wilt analyseren met V3-voorinstellingen van Media Services, moet u **transformaties** en **taken maken.**<br/><br/>Als u meer gedetailleerde inzichten wilt, gebruikt u [Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) rechtstreeks.|[Video- en audiobestanden analyseren](analyzing-video-audio-files-concept.md)|
|Verpakking en levering|Zodra uw inhoud is gecodeerd, u profiteren van **Dynamic Packaging.** In Media Services is een **Streaming Endpoint** de dynamische verpakkingsservice die wordt gebruikt om media-inhoud te leveren aan spelers van klanten. Als u video's in het uitvoeritem beschikbaar wilt maken voor clients voor afspelen, moet u een **streaminglocator** maken en vervolgens streaming-URL's maken. <br/><br/>Bij het maken van de **Streaming Locator**moet u naast de naam van het asset **ook streamingbeleid**opgeven. **Met streamingbeleid** u streamingprotocollen en coderingsopties definiëren (indien van toepassing) voor uw **streaminglocators.** Dynamic Packaging wordt gebruikt, of u uw inhoud nu live of on-demand streamt. <br/><br/>U Media Services **Dynamic Manifests** gebruiken om alleen een specifieke weergave of subclips van uw video te streamen.|[Dynamische verpakking](dynamic-packaging-overview.md)<br/><br/>[Streaming eindpunten](streaming-endpoint-concept.md)<br/><br/>[Streaming-locators](streaming-locators-concept.md)<br/><br/>[Beleid voor streaming](streaming-policy-concept.md)<br/><br/>[Dynamische manifesten](filters-dynamic-manifest-overview.md)<br/><br/>[Filters](filters-concept.md)|
|Inhoudsbeveiliging|Met Media Services u uw live en on-demand content dynamisch versleuteld leveren met Advanced Encryption Standard (AES-128) of/en een van de drie grote DRM-systemen: Microsoft PlayReady, Google Widevine en Apple FairPlay. Media Services biedt ook een service voor het leveren van AES-sleutels en DRM-licenties (PlayReady, Widevine en FairPlay) aan geautoriseerde clients. <br/><br/>Als u versleutelingsopties op uw stream opgeeft, maakt u het **beleid voor inhoudssleutel** en koppelt u deze aan uw **streaminglocator.** **Met het beleid voor inhoudssleutel** u configureren hoe de inhoudssleutel aan eindclients wordt geleverd.<br/><br/> Probeer beleid opnieuw te gebruiken wanneer dezelfde opties nodig zijn.| [Beleid voor inhoudssleutels](content-key-policy-concept.md)<br/><br/>[Inhoudsbescherming](content-protection-overview.md)|
|Live streamen|Met Media Services u live-evenementen leveren aan uw klanten in de Azure-cloud. **Livegebeurtenissen** zijn verantwoordelijk voor het opnemen en verwerken van de live videofeeds. Wanneer u een **Live Event maakt,** wordt een invoereindpunt gemaakt dat u gebruiken om een live signaal te verzenden vanaf een externe encoder. Zodra je de stream naar het **Live-evenement**hebt gestreamd, kun je de streaming-gebeurtenis beginnen door een **Asset,** **Live Output**en **Streaming Locator**te maken. **Live Output** archiveert de stream in het **actief** en maakt deze beschikbaar voor kijkers via het **streaming eindpunt.** Een live-evenement kan worden ingesteld op een *pass-through* (een on-premises live encoder stuurt een meervoudige bitrate stream) of *live codering* (een on-premises live encoder stuurt een enkele bitrate stream). |[Overzicht van live streaming](live-streaming-overview.md)<br/><br/>[Livegebeurtenissen en live-uitvoer](live-events-outputs-concept.md)|
|Monitoring met gebeurtenisraster|Als u de voortgang van de taak wilt zien, gebruikt u **Gebeurtenisraster**. Media Services zendt ook de typen live-evenementen uit. Met Event Grid kunnen uw apps luisteren naar en reageren op gebeurtenissen uit vrijwel alle Azure-services, evenals aangepaste bronnen. |[Event Grid-gebeurtenissen verwerken](reacting-to-media-services-events.md)<br/><br/>[Schema 's](media-services-event-schemas.md)|
|Controle met Azure-monitor|Monitor statistieken en diagnostische logboeken die u helpen te begrijpen hoe uw apps presteren met Azure Monitor.|[Metrische gegevens en diagnostische logboeken](media-services-metrics-diagnostic-logs.md)<br/><br/>[Schema's van diagnostische logboeken](media-services-diagnostic-logs-schema.md)|
|Afspeel-clients|U Azure Media Player gebruiken om media-inhoud af te spelen die door Media Services wordt gestreamd op een breed scala aan browsers en apparaten. Azure Media Player maakt gebruik van industriestandaarden, zoals HTML5, Media Source Extensions (MSE) en Encrypted Media Extensions (EME) om een verrijkte adaptieve streaming-ervaring te bieden. |[Overzicht van Azure Media Player](use-azure-media-player.md)|

## <a name="ask-questions-give-feedback-get-updates"></a>Stel vragen, geef feedback, ontvang updates

Bekijk het communityartikel [van Azure Media Services](media-services-community.md) om verschillende manieren te zien waarop u vragen stellen, feedback geven en updates ontvangen over Media Services.

## <a name="next-steps"></a>Volgende stappen

* [Externe bestanden coderen en video streamen - REST](stream-files-tutorial-with-rest.md)
* [Externe bestanden coderen en video streamen - .NET](stream-files-tutorial-with-api.md)
* [Live streamen - .NET](stream-live-tutorial-with-api.md)
* [Uw video analyseren - .NET](analyze-videos-tutorial-with-api.md)
* [Dynamische AES-128-versleuteling - .NET](protect-with-aes128.md)
* [Dynamisch versleutelen met multi-DRM - .NET](protect-with-drm.md)
