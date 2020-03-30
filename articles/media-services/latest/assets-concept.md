---
title: Assets
titleSuffix: Azure Media Services
description: Meer informatie over wat assets zijn en hoe ze worden gebruikt door Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/09/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 9b04941a5799955097fbd54ad9bdf50eccb87541
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087909"
---
# <a name="assets-in-azure-media-services-v3"></a>Assets in Azure Media Services v3

In Azure Media Services is een [asset](https://docs.microsoft.com/rest/api/media/assets) een kernconcept. Het is waar je media invoert (bijvoorbeeld via upload of live inname), outputmedia (vanuit een joboutput) en media publiceert van (voor streaming). 

Een asset wordt toegewezen aan een blobcontainer in het [Azure Storage-account](storage-account-concept.md) en de bestanden in het actief worden opgeslagen als blokblobs in die container. Elementen bevatten informatie over digitale bestanden die zijn opgeslagen in Azure Storage (waaronder video, audio, afbeeldingen, miniatuurverzamelingen, teksttracks en bestanden met ondertiteling).

Media Services ondersteunt Blob-lagen wanneer het account GPv2-opslag (GPv2) gebruikt voor Algemene doeleinden. Met GPv2 u bestanden verplaatsen naar [Cool- of Archive-opslag.](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) **Archiefopslag** is geschikt voor het archiveren van bronbestanden wanneer deze niet meer nodig zijn (bijvoorbeeld nadat ze zijn gecodeerd).

De **Archive** archiefopslaglaag wordt alleen aanbevolen voor zeer grote bronbestanden die al zijn gecodeerd en de taakuitvoer voor codering is in een uitvoerblobcontainer geplaatst. De blobs in de uitvoercontainer die u aan een actief wilt koppelen en gebruiken om uw inhoud te streamen of te analyseren, moeten in een **opslaglaag hot** of **cool** bestaan.

## <a name="naming"></a>Naamgeving 

### <a name="assets"></a>Assets

De namen van activa moeten uniek zijn. Media Services v3-resourcenamen (bijvoorbeeld Assets, Jobs, Transforms) zijn onderhevig aan naamgevingsbeperkingen voor Azure Resource Manager. Zie [Naamgevingsconventies voor](media-services-apis-overview.md#naming-conventions)meer informatie.

### <a name="blobs"></a>Blobs

De namen van bestanden/blobs binnen een asset moeten zowel de [vereisten voor blobnamen](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) als de [NTFS-naamvereisten](https://docs.microsoft.com/windows/win32/fileio/naming-a-file)volgen. De reden voor deze vereisten is dat de bestanden kunnen worden gekopieerd van blob-opslag naar een lokale NTFS-schijf voor verwerking.

## <a name="next-steps"></a>Volgende stappen

[Activa beheren in Media Services](manage-asset-concept.md)

## <a name="see-also"></a>Zie ook

[Verschillen tussen Media Services v2 en v3](migrate-from-v2-to-v3.md)
