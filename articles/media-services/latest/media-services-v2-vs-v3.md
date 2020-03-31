---
title: Migreren van Azure Media Services v2 naar v3
description: In dit artikel worden wijzigingen beschreven die zijn geïntroduceerd in Azure Media Services v3 en worden verschillen tussen twee versies weergegeven.
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
ms.date: 03/09/2020
ms.author: juliako
ms.openlocfilehash: fd094e35ceaa718ec1b258d74106b39744cbd16f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087832"
---
# <a name="media-services-v2-vs-v3"></a>Media Services v2 vs. v3

In dit artikel worden wijzigingen beschreven die zijn geïntroduceerd in Azure Media Services v3 en worden verschillen tussen twee versies weergegeven.

## <a name="general-changes-from-v2"></a>Algemene wijzigingen van v2

* Voor assets die met v3 zijn gemaakt, ondersteunt Media Services alleen de opslagversleuteling aan de [Azure Storage-serverzijde.](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)
    * U v3 API's gebruiken met assets die zijn gemaakt met v2-API's met [opslagversleuteling](../previous/media-services-rest-storage-encryption.md) (AES 256) van Media Services.
    * U geen nieuwe assets maken met de verouderde AES [256-opslagversleuteling](../previous/media-services-rest-storage-encryption.md) met v3-API's.
