---
title: Migreren van Azure Media Services v2 naar v3 | Microsoft Docs
description: In dit artikel worden wijzigingen beschreven die in Azure Media Services v3 zijn geïntroduceerd en verschillen tussen twee versies worden weer gegeven. Het artikel bevat ook migratie richtlijnen voor het overstappen van Media Services v2 naar v3.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: azure media services, stream, uitzenden, live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 10/02/2019
ms.author: juliako
ms.openlocfilehash: 3520b7d6b0fd67fdbff3e1dd78d038f36ad5f0af
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77133424"
---
# <a name="migration-guidance-for-moving-from-media-services-v2-to-v3"></a>Migratie richtlijnen voor het overstappen van Media Services versie 2 naar v3

>Ontvang een melding over wanneer u deze pagina voor updates opnieuw moet bezoeken door deze URL te kopiëren en te plakken: `https://docs.microsoft.com/api/search/rss?search=%22Migrate+from+Azure+Media+Services+v2+to+v3%22&locale=en-us` in uw RSS feed-lezer.

In dit artikel worden de wijzigingen beschreven die in Azure Media Services v3 zijn geïntroduceerd, verschillen tussen twee versies weer gegeven en de richt lijnen voor migratie.

Als u op dit moment een video service hebt ontwikkeld voor de [oudere Media Services v2 api's](../previous/media-services-overview.md), moet u de volgende richt lijnen en overwegingen door nemen voordat u naar de V3-api's migreert. Er zijn veel voor delen en nieuwe functies in de V3 API die de ontwikkel ervaring en mogelijkheden van Media Services verbeteren. Net als in het gedeelte [bekende problemen](#known-issues) in dit artikel, zijn er echter ook enkele beperkingen door wijzigingen tussen de API-versies. Deze pagina blijft behouden naarmate het Media Services team voortdurende verbeteringen in de V3-Api's aanbrengt en de hiaten tussen de versies verhelpt. 

> [!NOTE]
> U kunt momenteel geen gebruik maken van de Azure-portal om v3-resources te beheren. Gebruik de [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref) of een van de ondersteunde [SDK's](media-services-apis-overview.md#sdks).

## <a name="benefits-of-media-services-v3"></a>Voor delen van Media Services v3
  
### <a name="api-is-more-approachable"></a>API is beter te benaderen

*  v3 is gebaseerd op een geïntegreerde API-gebied dat functionaliteit voor zowel beheer als bewerkingen beschikbaar stelt die is gebouwd op Azure Resource Manager. Azure Resource Manager sjablonen kunnen worden gebruikt voor het maken en implementeren van trans formaties, streaming-eind punten, Live-gebeurtenissen en meer.
* [OpenAPI-specificatie (voorheen Swagger-document genoemd)](https://aka.ms/ams-v3-rest-sdk) .
    Beschrijft het schema voor alle service onderdelen, inclusief code ring op basis van een bestand.
* Sdk's beschikbaar voor [.net](https://aka.ms/ams-v3-dotnet-ref), .net core, [node. js](/javascript/api/overview/azure/mediaservices/management), [python](https://aka.ms/ams-v3-python-ref), [Java](https://aka.ms/ams-v3-java-ref), [Go](https://aka.ms/ams-v3-go-ref)en Ruby.
* [Azure cli](https://aka.ms/ams-v3-cli-ref) -integratie voor eenvoudige script ondersteuning.

### <a name="new-features"></a>Nieuwe functies

* Voor taak verwerking op basis van een bestand kunt u een HTTP/S-URL gebruiken als invoer.<br/>U hoeft inhoud niet al in azure te hebben opgeslagen en u hoeft geen assets te maken.
* Introduceert het concept van [trans formaties](transforms-jobs-concept.md) voor taak verwerking op basis van bestanden. Een trans formatie kan worden gebruikt om herbruikbare configuraties te bouwen, Azure Resource Manager-sjablonen te maken en verwerkings instellingen te isoleren tussen meerdere klanten of tenants.
* Een Asset kan meerdere [Stream-Locators](streaming-locators-concept.md) hebben elk met verschillende [dynamische pakketten](dynamic-packaging-overview.md) en dynamische versleutelings instellingen.
* [Content Protection](content-key-policy-concept.md) ondersteunt functies met meerdere sleutels.
* U kunt live-gebeurtenissen die Maxi maal 24 uur duren, streamen wanneer u Media Services gebruikt voor het omzetten van een single bitrate-bijdrage-feed in een uitvoer stroom met meerdere bitrates.
* Nieuwe ondersteuning voor live streaming met lage latentie voor Live-gebeurtenissen. Zie [latentie](live-event-latency.md)voor meer informatie.
* Preview van Live Event ondersteunt [dynamische pakketten](dynamic-packaging-overview.md) en dynamische versleuteling. Hiermee wordt de beveiliging van inhoud op de preview-versie en de verstreep-en HLS-verpakking ingeschakeld.
* Live output is eenvoudiger te gebruiken dan de entiteit van het programma in de v2-Api's. 
* Verbeterde RTMP-ondersteuning (verbeterde stabiliteit en meer ondersteuning voor broncode encoders).
* RTMP beveiligde opname.<br/>Wanneer u een live gebeurtenis maakt, krijgt u vier opname-Url's. De 4 opname-Url's zijn bijna identiek, hebben hetzelfde streaming token (AppId), alleen het poort nummer onderdeel is anders. Twee van de Url's zijn primaire en back-ups voor RTMP.   
* U hebt op rollen gebaseerd toegangs beheer (RBAC) voor uw entiteiten. 

## <a name="changes-from-v2"></a>Wijzigingen van v2

* Voor assets die zijn gemaakt met v3, Media Services alleen de [Azure storage opslag versleuteling aan de server zijde](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)ondersteunt.
    * U kunt v3-Api's gebruiken met assets die zijn gemaakt met v2-Api's met de [opslag versleuteling](../previous/media-services-rest-storage-encryption.md) (AES 256) van Media Services.
    * U kunt geen nieuwe assets maken met de verouderde AES 256- [opslag versleuteling](../previous/media-services-rest-storage-encryption.md) met behulp van v3-api's.
* De eigenschappen van het [activum](assets-concept.md)in v3 verschillen van v2, Zie [hoe de eigenschappen worden toegewezen](assets-concept.md#map-v3-asset-properties-to-v2).
* De V3 Sdk's zijn nu losgekoppeld van de opslag-SDK, waarmee u meer controle hebt over de versie van de opslag-SDK die u wilt gebruiken en voor komt versie problemen. 
* In de V3-Api's bevinden alle coderings bitsnelheden zich in bits per seconde. Dit wijkt af van de v2-Media Encoder Standard voor instellingen. De bitrate in v2 wordt bijvoorbeeld opgegeven als 128 (kbps), maar in v3 zou 128000 (bits/seconde) zijn. 
* Entiteiten AssetFiles, AccessPolicies en IngestManifests bestaan niet in v3.
* De eigenschap IAsset. ParentAssets bestaat niet in v3.
* ContentKeys is geen entiteit meer, het is nu een eigenschap van de streaming-Locator.
* Event Grid-ondersteuning vervangt NotificationEndpoints.
* De naam van de volgende entiteiten is gewijzigd
    * Taak uitvoer vervangt taak en maakt nu deel uit van een taak.
    * De streaming-Locator vervangt Locator.
    * Live gebeurtenis vervangt kanaal.<br/>Facturering van Live-gebeurtenissen is gebaseerd op Live Channel meters. Zie [facturering](live-event-states-billing.md) en [prijzen](https://azure.microsoft.com/pricing/details/media-services/)voor meer informatie.
    * Live uitvoer vervangt programma.
* Live-uitvoer starten zodra ze zijn gemaakt en stoppen wanneer ze worden verwijderd. Program ma's hebben in de v2-Api's anders gewerkt, maar ze moesten worden gestart na het maken.
* Als u informatie over een taak wilt ontvangen, moet u de naam van de trans formatie weten waaronder de taak is gemaakt. 
* In v2 worden XML- [invoer](../previous/media-services-input-metadata-schema.md) -en [uitvoer](../previous/media-services-output-metadata-schema.md) -meta gegevensbestanden gegenereerd als resultaat van een coderings taak. In v3 wordt de meta gegevens indeling gewijzigd van XML naar JSON. 

> [!NOTE]
> Bekijk de naamgevings regels die worden toegepast op [Media Services v3-resources](media-services-apis-overview.md#naming-conventions). Bekijk ook [naamgevings-blobs](assets-concept.md#naming-blobs).

## <a name="feature-gaps-with-respect-to-v2-apis"></a>Hiaten in functies met betrekking tot v2-Api's

De V3 API heeft de volgende functie hiaten ten opzichte van de v2 API. Het sluiten van de hiaten is het onderhanden werk.

* De [Premium encoder](../previous/media-services-premium-workflow-encoder-formats.md) en de verouderde [Media Analytics-processors](../previous/media-services-analytics-overview.md) (Azure Media Services indexer 2, Face Redactor, enzovoort) zijn niet toegankelijk via v3.<br/>Klanten die vanaf de preview-versie van Media Indexer 1 of 2 willen migreren, kunnen onmiddellijk de voor instelling AudioAnalyzer in de V3 API gebruiken.  Deze nieuwe definitie bevat meer functies dan de oudere Media Indexer 1 of 2. 
* Veel van de [geavanceerde functies van de Media Encoder Standard in v2](../previous/media-services-advanced-encoding-with-mes.md) -api's zijn momenteel niet beschikbaar in v3, zoals:
  
    * Activa bijvoegen
    * Overlays
    * Bijsnijd
    * Miniatuur sprites
    * Een Silent audio-track invoegen wanneer de invoer geen audio heeft
    * Een video track invoegen wanneer de invoer geen video bevat
* Live-gebeurtenissen met transcode ring bieden momenteel geen ondersteuning voor de toevoeging van een mid-Stream en het invoegen van AD-markeringen via API-aanroepen. 

> [!NOTE]
> Geef een blad wijzer op voor dit artikel en blijf controleren op updates.
 
## <a name="code-differences"></a>Code verschillen

In de volgende tabel ziet u de code verschillen tussen v2 en v3 voor algemene scenario's.

|Scenario|V2 API|V3 API|
|---|---|---|
|Een Asset maken en een bestand uploaden |[v2 .NET-voor beeld](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[v3 .NET-voor beeld](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|Een taak indienen|[v2 .NET-voor beeld](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[v3 .NET-voor beeld](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>Laat zien hoe u eerst een trans formatie maakt en vervolgens een taak verzendt.|
|Een Asset met AES-versleuteling publiceren |1. ContentKeyAuthorizationPolicyOption maken<br/>2. ContentKeyAuthorizationPolicy maken<br/>3. AssetDeliveryPolicy maken<br/>4. activa maken en inhoud uploaden of taak verzenden en uitvoer activa gebruiken<br/>5. AssetDeliveryPolicy koppelen aan Asset<br/>6. ContentKey maken<br/>7. Koppel ContentKey aan Asset<br/>8. AccessPolicy maken<br/>9. Locator maken<br/><br/>[v2 .NET-voor beeld](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. inhouds sleutel beleid maken<br/>2. Asset maken<br/>3. inhoud uploaden of activa gebruiken als JobOutput<br/>4. streaming-Locator maken<br/><br/>[v3 .NET-voor beeld](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|Taak Details ophalen en taken beheren |[Taken beheren met v2](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[Taken beheren met v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

## <a name="known-issues"></a>Bekende problemen

* U kunt momenteel geen gebruik maken van de Azure-portal om v3-resources te beheren. Gebruik de [rest API](https://aka.ms/ams-v3-rest-sdk), CLI of een van de ondersteunde sdk's.
* U moet gereserveerde media-eenheden (MRUs) inrichten in uw account om de gelijktijdigheid en prestaties van uw taken te bepalen, met name voor de analyse van video of audio. Zie [Mediaverwerking schalen](../previous/media-services-scale-media-processing-overview.md) voor meer informatie. U kunt de MRUs beheren met [CLI 2,0 voor Media Services v3](media-reserved-units-cli-how-to.md), met behulp van de [Azure Portal](../previous/media-services-portal-scale-media-processing.md)of met behulp van de [v2-api's](../previous/media-services-dotnet-encoding-units.md). U moet MRUs inrichten, ongeacht of u Media Services v2-of v3-Api's gebruikt.
* Media Services entiteiten die met de V3 API zijn gemaakt, kunnen niet worden beheerd door de v2 API.  
* Niet alle entiteiten in de v2 API worden automatisch weer gegeven in de V3 API.  Hieronder volgen enkele voor beelden van entiteiten in de twee versies die niet compatibel zijn:  
    * Taken en taken die zijn gemaakt in v2, worden niet weer gegeven in v3, omdat ze niet zijn gekoppeld aan een trans formatie. De aanbeveling is om over te scha kelen naar v3-trans formaties en-taken. Er is een relatief korte periode voor het bewaken van de Inflight v2-taken tijdens het overschakelen.
    * Kanalen en Program Ma's die zijn gemaakt met v2 (die zijn toegewezen aan Live-gebeurtenissen en live-uitvoer in v3), kunnen niet worden beheerd met v3. De aanbeveling is om over te scha kelen naar v3 Live-gebeurtenissen en live-uitvoer op een handige kanaal stop.<br/>Momenteel kunt u continu actieve kanalen niet migreren.  

> [!NOTE]
> Deze pagina blijft behouden naarmate het Media Services team voortdurende verbeteringen in de V3-Api's aanbrengt en de hiaten tussen de versies verhelpt.

## <a name="ask-questions-give-feedback-get-updates"></a>Vragen stellen, feedback geven, updates ophalen

Bekijk het [Azure Media Services Community](media-services-community.md) -artikel voor verschillende manieren om vragen te stellen, feedback te geven en updates te ontvangen over Media Services.

## <a name="next-steps"></a>Volgende stappen

Als u wilt zien hoe eenvoudig het is om videobestanden te coderen en streamen, gaat u naar [Snelstartgids: Videobestanden streamen - .NET](stream-files-dotnet-quickstart.md). 

