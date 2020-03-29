---
title: Een Azure Media Services-taakinvoer maken op basis van een HTTPS-URL | Microsoft Documenten
description: In dit onderwerp wordt uitgelegd hoe u een Azure Media Services-taakinvoer maakt op basis van een HTTPS-URL.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/13/2019
ms.author: juliako
ms.openlocfilehash: a3d4568dd237491f28ae2880bdd78dd236870c3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74899831"
---
# <a name="create-a-job-input-from-an-https-url"></a>Een taakinvoer maken op basis van een HTTPS-URL

Wanneer u in Media Services v3 taken verzendt voor het verwerken van uw video's, moet u aan Media Services de locatie van de invoervideo doorgeven. Een van de opties is dat u een HTTPS-URL als taakinvoer opgeeft (zoals in dit voorbeeld). Op dit moment biedt AMS v3 geen ondersteuning voor gesegmenteerde overdrachtscodering via HTTPS-URL's. Zie dit [GitHub-voorbeeld](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)voor een volledig voorbeeld.

> [!TIP]
> Voordat u begint met het ontwikkelen, bekijkt [u Ontwikkelen met Media Services v3 API's](media-services-apis-overview.md) (inclusief informatie over toegang tot API's, naamgevingsconventies, enz.)

## <a name="net-sample"></a>.NET-monster

In de volgende code ziet u hoe u een taak maakt met een HTTPS-URL-invoer.

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Foutcodes in taak

Zie [Foutcodes](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Volgende stappen

[Maak een taakinvoer uit een lokaal bestand](job-input-from-local-file-how-to.md).
