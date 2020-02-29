---
title: Microsoft Azure Media Services-scenario's en de beschikbaarheid van functies in datacenters | Microsoft Docs
description: In dit onderwerp vindt u informatie over Microsoft Azure Media Services-scenario's en over de beschikbaarheid van functies en services in datacenters.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 7b5569738721038beadc78d94c81393803b6d36a
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920583"
---
# <a name="scenarios-and-availability-of-media-services-features-across-datacenters"></a>Scenario's en de beschikbaarheid van Media Services-functies in datacenters

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Maak kennis met de nieuwste versie, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zie ook [migratie richtlijnen van v2 naar v3](../latest/migrate-from-v2-to-v3.md)

Met Microsoft Azure Media Services (AMS) kunt u veilig video- of audio-inhoud uploaden, opslaan, coderen en verpakken, zowel voor levering on-demand als levering via livestreaming aan verschillende clients (bijvoorbeeld tv, pc en mobiele apparaten).

Azure Media Services werkt vanuit diverse datacentra, op locaties overal ter wereld. Deze datacenters worden gegroepeerd in geografische regio's, waardoor u flexibiliteit heeft bij het kiezen waar u uw toepassingen ontwikkelt. U kunt zelf de [lijst met regio's en bijbehorende locaties](https://azure.microsoft.com/regions/) bekijken. 

Dit onderwerp bevat algemene scenario's voor het [live](#live_scenarios) of on-demand leveren van uw inhoud. Het onderwerp bevat ook informatie over de beschikbaarheid van mediafuncties en -services in datacenters.

## <a name="overview"></a>Overzicht

### <a name="prerequisites"></a>Vereisten

Als u Azure Media Services wilt gaan gebruiken, moet u over het volgende beschikken:

* Een Azure-account. Als u geen account hebt, kunt u binnen een paar minuten een account voor de gratis proefversie maken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com) voor meer informatie.
* Een Azure Media Services-account. Zie [Een account maken](media-services-portal-create-account.md) voor meer informatie.
* Het streaming-eindpunt van waar u inhoud wilt streamen, moet de status **Wordt uitgevoerd** hebben.

    Wanneer uw AMS-account is gemaakt, wordt er een **standaardstreaming-eindpunt** met de status **Gestopt** toegevoegd aan uw account. Als u inhoud wilt streamen en gebruik wilt maken van dynamische pakketten en dynamische versleuteling, moet het streaming-eindpunt de status **Wordt uitgevoerd** hebben.

### <a name="commonly-used-objects-when-developing-against-the-ams-odata-model"></a>Veelgebruikte objecten bij ontwikkelen op basis van het AMS OData-model

In de volgende afbeelding ziet u een aantal van de meest gebruikte objecten bij het ontwikkelen in het Media Services OData-model.

Klik op de afbeelding om deze in volledig formaat weer te geven.  

<a href="./media/media-services-overview/media-services-overview-object-model.png" target="_blank"><img src="./media/media-services-overview/media-services-overview-object-model-small.png"></a> 

