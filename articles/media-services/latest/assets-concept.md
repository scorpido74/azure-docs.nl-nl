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
ms.openlocfilehash: ce32343faefbcf2484ec0b1b39f752654a2d8514
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303610"
---
# <a name="assets-in-azure-media-services"></a>Activa in Azure Media Services

In Azure Media Services is een [activum](https://docs.microsoft.com/rest/api/media/assets) een kern concept. U voert media in (bijvoorbeeld via upload-of live-opname), uitvoer media (van een taak uitvoer) en het publiceren van media van (voor streaming). 

Een Asset wordt toegewezen aan een BLOB-container in het [Azure Storage-account](storage-account-concept.md) en de bestanden in de Asset worden opgeslagen als blok-blobs in die container. Assets bevatten informatie over digitale bestanden die zijn opgeslagen in Azure Storage (inclusief video, audio, afbeeldingen, miniatuur verzamelingen, tekst sporen en ondertitelings bestanden).

Media Services ondersteunt BLOB-lagen wanneer het account gebruik maakt van de GPv2-opslag (General-Purpose v2). Met GPv2 kunt u bestanden verplaatsen naar [koele of archief opslag](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers). **Archief** opslag is geschikt voor het archiveren van bron bestanden wanneer deze niet meer nodig zijn (bijvoorbeeld nadat deze zijn gecodeerd).

De opslaglaag van het **Archief** wordt alleen aanbevolen voor zeer grote bron bestanden die al zijn gecodeerd en de uitvoer van de coderings taak in een uitvoer BLOB-container is geplaatst. De blobs in de uitvoer container die u wilt koppelen aan een activum en gebruiken om uw inhoud te streamen of te analyseren, moeten zich in een **warme** of **koud** opslaglaag bevinden.

## <a name="naming"></a>Naamgeving 

### <a name="assets"></a>Assets

De namen van het activum moeten uniek zijn. Media Services v3-resource namen (bijvoorbeeld assets, taken, trans formaties) zijn onderhevig aan Azure Resource Manager naamgevings beperkingen. Zie [naamgevings conventies](media-services-apis-overview.md#naming-conventions)voor meer informatie.

### <a name="blobs"></a>Blobs

De namen van bestanden/blobs in een Asset moeten de [vereisten voor de BLOB-naam](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) en de [NTFS-naam](https://docs.microsoft.com/windows/win32/fileio/naming-a-file)volgen. De reden voor deze vereisten is dat de bestanden kunnen worden gekopieerd van Blob Storage naar een lokale NTFS-schijf voor verwerking.

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

## <a name="next-steps"></a>Volgende stappen

[Assets in Media Services beheren](manage-asset-concept.md)

## <a name="see-also"></a>Zie ook

[Verschillen tussen Media Services v2 en v3](migrate-from-v2-to-v3.md)
