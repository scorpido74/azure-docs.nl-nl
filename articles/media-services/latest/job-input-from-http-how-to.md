---
title: Een Azure Media Services taak invoer maken op basis van een HTTPS-URL | Microsoft Docs
description: In dit onderwerp ziet u hoe u een Azure Media Services taak invoer maakt op basis van een HTTPS-URL.
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
ms.openlocfilehash: 1ff2bc6d4bb4f7b983787b287fbb27e1c33cc179
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87022987"
---
# <a name="create-a-job-input-from-an-https-url"></a>Een taak invoer maken op basis van een HTTPS-URL

Wanneer u in Media Services v3 taken verzendt voor het verwerken van uw video's, moet u aan Media Services de locatie van de invoervideo doorgeven. Een van de opties is dat u een HTTPS-URL als taakinvoer opgeeft (zoals in dit voorbeeld). Op dit moment biedt AMS v3 geen ondersteuning voor gesegmenteerde overdrachtscodering via HTTPS-URL's. Voor een volledig voor beeld raadpleegt u dit [github voor beeld](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

> [!TIP]
> Controleer voordat u begint met het ontwikkelen [met behulp van Media Services v3 api's](media-services-apis-overview.md) (bevat informatie over het openen van api's, naam conventies, enzovoort).

## <a name="net-sample"></a>.NET-voor beeld

De volgende code laat zien hoe u een taak met een HTTPS-URL-invoer maakt.

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Foutcodes in taak

Zie [Foutcodes](/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Volgende stappen

[Een taak invoer maken op basis van een lokaal bestand](job-input-from-local-file-how-to.md).