* De [eigenschappen van de asset](assets-concept.md)in v3 verschillen van v2, zie hoe de eigenschappen [kaart](#map-v3-asset-properties-to-v2).
* De v3 SDK's zijn nu losgekoppeld van de Storage SDK, waardoor u meer controle hebt over de versie van Storage SDK die u wilt gebruiken en problemen met versiebeheer voorkomt. 
* In de v3 API's zijn alle coderingsbitsnelheden in bits per seconde. Dit is anders dan de v2 Media Encoder Standard presets. Bijvoorbeeld, de bitrate in v2 zou worden opgegeven als 128 (kbps), maar in v3 zou het 128000 (bits/seconde). 
* Entiteiten AssetFiles, AccessPolicies en IngestManifests bestaan niet in v3.
* De eigenschap IAsset.ParentAssets bestaat niet in v3.
* ContentKeys is niet langer een entiteit, het is nu een eigenschap van de Streaming Locator.
* Ondersteuning voor gebeurtenisraster vervangt NotificationEndpoints.
* De volgende entiteiten zijn hernoemd
    * Taakuitvoer vervangt Taak en maakt nu deel uit van een taak.
    * Streaming Locator vervangt Locator.
    * Live Event vervangt Channel.<br/>Facturering voor live gebeurtenissen is gebaseerd op livekanaalmeters. Zie [facturering](live-event-states-billing.md) en [prijzen](https://azure.microsoft.com/pricing/details/media-services/)voor meer informatie.
    * Live Output vervangt het programma.
* Live-uitvoer starten zodra ze zijn gemaakt en stoppen wanneer ze worden verwijderd. Programma's werkten anders in de v2 API's, ze moesten na de creatie worden gestart.
* Als u informatie wilt krijgen over een taak, moet u de naam Transformeren kennen waaronder de taak is gemaakt. 
* In v2 worden [XML-invoer-](../previous/media-services-input-metadata-schema.md) en uitvoermetagegevensbestanden gegenereerd als gevolg van een coderingstaak. [output](../previous/media-services-output-metadata-schema.md) In v3 is de metagegevensindeling gewijzigd van XML naar JSON. 
* In Media Services v2 kan initialisatievector (IV) worden opgegeven. In Media Services v3 kan de FairPlay IV niet worden opgegeven. Hoewel het geen invloed heeft op klanten die Media Services gebruiken voor zowel verpakking als licentielevering, kan het een probleem zijn bij het gebruik van een DRM-systeem van derden om de FairPlay-licenties (hybride modus) te leveren. In dat geval is het belangrijk om te weten dat de FairPlay IV is afgeleid van de cbcs-sleutel-ID en kan worden opgehaald met behulp van deze formule:

    ```
    string cbcsIV =  Convert.ToBase64String(HexStringToByteArray(cbcsGuid.ToString().Replace("-", string.Empty)));
    ```

    wordt uitgevoerd met

    ``` 
    public static byte[] HexStringToByteArray(string hex)
    {
        return Enumerable.Range(0, hex.Length)
            .Where(x => x % 2 == 0)
            .Select(x => Convert.ToByte(hex.Substring(x, 2), 16))
            .ToArray();
    }
    ```

    Zie de Azure [Functions C#-code voor Media Services v3 in hybride modus voor zowel Live- als VOD-bewerkingen](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration/tree/master/LiveAndVodDRMOperationsV3).
 
> [!NOTE]
> Bekijk de naamgevingsconventies die worden toegepast op [V3-bronnen van Media Services](media-services-apis-overview.md#naming-conventions). Bekijk ook [het benoemen van blobs](assets-concept.md#naming).

## <a name="feature-gaps-with-respect-to-v2-apis"></a>Functieproblemen met v2-API's

De v3 API heeft de volgende functiehiaten met betrekking tot de v2 API. Het dichten van de hiaten is werk in uitvoering.

* De [Premium Encoder](../previous/media-services-premium-workflow-encoder-formats.md) en de oudere [mediaanalyseprocessors](../previous/media-services-analytics-overview.md) (Azure Media Services Indexer 2 Preview, Face Redactor, enz.) zijn niet toegankelijk via v3.<br/>Klanten die willen migreren van de Preview Media Indexer 1 of 2 kunnen de AudioAnalyzer-voorinstelling direct gebruiken in de v3-API.  Deze nieuwe voorinstelling bevat meer functies dan de oudere Media Indexer 1 of 2. 
* Veel van de [geavanceerde functies van de Media Encoder Standard in v2](../previous/media-services-advanced-encoding-with-mes.md) API's zijn momenteel niet beschikbaar in v3, zoals:
  
    * Stiksels van activa
    * Overlays
    * Bijsnijden
    * Miniatuursprites
    * Een stille audiotrack invoegen wanneer invoer geen audio heeft
    * Een videotrack invoegen wanneer invoer geen video heeft
* Live-gebeurtenissen met transcodering bieden momenteel geen ondersteuning voor het invoegen van leisteen in het midden van de stream en het invoegen van advertentiemarkeringen via API-aanroep. 
 
## <a name="asset-specific-changes"></a>Asset specifieke wijzigingen

### <a name="map-v3-asset-properties-to-v2"></a>V3-activa toewijzen aan v2

In de volgende tabel ziet u hoe de eigenschappen van [de activa](https://docs.microsoft.com/rest/api/media/assets/createorupdate#asset)in v3-kaart naar de eigenschappen van Asset in v2 worden weergegeven.

|v3-eigenschappen|v2-eigenschappen|
|---|---|
|`id`- (uniek) het volledige Azure Resource Manager-pad, zie voorbeelden in [Asset](https://docs.microsoft.com/rest/api/media/assets/createorupdate)||
|`name`- (uniek) zie [Naamgevingsconventies](media-services-apis-overview.md#naming-conventions) ||
|`alternateId`|`AlternateId`|
|`assetId`|`Id`- (unieke) waarde `nb:cid:UUID:` begint bij het voorvoegsel.|
|`created`|`Created`|
|`description`|`Name`|
|`lastModified`|`LastModified`|
|`storageAccountName`|`StorageAccountName`|
|`storageEncryptionFormat`| `Options`(aanmaakopties)|
|`type`||

### <a name="storage-side-encryption"></a>Versleuteling aan de opslagzijde

Om uw assets in rust te beschermen, moeten de assets worden versleuteld door de opslagzijdeversleuteling. In de volgende tabel ziet u hoe de versleuteling aan de opslagzijde werkt in Media Services:

|Versleuteling, optie|Beschrijving|Media Services v2|Media Services v3|
|---|---|---|---|
|Media Services-opslagversleuteling|AES-256 encryptie, sleutel beheerd door Media Services.|Ondersteund<sup>(1)</sup>|Niet ondersteund<sup>(2)</sup>|
|[Opslagserviceversleuteling voor gegevens in rust](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Server-side encryptie aangeboden door Azure Storage, sleutel beheerd door Azure of door de klant.|Ondersteund|Ondersteund|
|[Versleuteling aan de clientzijde van opslag](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Client-side encryptie aangeboden door Azure storage, sleutel beheerd door de klant in Key Vault.|Niet ondersteund|Niet ondersteund|

<sup>1</sup> Hoewel Media Services de behandeling van inhoud in de duidelijke/zonder enige vorm van versleuteling ondersteunt, wordt dit niet aanbevolen.

<sup>2</sup> In Media Services v3 wordt opslagversleuteling (AES-256-versleuteling) alleen ondersteund voor achterwaartse compatibiliteit wanneer uw assets zijn gemaakt met Media Services v2. Dit betekent dat V3 werkt met bestaande opslagversleutelde assets, maar geen nieuwe kan maken.

## <a name="code-differences"></a>Codeverschillen

In de volgende tabel worden de codeverschillen tussen v2 en v3 voor veelvoorkomende scenario's weergegeven.

|Scenario|V2-API|V3-API|
|---|---|---|
|Een asset maken en een bestand uploaden |[v2 .NET voorbeeld](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[v3 .NET voorbeeld](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|Een taak indienen|[v2 .NET voorbeeld](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[v3 .NET voorbeeld](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>Hier ziet u hoe u eerst een transformatie maakt en vervolgens een taak indient.|
|Een asset publiceren met AES-versleuteling |1. ContentKeyAuthorizationPolicyOption maken<br/>2. ContentKeyAuthorizationPolicy maken<br/>3. AssetDeliveryPolicy maken<br/>4. Asset maken en inhoud uploaden OF taak verzenden en uitvoerasset gebruiken<br/>5. Associate AssetDeliveryPolicy with Asset<br/>6. ContentKey maken<br/>7. ContentKey koppelen aan Asset<br/>8. Toegangsbeleid maken<br/>9. Locator maken<br/><br/>[v2 .NET voorbeeld](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. Beleid voor inhoudssleutel maken<br/>2. Asset maken<br/>3. Inhoud uploaden of Asset gebruiken als JobOutput<br/>4. Maak Streaming Locator<br/><br/>[v3 .NET voorbeeld](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|Werkgegevens opvragen en taken beheren |[Taken beheren met v2](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[Taken beheren met v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

> [!NOTE]
> Gelieve bookmark dit artikel en blijven controleren op updates.

## <a name="ask-questions-give-feedback-get-updates"></a>Stel vragen, geef feedback, ontvang updates

Bekijk het communityartikel [van Azure Media Services](media-services-community.md) om verschillende manieren te zien waarop u vragen stellen, feedback geven en updates ontvangen over Media Services.

## <a name="next-steps"></a>Volgende stappen

[Migratierichtlijnen voor de overgang van Media Services v2 naar v3](migrate-from-v2-to-v3.md)
