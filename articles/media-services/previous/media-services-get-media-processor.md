---
title: Een mediaprocessor maken met de Azure Media Services SDK voor .NET| Microsoft Documenten
description: Meer informatie over het maken van een mediaprocessorcomponent om media-inhoud voor Azure Media Services te coderen, te converteren, te versleutelen of te decoderen. Codevoorbeelden zijn geschreven in C# en gebruiken de Media Services SDK voor .NET.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: dbf9496f-c6f0-42a7-aa36-70f89dcb8ea2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: af6badda426f1bb81d8528cfda9b8c02d55712b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61463829"
---
# <a name="how-to-get-a-media-processor-instance"></a>How to: Een mediaprocessor-exemplaar krijgen
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [Rest](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Overzicht
In Media Services is een mediaprocessor een onderdeel dat een specifieke verwerkingstaak verwerkt, zoals het coderen, converteren van formaten, versleutelen of decoderen van media-inhoud. U maakt meestal een mediaprocessor wanneer u een taak maakt om de indeling van media-inhoud te coderen, te versleutelen of te converteren.

## <a name="azure-media-processors"></a>Azure-mediaprocessors 

In het volgende onderwerp worden lijsten met mediaprocessors weergegeven:

* [Mediaprocessors coderen](scenarios-and-availability.md#encoding-media-processors)
* [Mediaprocessors voor analyse](scenarios-and-availability.md#analytics-media-processors)

## <a name="get-media-processor"></a>Mediaprocessor krijgen

De volgende methode laat zien hoe u een mediaprocessorexemplaar krijgt. In het codevoorbeeld wordt uitgegaan van het gebruik van een variabele op moduleniveau met de naam **_context** om te verwijzen naar de servercontext zoals beschreven in de sectie [Hoe: Maak programmatisch verbinding met MediaServices](media-services-use-aad-auth-to-access-ams-api.md).

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }


## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Volgende stappen
Nu u weet hoe u een mediaprocessorexemplaar krijgen, gaat u naar het onderwerp [Hoe u een asset-onderwerp](media-services-dotnet-encode-with-media-encoder-standard.md) coderen, waarmee u laten zien hoe u de Media Encoder-standaard gebruiken om een actief te coderen.

