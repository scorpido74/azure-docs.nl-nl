---
title: Assets
titleSuffix: Azure Media Services
description: Meer informatie over wat assets zijn en hoe deze worden gebruikt door Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/29/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 3860823787b860f2504d6fb13b9479d1feec9d28
ms.sourcegitcommit: 934776a860e4944f1a0e5e24763bfe3855bc6b60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77505810"
---
# <a name="assets-in-azure-media-services"></a>Activa in Azure Media Services

In Azure Media Services bevat een [Asset](https://docs.microsoft.com/rest/api/media/assets) informatie over digitale bestanden die zijn opgeslagen in azure Storage (inclusief video, audio, afbeeldingen, miniatuur verzamelingen, tekst sporen en ondertitelings bestanden).

Een Asset wordt toegewezen aan een BLOB-container in het [Azure Storage-account](storage-account-concept.md) en de bestanden in de Asset worden opgeslagen als blok-blobs in die container. Media Services ondersteunt BLOB-lagen wanneer het account gebruik maakt van de GPv2-opslag (General-Purpose v2). Met GPv2 kunt u bestanden verplaatsen naar [koele of archief opslag](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers). **Archief** opslag is geschikt voor het archiveren van bron bestanden wanneer deze niet meer nodig zijn (bijvoorbeeld nadat deze zijn gecodeerd).

De opslaglaag van het **Archief** wordt alleen aanbevolen voor zeer grote bron bestanden die al zijn gecodeerd en de uitvoer van de coderings taak in een uitvoer BLOB-container is geplaatst. De blobs in de uitvoer container die u wilt koppelen aan een activum en gebruiken om uw inhoud te streamen of te analyseren, moeten zich in een **warme** of **koud** opslaglaag bevinden.

### <a name="naming"></a>Naamgeving 

#### <a name="assets"></a>Assets

De namen van het activum moeten uniek zijn. Media Services v3-resource namen (bijvoorbeeld assets, taken, trans formaties) zijn onderhevig aan Azure Resource Manager naamgevings beperkingen. Zie [naamgevings conventies](media-services-apis-overview.md#naming-conventions)voor meer informatie.

#### <a name="blobs"></a>Blobs

De namen van bestanden/blobs in een Asset moeten de [vereisten voor de BLOB-naam](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) en de [NTFS-naam](https://docs.microsoft.com/windows/win32/fileio/naming-a-file)volgen. De reden voor deze vereisten is dat de bestanden kunnen worden gekopieerd van Blob Storage naar een lokale NTFS-schijf voor verwerking.

## <a name="upload-digital-files-into-assets"></a>Digitale bestanden uploaden naar assets

Nadat de digitale bestanden zijn geüpload naar de opslag en zijn gekoppeld aan een Asset, kunnen ze worden gebruikt in de Media Services versleuteling, streaming en analyse van inhouds werk stromen. Een van de algemene Media Services werk stromen is het uploaden, coderen en streamen van een bestand. In deze sectie vindt u een overzicht van de algemene stappen.

> [!TIP]
> Controleer voordat u begint met het ontwikkelen [met behulp van Media Services v3 api's](media-services-apis-overview.md) (bevat informatie over het openen van api's, naam conventies, enzovoort).

1. Gebruik de API van Media Services v3 om een asset voor nieuwe 'invoer' te maken. Met deze bewerking wordt een container gemaakt in het opslagaccount dat aan uw Media Services-account is gekoppeld. De API retourneert de naam van de container (bijvoorbeeld `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`).

    Als u al een BLOB-container hebt die u aan een Asset wilt koppelen, kunt u de naam van de container opgeven tijdens het maken van de Asset. Media Services ondersteunt momenteel alleen blobs in de hoofdmap van de container en geen mappen met paden in de bestandsnaam. Dus het werkt voor een container met de bestandsnaam 'input.mp4'. Een container met de bestands naam ' Video's/invoer/invoer. MP4 ' werkt echter niet.

    U kunt de Azure CLI gebruiken om bestanden rechtstreeks in een opslagaccount en container te uploaden waarvoor u rechten hebt in uw abonnement.

    De containernaam moet uniek zijn en de richtlijnen volgen voor de naamgeving van de opslag. De naam hoeft niet de indeling te volgen voor de containernaam van de Media Services-asset (Asset-GUID).

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. Haal een SAS-URL op met lees-/schrijfmachtigingen die wordt gebruikt voor het uploaden van digitale bestanden in de asset-container. U kunt de Media Services-API gebruiken voor het [vermelden van de URL's voor de asset-container](https://docs.microsoft.com/rest/api/media/assets/listcontainersas).
3. Gebruik de Azure Storage Api's of Sdk's (bijvoorbeeld de [Storage rest API](../../storage/common/storage-rest-api-auth.md) of [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) om bestanden te uploaden naar de-container van de Asset.
4. Gebruik Media Services v3-API's om een transformatie en een taak te maken om uw 'invoer'-asset te verwerken. Zie [Transformaties en taken](transform-concept.md) voor meer informatie.
5. De inhoud van de Asset ' output ' streamen.

Voor een volledig .NET-voor beeld waarin wordt getoond hoe de Asset moet worden gemaakt, een Beschrijf bare SAS-URL naar de container van het activum in de opslag wordt opgehaald en het bestand in de container in Storage wordt geüpload met behulp van de SAS URL, Zie [een taak invoer maken op basis van een lokaal bestand](job-input-from-local-file-how-to.md).

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

Zie [een taak invoer maken op basis van een lokaal bestand](job-input-from-local-file-how-to.md)voor een volledig voor beeld. In Media Services v3 kan de invoer van een taak ook worden gemaakt op basis van HTTPS-Url's (Zie [een taak invoer maken op basis van een HTTPS-URL](job-input-from-http-how-to.md)).

## <a name="map-v3-asset-properties-to-v2"></a>V3-Asset-eigenschappen toewijzen aan v2

In de volgende tabel ziet u hoe de eigenschappen van het [element](https://docs.microsoft.com/rest/api/media/assets/createorupdate#asset)in v3 worden toegewezen aan de eigenschappen van het element in v2.

|V3-eigenschappen|v2-eigenschappen|
|---|---|
|`id`-(uniek) het volledige Azure Resource Manager pad, zie voor beelden in [Asset](https://docs.microsoft.com/rest/api/media/assets/createorupdate)||
|`name`-(uniek) Zie [naamgevings conventies](media-services-apis-overview.md#naming-conventions) ||
|`alternateId`|`AlternateId`|
|`assetId`|`Id`-(unieke) waarde begint met het voor voegsel `nb:cid:UUID:`.|
|`created`|`Created`|
|`description`|`Name`|
|`lastModified`|`LastModified`|
|`storageAccountName`|`StorageAccountName`|
|`storageEncryptionFormat`| `Options` (opties voor maken)|
|`type`||

## <a name="storage-side-encryption"></a>Versleuteling van opslag aan de serverzijde

Ter bescherming van uw activa in rust, moeten de activa van de versleuteling van opslag aan de serverzijde worden versleuteld. De volgende tabel ziet u de werking van de versleuteling van opslag aan de serverzijde in Media Services:

|Optie voor opslagversleuteling|Beschrijving|Media Services v2|Media Services v3|
|---|---|---|---|
|Media Services-Storage-versleuteling|AES-256-versleuteling, sleutel beheerd door Media Services.|Ondersteund<sup>(1)</sup>|Niet ondersteund<sup>(2)</sup>|
|[Storage Service Encryption voor Data-at-rest](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Versleuteling aan de server zijde die wordt aangeboden door Azure Storage, sleutel die wordt beheerd door Azure of door de klant.|Ondersteund|Ondersteund|
|[Versleuteling van de opslag aan de client zijde](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Versleuteling aan de client zijde die wordt aangeboden door Azure Storage, sleutel die wordt beheerd door de klant in Key Vault.|Niet ondersteund|Niet ondersteund|

<sup>1</sup> hoewel Media Services de verwerking van inhoud in de Clear/zonder vorm van versleuteling ondersteunt, wordt dit niet aanbevolen.

<sup>2</sup> in Media Services V3 wordt opslag VERSLEUTELING (AES-256-versleuteling) alleen ondersteund voor achterwaartse compatibiliteit wanneer uw assets zijn gemaakt met Media Services v2. Dit betekent dat v3 werkt met bestaande, versleutelde opslag assets, maar geen nieuwe apparaten mag maken.

## <a name="filtering-ordering-paging"></a>Filters, bestellen, wisselbestand

Zie [filteren, ordenen, pagineren van Media Services entiteiten](entities-overview.md).

## <a name="next-steps"></a>Volgende stappen

* [Een bestand streamen](stream-files-dotnet-quickstart.md)
* [Een cloud-DVR gebruiken](live-event-cloud-dvr.md)
* [Verschillen tussen Media Services v2 en v3](migrate-from-v2-to-v3.md)
