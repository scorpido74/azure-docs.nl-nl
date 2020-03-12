---
title: Terminologie en concepten Azure Media Services-Azure | Microsoft Docs
description: In dit onderwerp vindt u een kort overzicht van Azure Media Services terminologie en concepten, en vindt u koppelingen naar meer informatie.
services: media-services
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
ms.openlocfilehash: bdc0af39e3f3a06da6c06ddc384efe44dd678ea3
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79087923"
---
# <a name="media-services-concepts"></a>Media Services concepten

In dit onderwerp vindt u een kort overzicht van Azure Media Services terminologie en concepten. Het artikel bevat ook koppelingen naar artikelen met uitgebreide uitleg over Media Services v3-concepten en-functionaliteit. 

Bekijk de basisconcepten die worden beschreven in de volgende onderwerpen voordat u start met de ontwikkeling.

> [!NOTE]
> Op dit moment kunt u de [Azure Portal](https://portal.azure.com/) gebruiken om te: Media Services v3 [Live Events](live-events-outputs-concept.md)beheren, v3- [assets](assets-concept.md)weer geven (niet beheren), [informatie over het openen van api's ophalen](access-api-portal.md). Gebruik de [rest API](https://aka.ms/ams-v3-rest-ref), [cli](https://aka.ms/ams-v3-cli-ref)of een van de ondersteunde [sdk's](media-services-apis-overview.md#sdks)voor alle andere beheer taken (bijvoorbeeld [trans formaties en taken](transforms-jobs-concept.md) en [inhouds beveiliging](content-protection-overview.md)).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="media-services-v3-terminology"></a>Media Services v3-terminologie

|Termijn|Beschrijving|
|---|---|
|Live-gebeurtenis|Een **live-gebeurtenis** vertegenwoordigt een pijp lijn voor opname, code ring (optioneel) en het verpakken van live streams van video, audio en realtime meta gegevens.<br/><br/>Voor klanten die migreren van Media Services v2 Api's, vervangt de **live-gebeurtenis** de **kanaal** entiteit in v2. Zie voor meer informatie [migreren van v2 naar v3](migrate-from-v2-to-v3.md).|
|Streaming-eind punt/verpakking/oorsprong|Een **streaming-eind punt** vertegenwoordigt een dynamische (just-in-time) verpakkings-en bron service die uw Live-en on-demand-inhoud rechtstreeks aan een client speler kan leveren, met behulp van een van de algemene protocollen voor streaming media (HLS of Dash). Daarnaast biedt het **streaming-eind punt** dynamische (just-in-time) versleuteling voor de toonaangevende DRMs.<br/><br/>In de media streaming-industrie wordt deze service meestal een **packager** of **Origin**genoemd.  Andere veelvoorkomende termen in de branche voor deze mogelijkheid zijn JITP (just-in-time-packager) of JITE (just-in-time-Encryption). 

## <a name="media-services-v3-concepts"></a>Media Services v3-concepten

|Concepten|Beschrijving|Koppelingen|
|---|---|---|
|Activa en uploaden van inhoud|Als u media-inhoud in azure wilt beheren, versleutelen, coderen, analyseren en streamen, moet u een Media Services account maken en uw digitale bestanden uploaden naar **assets**.|[Uploaden naar en opslaan in de cloud](storage-account-concept.md)<br/><br/>[Activa concept](assets-concept.md)|
|Inhoud versleutelen|Wanneer u uw digitale media bestanden van hoge kwaliteit uploadt naar assets, kunt u ze coderen in indelingen die kunnen worden afgespeeld op een groot aantal verschillende browsers en apparaten. <br/>Als u wilt coderen met Media Services v3, moet u **trans formaties** en **taken**maken.|[Trans formaties en taken](transforms-jobs-concept.md)<br/><br/>[Code ring met Media Services](encoding-concept.md)|
|Inhoud wordt geanalyseerd (Video Indexer)|Met Media Services v3 kunt u inzichten uit uw video-en audio bestanden ophalen met behulp van Media Services v3-voor waarden. Als u uw inhoud wilt analyseren met Media Services v3-voor instellingen, moet u **trans formaties** en **taken**maken.<br/><br/>Als u meer gedetailleerde inzichten wilt, gebruikt u [video indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) rechtstreeks.|[Video-en audio bestanden analyseren](analyzing-video-audio-files-concept.md)|
|Verpakking en levering|Als uw inhoud eenmaal is gecodeerd, kunt u gebruikmaken van **dynamische pakketten**. In Media Services is een **streaming-eind punt** de dynamische verpakkings service die wordt gebruikt voor het leveren van media-inhoud aan client spelers. Als u Video's in het uitvoer bare activum beschikbaar wilt maken voor het afspelen van clients, moet u een **streaming-Locator** maken en vervolgens streaming-url's bouwen. <br/><br/>Bij het maken van de **streaming-Locator**, naast de naam van het activum, moet u het **streaming-beleid**opgeven. Met **stroomsgewijze beleids regels** kunt u streaming-protocollen en versleutelings opties definiëren (indien van toepassing) voor uw **streaming-Locators**. Dynamische pakketten worden gebruikt, ongeacht of u uw inhoud live of on-demand streamt. <br/><br/>U kunt Media Services **dynamische manifesten** gebruiken om alleen een specifieke vertoning of subfragmenten van uw video te streamen.|[Dynamische pakketten](dynamic-packaging-overview.md)<br/><br/>[Streaming-eindpunten](streaming-endpoint-concept.md)<br/><br/>[Streaming-locators](streaming-locators-concept.md)<br/><br/>[Beleid voor streaming](streaming-policy-concept.md)<br/><br/>[Dynamische manifesten](filters-dynamic-manifest-overview.md)<br/><br/>[Filters](filters-concept.md)|
|Inhoudsbeveiliging|Met Media Services kunt u uw Live en on-demand inhoud dynamisch versleutelen met Advanced Encryption Standard (AES-128) of/en een van de drie belangrijkste Digital Rights Management (DRM)-systemen: micro soft PlayReady, Google Widevine en Apple FairPlay. Media Services biedt ook een service voor het leveren van AES-sleutels en DRM (PlayReady, Widevine en FairPlay) licenties voor geautoriseerde clients. <br/><br/>Als u versleutelings opties voor uw stroom opgeeft, maakt u het **beleid voor inhouds sleutels** en koppelt u dit aan uw **streaming-Locator**. Met het **beleid voor inhouds sleutels** kunt u configureren hoe de inhouds sleutel wordt bezorgd bij de eind clients.<br/><br/> Probeer het beleid opnieuw te gebruiken wanneer dezelfde opties nodig zijn.| [Beleid voor inhoudssleutels](content-key-policy-concept.md)<br/><br/>[Inhouds beveiliging](content-protection-overview.md)|
|Live streamen|Met Media Services kunt u live gebeurtenissen aan uw klanten leveren via de Azure-Cloud. **Livegebeurtenissen** zijn verantwoordelijk voor het opnemen en verwerken van de live videofeeds. Wanneer u een **live gebeurtenis**maakt, wordt er een invoer eindpunt gemaakt dat u kunt gebruiken om een live signaal van een extern coderings programma te verzenden. Zodra u de stroom naar de **live gebeurtenis**hebt gestroomd, kunt u de streaming-gebeurtenis starten door een **Asset**, **Live output**en **streaming-Locator**te maken. **Live output** archiveert de stroom naar de **Asset** en maakt deze beschikbaar voor gebruikers via het **streaming-eind punt**. Een live-gebeurtenis kan worden ingesteld op een *Pass-Through* -(een on-premises Live coderings programma verzendt een multi-bitrate stroom) of *Live encoding* (een on-premises Live Encoder verzendt een stream met één bitsnelheid). |[Overzicht van live streamen](live-streaming-overview.md)<br/><br/>[Livegebeurtenissen en live-uitvoer](live-events-outputs-concept.md)|
|Controleren met Event Grid|Als u de voortgang van de taak wilt zien, gebruikt u **Event grid**. Media Services worden ook de live-gebeurtenis typen meeverzonden. Met Event Grid kunnen uw apps luisteren naar en reageren op gebeurtenissen uit vrijwel alle Azure-services, evenals aangepaste bronnen. |[Event Grid gebeurtenissen verwerken](reacting-to-media-services-events.md)<br/><br/>[Schema's](media-services-event-schemas.md)|
|Controleren met Azure Monitor|Bewaak metrische gegevens en Diagnostische logboeken die u helpen inzicht te krijgen in de prestaties van uw toepassingen met Azure Monitor.|[Metrische gegevens en Diagnostische logboeken](media-services-metrics-diagnostic-logs.md)<br/><br/>[Schema's voor Diagnostische logboeken](media-services-diagnostic-logs-schema.md)|
|Afspeel-clients|U kunt Azure Media Player gebruiken om media-inhoud af te spelen die door Media Services wordt gestreamd op een groot aantal verschillende browsers en apparaten. Azure Media Player maakt gebruik van industrie standaarden, zoals HTML5, media source Extensions (MSE) en versleutelde Media-extensies (EME) om een geavanceerde adaptieve streaming-ervaring te bieden. |[Overzicht van Azure Media Player](use-azure-media-player.md)|

## <a name="ask-questions-give-feedback-get-updates"></a>Vragen stellen, feedback geven, updates ophalen

Bekijk het [Azure Media Services Community](media-services-community.md) -artikel voor verschillende manieren om vragen te stellen, feedback te geven en updates te ontvangen over Media Services.

## <a name="next-steps"></a>Volgende stappen

* [Video van extern bestand en stream coderen – REST](stream-files-tutorial-with-rest.md)
* [Geüpload bestand en stream video coderen-.NET](stream-files-tutorial-with-api.md)
* [Live-.NET streamen](stream-live-tutorial-with-api.md)
* [Uw video analyseren-.NET](analyze-videos-tutorial-with-api.md)
* [AES-128 dynamische versleuteling-.NET](protect-with-aes128.md)
* [Dynamisch versleutelen met multi-DRM-.NET](protect-with-drm.md) 