U kunt [hier](https://media.windows.net/API/$metadata?api-version=2.15) het hele model bekijken.  

## <a name="protect-content-in-storage-and-deliver-streaming-media-in-the-clear-non-encrypted"></a>Inhoud in de opslag beveiligen en niet-versleutelde streamingmedia leveren

![VoD-werkstroom](./media/scenarios-and-availability/scenarios-and-availability01.png)

1. Upload een mediabestand van hoge kwaliteit naar een asset.

    Het is raadzaam de optie voor opslagversleuteling toe te passen op de uitvoerasset om de inhoud in de opslag te beveiligen.
2. Codeer de assets als een set Adaptive Bitrate MP4-bestanden.

    Het is raadzaam de optie voor opslagversleuteling toe te passen op de uitvoerasset om de opgeslagen inhoud te beveiligen.
3. Configureer het beleid voor de levering van assets (gebruikt voor dynamische pakketten).

    Als de opslag van uw asset is versleuteld, **moet** u een beleid voor assetlevering configureren.
4. Publiceer de asset door een OnDemand-locator te maken.
5. Stream de gepubliceerde inhoud.

Zie de sectie [Beschikbaarheid](#availability) voor informatie over beschikbaarheid in datacenters.

## <a name="protect-content-in-storage-deliver-dynamically-encrypted-streaming-media"></a>De inhoud in de opslag beveiligen, dynamisch versleutelde streamingmedia leveren

![Beveiligen met PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

1. Upload een mediabestand van hoge kwaliteit naar een asset. Pas de optie voor opslagversleuteling toe op de asset.
2. Codeer de assets als een set Adaptive Bitrate MP4-bestanden. Pas de optie voor opslagversleuteling toe op de uitvoerasset.
3. Maak een inhoudssleutel voor de versleuteling van de asset die tijdens het afspelen dynamisch moet worden versleuteld.
4. Configureer het autorisatiebeleid voor de inhoudssleutel.
5. Configureer het beleid voor de levering van assets (gebruikt door dynamische pakketten en dynamische versleuteling).
6. Publiceer de asset door een OnDemand-locator te maken.
7. Stream de gepubliceerde inhoud.

Zie de sectie [Beschikbaarheid](#availability) voor informatie over beschikbaarheid in datacenters.

## <a name="use-media-analytics-to-derive-actionable-insights-from-your-videos"></a>Media Analytics gebruiken om inzichten aan uw video's te ontlenen waarvoor een actie kan worden uitgevoerd

Media Analytics is een verzameling spraakonderdelen en visuele onderdelen waarmee organisaties en bedrijven gemakkelijker inzichten aan hun video's kunnen ontlenen waarvoor een actie kan worden uitgevoerd. Zie [Overzicht van Azure Media Services Analytics](media-services-analytics-overview.md) voor meer informatie.

1. Upload een mediabestand van hoge kwaliteit naar een asset.
2. Verwerk uw video's met één van de Media Analytics-services die worden beschreven in de sectie [Media Analytics-overzicht](media-services-analytics-overview.md).
3. Media Analytics-mediaprocessoren produceren MP4- of JSON-bestanden. Als een Mediaprocessor een MP4-bestand produceert, kunt u het bestand progressief downloaden. Als een mediaprocessor een JSON-bestand produceert, kunt u het bestand downloaden via Azure Blob Storage.

Zie de sectie [Beschikbaarheid](#availability) voor informatie over beschikbaarheid in datacenters.

## <a name="deliver-progressive-download"></a>Een progressieve download leveren

1. Upload een mediabestand van hoge kwaliteit naar een asset.
2. Codeer het bestand naar één MP4-bestand.
3. Publiceer de asset door een OnDemand- of SAS-locator te maken.

    Als SAS-locator wordt gebruikt, wordt de inhoud gedownload vanaf Azure Blob Storage. In dit geval hebt u geen gereserveerde streaming-eindpunten in de status Gestart nodig.
4. Download de inhoud op progressieve wijze.

## <a id="live_scenarios"></a>Evenementen live streamen 

1. Live-inhoud met verschillende protocollen voor live streamen opnemen (bijvoorbeeld RTMP of Smooth Streaming).
2. (Optioneel) Uw stream coderen in een stream met een adaptieve bitsnelheid.
3. Een voorbeeld van uw livestream bekijken.
4. De inhoud rechtstreeks aan uw klanten weergeven via algemene protocollen voor streaming (bijvoorbeeld MPEG DASH, Smooth of HLS) of aan een Content Delivery Network (CDN) voor verdere distributie.

    -of-

    De opgenomen inhoud vastleggen en opnemen om deze later te streamen (Video-on-Demand).

Als u live streamt, kunt u één van de volgende routes kiezen:

### <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Werken met kanalen die een multi-bitrate livestream van on-premises encoders ontvangen (pass-through)

Het volgende diagram toont de belangrijkste onderdelen van het AMS-platform die betrokken zijn bij de **passthrough**-werkstroom.

![Live-werkstroom](./media/scenarios-and-availability/media-services-live-streaming-current.png)

Zie [Werken met kanalen die een multi-bitrate livestream van on-premises coderingsprogramma’s ontvangen](media-services-live-streaming-with-onprem-encoders.md) voor meer informatie.

### <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Werken met kanalen waarmee Live Encoding kan worden uitgevoerd met Azure Media Services

Het volgende diagram toont de belangrijke onderdelen van het AMS-platform die betrokken zijn bij de Live Streaming-werkstroom waarbij een kanaal live codering kan uitvoeren met Media Services.

![Live-werkstroom](./media/scenarios-and-availability/media-services-live-streaming-new.png)

Zie [Werken met kanalen waarmee Live Encoding kan worden uitgevoerd met Azure Media Services](media-services-manage-live-encoder-enabled-channels.md) voor meer informatie.

Zie de sectie [Beschikbaarheid](#availability) voor informatie over beschikbaarheid in datacenters.

## <a name="consuming-content"></a>Inhoud gebruiken

Azure Media Services biedt de hulpprogramma's die u nodig hebt om geavanceerde, dynamische clientspelertoepassingen te maken voor de meeste platforms, waaronder: iOS-apparaten, Android-apparaten, Windows, Windows Phone, Xbox en settopboxen. 

## <a name="enabling-azure-cdn"></a>Azure CDN inschakelen

Media Services ondersteunt de integratie met Azure CDN. Zie [Streaming-eindpunten in een Media Services-account beheren](media-services-portal-manage-streaming-endpoints.md) voor meer informatie over het inschakelen van Azure CDN.

## <a id="scaling"></a>Een Media Services-account schalen

AMS-klanten kunnen streaming-eindpunten, de mediaverwerking en de opslag in hun AMS-accounts schalen.

* Media Services-klanten kunnen een **Standard**-streaming-eindpunt of een Premium-**streaming**-eindpunt kiezen. **Standard**-streaming-eindpunten zijn geschikt voor de meeste streaming-workloads. Deze hebben dezelfde kenmerken als **Premium**-streaming-eindpunten en de uitgaande bandbreedte wordt hiermee automatisch geschaald. 

    **Premium**-streaming-eindpunten zijn geschikt voor geavanceerde workloads omdat er gebruik wordt gemaakt van toegewezen, schaalbare bandbreedtecapaciteit. Klanten met een **Premium**-streaming-eindpunt krijgen standaard één streaming-eenheid (SU). Het streaming-eindpunt kan worden geschaald door SU's toe te voegen. Elke SU biedt extra bandbreedtecapaciteit voor de toepassing. Zie het onderwerp **Streaming-eindpunten schalen** voor meer informatie over het schalen van [Premium](media-services-portal-scale-streaming-endpoints.md)-streaming-eindpunten.

* Media Services-accounts worden gekoppeld aan een gereserveerde-eenheidstype, waarmee wordt bepaald hoe snel de mediaverwerkingstaken worden verwerkt. U kunt kiezen uit de volgende gereserveerde-eenheidstypen: **S1**, **S2** en **S3**. Een coderingstaak wordt bijvoorbeeld sneller uitgevoerd wanneer u het gereserveerde-eenheidstype **S2** gebruikt (in vergelijking met het type **S1**).

    Naast het opgeven van het gereserveerde-eenheidstype kunt u opgeven dat uw account moet worden ingericht met **gereserveerde eenheden** (RUs). Op basis van het aantal ingerichte RU's wordt bepaald hoeveel mediataken tegelijk kunnen worden verwerkt voor een bepaald account.

    >[!NOTE]
    >RU's werken voor het parallel verwerken van alle media, waaronder indexeringstaken via Azure Media Indexer. Indexeringstaken worden echter niet sneller verwerkt met snellere gereserveerde eenheden, terwijl dit bij coderen wel het geval is.

    Zie [Mediaverwerking schalen](media-services-portal-scale-media-processing.md) voor meer informatie.
* U kunt uw Media Services-account schalen door opslagaccounts toe te voegen. Elk opslagaccount is beperkt tot 500 TB. Als u uw opslag wilt uitbreiden buiten de standaardbeperkingen, kunt u meerdere opslagaccounts aan een enkel Media Services-account koppelen. Zie [Opslagaccounts beheren](meda-services-managing-multiple-storage-accounts.md) voor meer informatie.

## <a id="availability"></a> De beschikbaarheid van Media Services-functies in datacenters

Deze sectie bevat informatie over de beschikbaarheid van Media Services-functies in datacenters.

### <a name="ams-accounts"></a>AMS-accounts

#### <a name="availability"></a>Beschikbaarheid

Als u wilt bepalen of Media Services beschikbaar is in een datacenter, bladert u naar https://azure.microsoft.com/status/ en gaat u naar de tabel MEDIA.

### <a name="streaming-endpoints"></a>Streaming-eindpunten 

Media Services-klanten kunnen een **Standard**-streaming-eindpunt of een Premium-**streaming**-eindpunt kiezen. Zie de sectie [Schalen](#scaling) voor meer informatie.

#### <a name="availability"></a>Beschikbaarheid

|Name|Status|Datacenters
|---|---|---|
|Standard|Algemene beschikbaarheid|Alle|
|Premium|Algemene beschikbaarheid|Alle|

### <a name="live-encoding"></a>Live Encoding

#### <a name="availability"></a>Beschikbaarheid

Beschikbaar in alle datacenters, behalve in: Duitsland, BR - zuid, India - west, India - zuid en India - centraal. 

### <a name="encoding-media-processors"></a>Mediaprocessors coderen

AMS biedt twee coderingsprogramma's die op basis van behoefte kunnen worden gebruikt: **Media Encoder Standard** en **Media Encoder Premium Workflow**. Zie [Overzicht en een vergelijking van Azure-mediacoderingsprogramma's die op basis van behoefte kunnen worden gebruikt](media-services-encode-asset.md) voor meer informatie. 

#### <a name="availability"></a>Beschikbaarheid

|Naam van mediaprocessor|Status|Datacenters
|---|---|---|
|Media Encoder Standard|Algemene beschikbaarheid|Alle|
|Media Encoder Premium Workflow|Algemene beschikbaarheid|Overal behalve China|

### <a name="analytics-media-processors"></a>Mediaprocessors voor analyse

Media Analytics is een verzameling spraakonderdelen en visuele onderdelen waarmee organisaties en bedrijven gemakkelijker inzichten kunnen verkrijgen op basis van hun video's, waarvoor een actie kan worden uitgevoerd. Zie [Overzicht van Azure Media Services Analytics](media-services-analytics-overview.md) voor meer informatie.

> [!NOTE]
> Sommige analyse media-processors worden buiten gebruik gesteld. Zie het onderwerp [oudere onderdelen](legacy-components.md) voor de pensioen datums.

#### <a name="availability"></a>Beschikbaarheid

|Naam van mediaprocessor|Status|Datacenters
|---|---|---|
|Azure Media Face Detector|Preview|Alle|
|Azure Media Indexer|Algemene beschikbaarheid|Alle|
|Azure Media Motion Detector|Preview|Alle|
|Azure Media OCR|Preview|Alle|
|Azure Media Redactor|Algemene beschikbaarheid|Alle|
|Azure Media Video Thumbnails|Preview|Alle|

### <a name="protection"></a>Beveiliging

Met Microsoft Azure Media Services kunt u uw media beveiligen vanaf het moment dat deze uw computer verlaten en ze worden opgeslagen, verwerkt en afgeleverd. Zie [AMS-inhoud beveiligen](media-services-content-protection-overview.md) voor meer informatie.

#### <a name="availability"></a>Beschikbaarheid

|Versleuteling|Status|Datacenters|
|---|---|---| 
|Opslag|Algemene beschikbaarheid|Alle|
|AES-128-sleutels|Algemene beschikbaarheid|Alle|
|FairPlay|Algemene beschikbaarheid|Alle|
|PlayReady|Algemene beschikbaarheid|Alle|
|Widevine|Algemene beschikbaarheid|Overal behalve in Duitsland, bij overheden en in China.

### <a name="reserved-units-rus"></a>Gereserveerde eenheden (RU's)

Op basis van het aantal ingerichte, gereserveerde eenheden wordt bepaald hoeveel mediataken tegelijk kunnen worden verwerkt voor een bepaald account. 

Zie de sectie [Schalen](#scaling) voor meer informatie.

#### <a name="availability"></a>Beschikbaarheid

Beschikbaar in alle datacenters.

### <a name="reserved-unit-ru-type"></a>Gereserveerde-eenheidstype (RU)

Media Services-accounts worden gekoppeld aan een gereserveerde-eenheidstype, waarmee wordt bepaald hoe snel de mediaverwerkingstaken worden verwerkt. U kunt kiezen uit de volgende gereserveerde-eenheidstypen: S1, S2 en S3.

Zie de sectie [Schalen](#scaling) voor meer informatie.

#### <a name="availability"></a>Beschikbaarheid

|RU-typenaam|Status|Datacenters
|---|---|---|
|S1|Algemene beschikbaarheid|Alle|
|S2|Algemene beschikbaarheid|Overal behalve in BR - zuid en India - west|
|S3|Algemene beschikbaarheid|Overal behalve in India - west|

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en is onderworpen aan de service voorwaarden en het privacybeleid van Google, Inc.

## <a name="next-steps"></a>Volgende stappen

Media Services-leertrajecten bekijken.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

