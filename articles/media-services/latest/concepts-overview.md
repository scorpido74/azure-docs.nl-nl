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
ms.date: 09/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 47c7e35f71fd33cc53d498867ef015364252d5ea
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910296"
---
# <a name="media-services-concepts"></a>Media Services concepten

In dit onderwerp vindt u een kort overzicht van Azure Media Services terminologie en concepten. Het artikel bevat ook koppelingen naar artikelen met uitgebreide uitleg over Media Services v3-concepten en-functionaliteit. 

Bekijk de basisconcepten die worden beschreven in de volgende onderwerpen voordat u start met de ontwikkeling.

> [!NOTE]
> U kunt momenteel geen gebruik maken van de Azure-portal om v3-resources te beheren. Gebruik de [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref) of een van de ondersteunde [SDK's](media-services-apis-overview.md#sdks).

## <a name="terminology"></a>Terminologie

In deze sectie ziet u hoe sommige algemene branche termen worden toegewezen aan de Media Services v3 API.

### <a name="live-event"></a>Livegebeurtenis

Een **live-gebeurtenis** vertegenwoordigt een pijp lijn voor opname, code ring (optioneel) en het verpakken van live streams van video, audio en realtime meta gegevens.

Voor klanten die migreren van Media Services v2 Api's, vervangt de **live-gebeurtenis** de **kanaal** entiteit in v2. Zie voor meer informatie [migreren van v2 naar v3](migrate-from-v2-to-v3.md).

### <a name="streaming-endpoint-packaging-and-origin"></a>Streaming-eind punt (verpakking en oorsprong)

Een **streaming-eind punt** vertegenwoordigt een dynamische (just-in-time) verpakkings-en bron service die uw Live-en on-demand-inhoud rechtstreeks aan een client speler kan leveren, met behulp van een van de algemene protocollen voor streaming media (HLS of Dash). Daarnaast biedt het **streaming-eind punt** dynamische (just-in-time) versleuteling voor de toonaangevende DRMs.

In de media streaming-industrie wordt deze service meestal een **packager** of **Origin**genoemd.  Andere veelvoorkomende termen in de branche voor deze mogelijkheid zijn JITP (just-in-time-packager) of JITE (just-in-time-Encryption). 
 
## <a name="cloud-upload-and-storage"></a>Uploaden naar en opslaan in de cloud

Als u media-inhoud in azure wilt beheren, versleutelen, coderen, analyseren en streamen, moet u een Media Services account maken en uw digitale bestanden uploaden naar **assets**.

- [Uploaden naar en opslaan in de cloud](storage-account-concept.md)
- [Activa concept](assets-concept.md)

## <a name="encoding"></a>Encoding

Wanneer u uw digitale media bestanden van hoge kwaliteit uploadt naar assets, kunt u ze coderen in indelingen die kunnen worden afgespeeld op een groot aantal verschillende browsers en apparaten. 

Als u wilt coderen met Media Services v3, moet u **trans formaties** en **taken**maken.

![Transformaties](./media/encoding/transforms-jobs.png)

- [Trans formaties en taken](transforms-jobs-concept.md)
- [Code ring met Media Services](encoding-concept.md)

## <a name="media-analytics"></a>Media Analytics

Als u uw video-en audio bestanden wilt analyseren, moet u ook **trans formaties** en **taken**maken.

- [Video-en audio bestanden analyseren](analyzing-video-audio-files-concept.md)

## <a name="packaging-delivery-protection"></a>Verpakking, levering, beveiliging

Als uw inhoud eenmaal is gecodeerd, kunt u gebruikmaken van **dynamische pakketten**. In Media Services is een **streaming-eind punt**/origin de dynamische verpakkings service die wordt gebruikt voor het leveren van media-inhoud aan client spelers. Als u Video's in het uitvoer bare activum beschikbaar wilt maken voor het afspelen van clients, moet u een **streaming-Locator** maken en vervolgens streaming-url's bouwen. 

Bij het maken van de **streaming-Locator**, naast de naam van het activum, moet u het **streaming-beleid**opgeven. Met **stroomsgewijze beleids regels** kunt u streaming-protocollen en versleutelings opties definiëren (indien van toepassing) voor uw **streaming-Locators**.

Dynamische pakketten worden gebruikt, ongeacht of u uw inhoud live of on-demand streamt. In het volgende diagram ziet u de on-demand streaming met dynamische verpakkings werk stroom.

![Dynamische verpakking](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

Met Media Services kunt u uw Live en on-demand inhoud dynamisch versleutelen met Advanced Encryption Standard (AES-128) of/en een van de drie belangrijkste Digital Rights Management (DRM)-systemen: Micro soft PlayReady, Google Widevine en Apple FairPlay. Media Services biedt ook een service voor het leveren van AES-sleutels en DRM (PlayReady, Widevine en FairPlay) licenties voor geautoriseerde clients.

Als u versleutelings opties voor uw stroom opgeeft, maakt u het **beleid voor inhouds sleutels** en koppelt u dit aan uw **streaming-Locator**. Met het **beleid voor inhouds sleutels** kunt u configureren hoe de inhouds sleutel wordt bezorgd bij de eind clients.

De volgende afbeelding ziet u de Media Services content protection-werkstroom: 

![Inhoud beveiligen](./media/content-protection/content-protection.svg)

&#42;dynamische versleuteling ondersteunt AES-128 ' Clear key ', CBCS en CENC. 

U kunt Media Services **dynamische manifesten** gebruiken om alleen een specifieke vertoning of subfragmenten van uw video te streamen. In het volgende voor beeld werd een encoder gebruikt voor het coderen van een mezzanine-asset in zeven ISO Mp4's-video weergaven (van 180p naar 1080p). De versleutelde Asset kan dynamisch worden verpakt in een van de volgende streaming-protocollen: HLS, MPEG DASH en Smooth.  Boven aan het diagram wordt het HLS-manifest voor het activum zonder filters weer gegeven (dit bevat alle zeven weer gaven).  In de linkerbenedenhoek wordt het HLS-manifest weer gegeven waarop een filter met de naam ' Ott ' is toegepast. Het filter ' Ott ' geeft aan dat alle bitsnelheden onder 1 Mbps moeten worden verwijderd, wat heeft geleid tot de laagste twee kwaliteits niveaus die in het antwoord worden verwijderd. In de rechter benedenhoek wordt het HLS-manifest weer gegeven waarop een filter met de naam ' Mobile ' is toegepast. Met het filter ' Mobiel ' geeft u de weer gaven van de oplossing groter dan 720p, waardoor de twee 1080p-weer gaven worden verwijderd.

![Vertoning filteren](./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png)

- [Dynamische pakketten](dynamic-packaging-overview.md)
- [Streaming-eindpunten](streaming-endpoint-concept.md)
- [Streaming-locators](streaming-locators-concept.md)
- [Beleid voor streaming](streaming-policy-concept.md)
- [Beleid voor inhoudssleutels](content-key-policy-concept.md)
- [Inhouds beveiliging](content-protection-overview.md)
- [Dynamische manifesten](filters-dynamic-manifest-overview.md)
- [Filters](filters-concept.md)

## <a name="live-streaming"></a>Live streamen

Met Azure Media Services kunt u live gebeurtenissen aan uw klanten leveren via de Azure-Cloud. **Livegebeurtenissen** zijn verantwoordelijk voor het opnemen en verwerken van de live videofeeds. Wanneer u een **live gebeurtenis**maakt, wordt er een invoer eindpunt gemaakt dat u kunt gebruiken om een live signaal van een extern coderings programma te verzenden. Zodra u de stroom naar de **live gebeurtenis**hebt gestroomd, kunt u de streaming-gebeurtenis starten door een **Asset**, **Live output**en **streaming-Locator**te maken. **Live output** archiveert de stroom naar de **Asset** en maakt deze beschikbaar voor gebruikers via het **streaming-eind punt**. Een **live gebeurtenis** kan een van de volgende twee typen zijn: **Pass-Through** en **Live encoding**.

In de volgende afbeelding ziet u de werk stroom Pass-Through type:

![passthrough](./media/live-streaming/pass-through.svg)

- [Overzicht van live streamen](live-streaming-overview.md)
- [Livegebeurtenissen en live-uitvoer](live-events-outputs-concept.md)

## <a name="monitoring"></a>Bewaking

### <a name="event-grid"></a>Event Grid

Als u de voortgang van de taak wilt zien, gebruikt u **Event grid**. Media Services worden ook de live-gebeurtenis typen meeverzonden. Met Event Grid kunnen uw apps luisteren naar en reageren op gebeurtenissen uit vrijwel alle Azure-services, evenals aangepaste bronnen. 

- [Event Grid gebeurtenissen verwerken](reacting-to-media-services-events.md)
- [Schema's](media-services-event-schemas.md)

### <a name="azure-monitor"></a>Azure Monitor

Bewaak metrische gegevens en Diagnostische logboeken die u helpen inzicht te krijgen in de prestaties van uw toepassingen met Azure Monitor.

- [Metrische gegevens en Diagnostische logboeken](media-services-metrics-diagnostic-logs.md)
- [Schema's voor Diagnostische logboeken](media-services-diagnostic-logs-schema.md)

## <a name="player-clients"></a>Afspeel-clients

U kunt Azure Media Player gebruiken om media-inhoud af te spelen die door Media Services wordt gestreamd op een groot aantal verschillende browsers en apparaten. Azure Media Player maakt gebruik van industrie standaarden, zoals HTML5, media source Extensions (MSE) en versleutelde Media-extensies (EME) om een geavanceerde adaptieve streaming-ervaring te bieden. 

- [Overzicht van Azure Media Player](use-azure-media-player.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Vragen stellen, feedback geven, updates ophalen

Bekijk het [Azure Media Services Community](media-services-community.md) -artikel voor verschillende manieren om vragen te stellen, feedback te geven en updates te ontvangen over Media Services.

## <a name="next-steps"></a>Volgende stappen

* [Video van extern bestand en stream coderen – REST](stream-files-tutorial-with-rest.md)
* [Geüpload bestand en stream video coderen-.NET](stream-files-tutorial-with-api.md)
* [Live-.NET streamen](stream-live-tutorial-with-api.md)
* [Uw video analyseren-.NET](analyze-videos-tutorial-with-api.md)
* [AES-128 dynamische versleuteling-.NET](protect-with-aes128.md)
* [Dynamisch versleutelen met multi-DRM-.NET](protect-with-drm.md) 
