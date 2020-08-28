---
title: Media verwerking schalen door coderings eenheden toe te voegen-Azure |  Microsoft Docs
description: In dit artikel wordt beschreven hoe u coderings eenheden kunt toevoegen met Azure Media Services .NET.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 33f7625a-966a-4f06-bc09-bccd6e2a42b5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.reviewer: milangada
ms.custom: devx-track-csharp
ms.openlocfilehash: c9e99bcc1ca8681b9052bb9a25f18ea7e6f070c8
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2020
ms.locfileid: "89008655"
---
# <a name="how-to-scale-encoding-with-net-sdk"></a>Codering schalen met .NET SDK
> [!div class="op_single_selector"]
> * [Portal](media-services-portal-scale-media-processing.md)
> * [.NET](media-services-dotnet-encoding-units.md)
> * [REST](/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>Overzicht
> [!IMPORTANT]
> Controleer het [overzicht](media-services-scale-media-processing-overview.md) voor meer informatie over het schalen van media-verwerkingen.
> 
> 

Ga als volgt te werk om het gereserveerde eenheids type en het aantal gereserveerde encoding-eenheden te wijzigen met behulp van .NET SDK:

```csharp
IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
encodingS1ReservedUnit.Update();
Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);

encodingS1ReservedUnit.CurrentReservedUnits = 2;
encodingS1ReservedUnit.Update();

Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);
```

## <a name="opening-a-support-ticket"></a>Een ondersteunings ticket openen

Elke Media Services-account kan standaard worden geschaald naar Maxi maal 10 S2-of S3-media gereserveerde eenheden (MRUs) of 25 S1 MRUs en 5 gereserveerde on-demand streaming-eenheden. U kunt een hogere limiet aanvragen door een ondersteunings ticket te openen.

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
