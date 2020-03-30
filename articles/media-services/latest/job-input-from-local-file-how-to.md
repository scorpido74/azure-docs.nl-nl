---
title: Een Azure Media Services-taakinvoer maken uit een lokaal bestand | Microsoft Documenten
description: In dit artikel wordt uitgelegd hoe u een Azure Media Services-taakinvoer maakt vanuit een lokaal bestand.
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
ms.openlocfilehash: aba987ba232a29ffc240f72039b1e24bb87a2ed4
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80345912"
---
# <a name="create-a-job-input-from-a-local-file"></a>Een taakinvoer maken uit een lokaal bestand

Wanneer u in Media Services v3 taken verzendt voor het verwerken van uw video's, moet u aan Media Services de locatie van de invoervideo doorgeven. De invoervideo kan worden opgeslagen als Media Service-asset, in welk geval u een invoerasset maakt op basis van een bestand (lokaal opgeslagen of in Azure Blob-opslag). In dit onderwerp ziet u hoe u een taakinvoer maakt uit een lokaal bestand. Zie dit [GitHub-voorbeeld](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs)voor een volledig voorbeeld.

## <a name="prerequisites"></a>Vereisten 

* [Een Azure Media Services-account maken](create-account-cli-how-to.md).
* Beheervan [activa controleren](manage-asset-concept.md).

## <a name="net-sample"></a>.NET-monster

In de volgende code ziet u hoe u een invoerelement maakt en deze gebruikt als invoer voor de taak. Met de functie CreateInputAsset worden de volgende acties uitgevoerd:

* Maakt het item
* Hiermee krijgt u een beschrijfbare [SAS-URL](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) naar de container van het asset [in opslag](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#upload-blobs-to-a-container)
* Uploadt het bestand naar de container in opslag met de SAS-URL

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

In het volgende codefragment wordt een uitvoeritem gemaakt als deze nog niet bestaat:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

In het volgende codefragment wordt een coderingstaak verzonden:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Foutcodes in taak

Zie [Foutcodes](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Volgende stappen

[Een taakinvoer maken op basis van een HTTPS-URL](job-input-from-http-how-to.md).
