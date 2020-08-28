---
title: Een media processor maken met behulp van de Azure Media Services SDK voor .NET | Microsoft Docs
description: Meer informatie over het maken van een media processor onderdeel voor het coderen, converteren van de indeling, het versleutelen of ontsleutelen van media-inhoud voor Azure Media Services. Code voorbeelden zijn geschreven in C# en gebruiken de Media Services SDK voor .NET.
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
ms.custom: devx-track-csharp
ms.openlocfilehash: cc0292e908c09f07bd7495ba4f18cc1c29076a49
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2020
ms.locfileid: "89009080"
---
# <a name="how-to-get-a-media-processor-instance"></a>Procedure: een media processor-exemplaar ophalen
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Overzicht
In Media Services een media processor een onderdeel dat een specifieke verwerkings taak verwerkt, zoals code ring, indelings conversie, versleuteling of ontsleuteling van media-inhoud. Doorgaans maakt u een media processor wanneer u een taak maakt voor het coderen, versleutelen of converteren van de indeling van media-inhoud.

## <a name="azure-media-processors"></a>Azure media-processors 

Het volgende onderwerp bevat een lijst met media processors:

* [Mediaprocessors coderen](scenarios-and-availability.md#encoding-media-processors)
* [Mediaprocessors voor analyse](scenarios-and-availability.md#analytics-media-processors)

## <a name="get-media-processor"></a>Media processor ophalen

De volgende methode laat zien hoe u een exemplaar van een media processor kunt ophalen. In het voorbeeld code wordt ervan uitgegaan dat het gebruik van een variabele op module niveau met de naam **_context** wordt gebruikt om te verwijzen naar de server context, zoals beschreven in de sectie [: verbinding maken met Media Services via een programma](media-services-use-aad-auth-to-access-ams-api.md).

```csharp
private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
{
    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

    if (processor == null)
    throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

    return processor;
}
```


## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Volgende stappen
Nu u weet hoe u een exemplaar van een media processor krijgt, gaat u naar het onderwerp [een Asset coderen](media-services-dotnet-encode-with-media-encoder-standard.md) . hier wordt uitgelegd hoe u de Media Encoder Standard kunt gebruiken om een Asset te coderen.

