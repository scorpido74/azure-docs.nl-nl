---
title: Een Azure Media Services-taak invoer maken op basis van een lokaal bestand | Microsoft Docs
description: In dit onderwerp wordt beschreven hoe u een taak invoer maakt op basis van een lokaal bestand.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/18/2019
ms.author: juliako
ms.openlocfilehash: 94ac332867d46deb426672fbc40cc72cc121fe64
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/23/2019
ms.locfileid: "69998138"
---
# <a name="create-a-job-input-from-a-local-file"></a>Een taak invoer maken op basis van een lokaal bestand

Wanneer u in Media Services v3 taken verzendt voor het verwerken van uw video's, moet u aan Media Services de locatie van de invoervideo doorgeven. De invoer video kan worden opgeslagen als een media service-Asset. in dat geval kunt u een invoer element maken op basis van een bestand (lokaal opgeslagen of in Azure Blob-opslag). In dit onderwerp wordt beschreven hoe u een taak invoer maakt op basis van een lokaal bestand. Voor een volledig voor beeld raadpleegt u dit [github voor beeld](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>.NET-voor beeld

De volgende code laat zien hoe u een invoer element maakt en dit als invoer voor de taak gebruikt. De functie CreateInputAsset voert de volgende acties uit:

* Maakt de Asset
* Haalt een beschrijfbare [SAS-URL](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) voor de [container in opslag](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#upload-blobs-to-a-container) van de asset
* Uploadt het bestand naar de container in opslag met de SAS-URL

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

Met het volgende code fragment wordt een uitvoer element gemaakt als dit nog niet bestaat:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAssetAsync)]

Met het volgende code fragment wordt een coderings taak verzonden:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Foutcodes in taak

Zie [Foutcodes](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Volgende stappen

[Een taak invoer maken op basis van een HTTPS-URL](job-input-from-http-how-to.md).
