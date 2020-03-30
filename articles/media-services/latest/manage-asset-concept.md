---
title: Assets beheren in Azure Media Services
titleSuffix: Azure Media Services
description: Een asset waarbij u media invoert (bijvoorbeeld via upload of live inname), uitvoermedia (uit een taakuitvoer) en media publiceert van (voor streaming). Dit onderwerp geeft een overzicht van hoe u een nieuw item maken en bestanden uploaden.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/26/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: fcdb8af770fa0068e8413d4609a56223a9a20ce2
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80345899"
---
# <a name="manage-assets"></a>Assets beheren

In Azure Media Services is een [asset](https://docs.microsoft.com/rest/api/media/assets) de plaats waar u 

* mediabestanden uploaden naar een asset,
* live streams in te nemen en te archiveren tot een asset,
* de resultaten van een codering van analysetaak naar een asset,
* media publiceren voor streaming, 
* bestanden downloaden van een asset.

Dit onderwerp geeft een overzicht van hoe u bestanden uploaden naar een asset en een aantal andere algemene bewerkingen uitvoeren. Het biedt ook links naar code monsters en aanverwante onderwerpen.

## <a name="prerequisite"></a>Vereiste 

Voordat u begint met ontwikkelen, bekijkt u:

* [Concepten](concepts-overview.md)
* [Ontwikkelen met Media Services v3 API's](media-services-apis-overview.md) (inclusief informatie over toegang tot API's, naamgevingsconventies, enzovoort) 

## <a name="upload-media-files-into-an-asset"></a>Mediabestanden uploaden naar een asset

Nadat de digitale bestanden zijn geüpload naar opslag en gekoppeld aan een asset, kunnen ze worden gebruikt in de Media Services-coderings-, streaming- en analyse-inhoudswerkstromen. Een van de algemene mediaservices-werkstromen is het uploaden, coderen en streamen van een bestand. In dit gedeelte worden de algemene stappen beschreven.

1. Gebruik de API van Media Services v3 om een asset voor nieuwe 'invoer' te maken. Met deze bewerking wordt een container gemaakt in het opslagaccount dat aan uw Media Services-account is gekoppeld. De API retourneert de `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`containernaam (bijvoorbeeld).

    Als u al een blobcontainer hebt die u aan een actief wilt koppelen, u de containernaam opgeven wanneer u het actief maakt. Media Services ondersteunt momenteel alleen blobs in de hoofdmap van de container en geen mappen met paden in de bestandsnaam. Dus het werkt voor een container met de bestandsnaam 'input.mp4'. Een container met de bestandsnaam 'video's/inputs/input.mp4' werkt echter niet.

    U kunt de Azure CLI gebruiken om bestanden rechtstreeks in een opslagaccount en container te uploaden waarvoor u rechten hebt in uw abonnement.

    De containernaam moet uniek zijn en de richtlijnen volgen voor de naamgeving van de opslag. De naam hoeft niet de indeling te volgen voor de containernaam van de Media Services-asset (Asset-GUID).

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. Haal een SAS-URL op met lees-/schrijfmachtigingen die wordt gebruikt voor het uploaden van digitale bestanden in de asset-container.

    U kunt de Media Services-API gebruiken voor het [vermelden van de URL's voor de asset-container](https://docs.microsoft.com/rest/api/media/assets/listcontainersas).

    **AssetContainerSas.listContainerSas** neemt een [parameter ListContainerSasInput](https://docs.microsoft.com/rest/api/media/assets/listcontainersas#listcontainersasinput) waarop u instelt `expiryTime`. De tijd moet worden ingesteld op < 24 uur.

    [ListContainerSasInput](https://docs.microsoft.com/rest/api/media/assets/listcontainersas#listcontainersasinput) retourneert meerdere SAS-URL's omdat er twee opslagaccountsleutels voor elk opslagaccount zijn. Een opslagaccount heeft twee sleutels omdat het zorgt voor een naadloze rotatie van opslagaccountsleutels (bijvoorbeeld de ene keer tijdens het gebruik van de andere, en begint vervolgens de nieuwe sleutel te gebruiken en de andere sleutel te roteren). De 1e SAS URL staat voor storage key1 en de tweede opslagsleutel2.
3. Gebruik de Azure Storage API's of SDK's (bijvoorbeeld de [Storage REST API](../../storage/common/storage-rest-api-auth.md) of [.NET SDK)](../../storage/blobs/storage-quickstart-blobs-dotnet.md)om bestanden in de Asset-container te uploaden.
4. Gebruik Media Services v3-API's om een transformatie en een taak te maken om uw 'invoer'-asset te verwerken. Zie [Transformeren en taken voor](transform-concept.md)meer informatie.
5. Stream de inhoud van het item 'uitvoer'.

### <a name="create-a-new-asset"></a>Nieuwe asset maken

> [!NOTE]
> De eigenschappen van een asset van het type Datetime zijn altijd in UTC-indeling.

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

Zie voor een voorbeeld van REST het voorbeeld [Een actief maken met REST.](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples)

In het voorbeeld wordt uitgelegd hoe u de **aanvraaginstantie maakt,** waar u beschrijving, containernaam, opslagaccount en andere nuttige informatie opgeven.

#### <a name="curl"></a>cURL

```cURL
curl -X PUT \
  'https://management.azure.com/subscriptions/00000000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Media/mediaServices/amsAccountName/assets/myOutputAsset?api-version=2018-07-01' \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "properties": {
    "description": "",
  }
}'
```

#### <a name="net"></a>.NET

```csharp
 Asset asset = await client.Assets.CreateOrUpdateAsync(resourceGroupName, accountName, assetName, new Asset());
```

### <a name="see-also"></a>Zie ook

* [Een taakinvoer maken uit een lokaal bestand](job-input-from-local-file-how-to.md)
* [Een taakinvoer maken op basis van een HTTPS-URL](job-input-from-http-how-to.md)

## <a name="ingest-and-archive-live-streams-into-an-asset"></a>Inname en archiveren van live streams in een asset

In Media Services is een [object Live Output](https://docs.microsoft.com/rest/api/media/liveoutputs) als een digitale videorecorder die uw live stream opneemt en opneemt tot een asset in uw Media Services-account. De opgenomen inhoud blijft bestaan in de container die wordt gedefinieerd door de [assetbron.](https://docs.microsoft.com/rest/api/media/assets)

Zie voor meer informatie:

* [Een cloud-DVR gebruiken](live-event-cloud-dvr.md)
* [Live-zelfstudie streamen](stream-live-tutorial-with-api.md)

## <a name="output-the-results-of-a-job-to-an-asset"></a>De resultaten van een taak uitleveren aan een actief

In Media Services moet u bij het verwerken van uw video's (bijvoorbeeld coderen of analyseren) een [uitvoerasset](assets-concept.md) maken om het resultaat van uw taak op te [slaan.](transforms-jobs-concept.md)

Zie voor meer informatie:

* [Een video coderen](encoding-concept.md)
* [Een taakinvoer maken uit een lokaal bestand](job-input-from-local-file-how-to.md)

## <a name="publish-an-asset-for-streaming"></a>Een asset publiceren voor streaming

Als u een item voor streaming wilt publiceren, moet u een [streaminglocator](streaming-locators-concept.md)maken. De streaming locator moet weten welke naam u wilt publiceren. 

Zie voor meer informatie:

[Zelfstudie: Video's uploaden, coderen en streamen met Media Services v3](stream-files-tutorial-with-api.md)

## <a name="download-results-of-a-job-from-an-output-asset"></a>Resultaten van een taak downloaden van een uitvoerasset

U deze resultaten van uw taak vervolgens downloaden naar een lokale map met behulp van Media Service en Storage API's. 

Zie het [voorbeeld van downloadbestanden.](download-results-howto.md)

## <a name="filtering-ordering-paging"></a>Filteren, bestellen, paging

Zie [Het filteren, bestellen, paging van mediaservices entiteiten](entities-overview.md).

## <a name="next-steps"></a>Volgende stappen

Bekijk de volledige codevoorbeelden die laten zien hoe je live en on-demand uploaden, coderen, analyseren, streamen: 

* [Java](https://docs.microsoft.com/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/), 
* [.net](https://docs.microsoft.com/samples/azure-samples/media-services-v3-dotnet/azure-media-services-v3-samples-using-net/), 
* [RUST](https://docs.microsoft.com/samples/azure-samples/media-services-v3-rest-postman/azure-media-services-postman-collection/).
