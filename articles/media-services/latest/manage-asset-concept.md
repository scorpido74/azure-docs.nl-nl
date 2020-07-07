---
title: Assets in Azure Media Services beheren
titleSuffix: Azure Media Services
description: Een Asset waar u media invoert (bijvoorbeeld via uploaden of live-opname), uitvoer media (vanuit een taak uitvoer) en het publiceren van media van (voor streaming). In dit onderwerp vindt u een overzicht van het maken van een nieuw activum en het uploaden van bestanden.
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
ms.openlocfilehash: 9136fd702fad5c12a8ec97a68ff8a592a203d7d2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80582201"
---
# <a name="manage-assets"></a>Assets beheren

In Azure Media Services is een [activum](https://docs.microsoft.com/rest/api/media/assets) waar u 

* Media bestanden uploaden naar een Asset,
* Live streams opnemen en archiveren in een Asset,
* de resultaten van een code ring van de analyse taak naar een Asset uitvoeren,
* media publiceren voor streaming, 
* down load bestanden van een Asset.

In dit onderwerp vindt u een overzicht van hoe u bestanden uploadt naar een Asset en enkele andere algemene bewerkingen uitvoert. Het bevat ook koppelingen naar code voorbeelden en verwante onderwerpen.

## <a name="prerequisite"></a>Vereiste 

Controleer het volgende voordat u begint met ontwikkelen:

* [Concepten](concepts-overview.md)
* [Ontwikkelen met Media Services v3-api's](media-services-apis-overview.md) (bevat informatie over het openen van api's, naam conventies, enzovoort) 

## <a name="upload-media-files-into-an-asset"></a>Media bestanden uploaden naar een Asset

Nadat de digitale bestanden zijn geüpload naar de opslag en zijn gekoppeld aan een Asset, kunnen ze worden gebruikt in de Media Services versleuteling, streaming en analyse van inhouds werk stromen. Een van de algemene Media Services werk stromen is het uploaden, coderen en streamen van een bestand. In deze sectie vindt u een overzicht van de algemene stappen.

1. Gebruik de API van Media Services v3 om een asset voor nieuwe 'invoer' te maken. Met deze bewerking wordt een container gemaakt in het opslagaccount dat aan uw Media Services-account is gekoppeld. De API retourneert de naam van de container (bijvoorbeeld `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"` ).

    Als u al een BLOB-container hebt die u aan een Asset wilt koppelen, kunt u de naam van de container opgeven tijdens het maken van de Asset. Media Services ondersteunt momenteel alleen blobs in de hoofdmap van de container en geen mappen met paden in de bestandsnaam. Dus het werkt voor een container met de bestandsnaam 'input.mp4'. Een container met de bestands naam ' Video's/invoer/input.mp4 ' werkt echter niet.

    U kunt de Azure CLI gebruiken om bestanden rechtstreeks in een opslagaccount en container te uploaden waarvoor u rechten hebt in uw abonnement.

    De containernaam moet uniek zijn en de richtlijnen volgen voor de naamgeving van de opslag. De naam hoeft niet de indeling te volgen voor de containernaam van de Media Services-asset (Asset-GUID).

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. Haal een SAS-URL op met lees-/schrijfmachtigingen die wordt gebruikt voor het uploaden van digitale bestanden in de asset-container.

    U kunt de Media Services-API gebruiken voor het [vermelden van de URL's voor de asset-container](https://docs.microsoft.com/rest/api/media/assets/listcontainersas).

    **AssetContainerSas. listContainerSas** haalt een [ListContainerSasInput](https://docs.microsoft.com/rest/api/media/assets/listcontainersas#listcontainersasinput) -para meter op die u hebt ingesteld `expiryTime` . De tijd moet worden ingesteld op < 24 uur.

    [ListContainerSasInput](https://docs.microsoft.com/rest/api/media/assets/listcontainersas#listcontainersasinput) retourneert meerdere SAS-url's als er twee opslag account sleutels voor elk opslag account zijn. Een opslag account heeft twee sleutels, omdat het een failover en naadloze rotatie van de sleutels van een opslag account biedt. De eerste SAS-URL vertegenwoordigt de eerste sleutel voor het opslag account en de tweede SAS-URL staat voor de tweede sleutel.
3. Gebruik de Azure Storage Api's of Sdk's (bijvoorbeeld de [Storage rest API](../../storage/common/storage-rest-api-auth.md) of [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) om bestanden te uploaden naar de-container van de Asset.
4. Gebruik Media Services v3-API's om een transformatie en een taak te maken om uw 'invoer'-asset te verwerken. Zie [trans formaties en jobs](transform-concept.md)voor meer informatie.
5. De inhoud van de Asset ' output ' streamen.

### <a name="create-a-new-asset"></a>Een nieuwe Asset maken

> [!NOTE]
> De eigenschappen van een Asset van het type datetime zijn altijd in UTC-indeling.

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

Zie het voor beeld [een activum maken met rest](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples) voor een rest-voor beeld.

Het voor beeld laat zien hoe u de **hoofd tekst** van de aanvraag maakt, waarin u een beschrijving, container naam, opslag account en andere nuttige informatie kunt opgeven.

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

* [Een taak invoer maken op basis van een lokaal bestand](job-input-from-local-file-how-to.md)
* [Een taak invoer maken op basis van een HTTPS-URL](job-input-from-http-how-to.md)

## <a name="ingest-and-archive-live-streams-into-an-asset"></a>Live streams opnemen en archiveren in een Asset

In Media Services is een [Live uitvoer](https://docs.microsoft.com/rest/api/media/liveoutputs) object net als een digitale video recorder waarmee uw live stream wordt onderschept en opgenomen in een asset in uw Media Services-account. De opgenomen inhoud wordt opgeslagen in de container die is gedefinieerd door de bron van de [activa](https://docs.microsoft.com/rest/api/media/assets) .

Zie voor meer informatie:

* [Een cloud-DVR gebruiken](live-event-cloud-dvr.md)
* [Zelf studie over streaming live](stream-live-tutorial-with-api.md)

## <a name="output-the-results-of-a-job-to-an-asset"></a>De resultaten van een taak naar een Asset uitvoeren

In Media Services moet u bij het verwerken van uw Video's (bijvoorbeeld code ring of analyse) een uitvoer [activum](assets-concept.md) maken om het resultaat van uw [taak](transforms-jobs-concept.md)op te slaan.

Zie voor meer informatie:

* [Een video coderen](encoding-concept.md)
* [Een taak invoer maken op basis van een lokaal bestand](job-input-from-local-file-how-to.md)

## <a name="publish-an-asset-for-streaming"></a>Een Asset publiceren voor streaming

Als u een Asset voor streaming wilt publiceren, moet u een [streaming-Locator](streaming-locators-concept.md)maken. De streaming-Locator moet weten wat de naam van de Asset is die u wilt publiceren. 

Zie voor meer informatie:

[Zelf studie: Video's uploaden, coderen en streamen met Media Services v3](stream-files-tutorial-with-api.md)

## <a name="download-results-of-a-job-from-an-output-asset"></a>Resultaten van een taak downloaden van een uitvoer activum

U kunt deze resultaten van uw taak vervolgens downloaden naar een lokale map met behulp van media service en opslag-Api's. 

Zie het voor beeld voor het [downloaden van bestanden](download-results-howto.md) .

## <a name="filtering-ordering-paging"></a>Filteren, ordenen, paginering

Zie [filteren, ordenen, pagineren van Media Services entiteiten](entities-overview.md).

## <a name="next-steps"></a>Volgende stappen

Bekijk de volledige code voorbeelden die laten zien hoe u live en on-Demand kunt uploaden, coderen, analyseren en streamen: 

* [Java](https://docs.microsoft.com/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/), 
* [.Net](https://docs.microsoft.com/samples/azure-samples/media-services-v3-dotnet/azure-media-services-v3-samples-using-net/), 
* [Rest](https://docs.microsoft.com/samples/azure-samples/media-services-v3-rest-postman/azure-media-services-postman-collection/).
