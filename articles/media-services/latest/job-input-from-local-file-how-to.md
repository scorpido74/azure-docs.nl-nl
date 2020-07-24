---
title: Een Azure Media Services-taak invoer maken op basis van een lokaal bestand | Microsoft Docs
description: In dit artikel wordt beschreven hoe u een Azure Media Services taak invoer maakt op basis van een lokaal bestand.
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
ms.openlocfilehash: a635a75a64db59fc56c6890f79af967a58735853
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87001125"
---
# <a name="create-a-job-input-from-a-local-file"></a>Een taak invoer maken op basis van een lokaal bestand

Wanneer u in Media Services v3 taken verzendt voor het verwerken van uw video's, moet u aan Media Services de locatie van de invoervideo doorgeven. De invoer video kan worden opgeslagen als een media service-Asset. in dat geval kunt u een invoer element maken op basis van een bestand (lokaal opgeslagen of in Azure Blob-opslag). In dit onderwerp wordt beschreven hoe u een taak invoer maakt op basis van een lokaal bestand. Voor een volledig voor beeld raadpleegt u dit [github voor beeld](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs).

## <a name="prerequisites"></a>Vereisten 

* [Een Azure Media Services-account maken](./create-account-howto.md).
* [Beheer assets](manage-asset-concept.md)controleren.

## <a name="net-sample"></a>.NET-voor beeld

De volgende code laat zien hoe u een invoer element maakt en dit als invoer voor de taak gebruikt. De functie CreateInputAsset voert de volgende acties uit:

* Maakt de Asset
* Hiermee wordt een Beschrijf bare [SAS-URL](../../storage/common/storage-sas-overview.md) naar de container van het activum [in de opslag](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-a-container) opgehaald
* Uploadt het bestand naar de container in opslag met de SAS-URL

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

Met het volgende code fragment wordt een uitvoer element gemaakt als dit nog niet bestaat:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

Met het volgende code fragment wordt een coderings taak verzonden:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Foutcodes in taak

Zie [Foutcodes](/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Volgende stappen

[Een taak invoer maken op basis van een HTTPS-URL](job-input-from-http-how-to.md).
