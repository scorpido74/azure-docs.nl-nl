---
title: Migreren van Azure Media Encoder naar Media Encoder Standard | Microsoft Documenten
description: In dit onderwerp wordt besproken hoe u migreren van Azure Media Encoder naar de mediaprocessor Media Encoder Standard.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2019
ms.author: juliako
ms.openlocfilehash: f8fe1b13db6473e80f0d7cdc638b775a0c8062c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513498"
---
# <a name="migrate-from-azure-media-encoder-to-media-encoder-standard"></a>Migreren van Azure Media Encoder naar Media Encoder Standard

In dit artikel worden de stappen besproken voor het migreren van de verouderde Azure Media Encoder (AME) mediaprocessor (die wordt verwijderd) naar de Media Encoder Standard-mediaprocessor. Zie voor de pensioendata dit [onderwerp met oudere onderdelen.](legacy-components.md)

Bij het coderen van bestanden met AME gebruikten `H264 Adaptive Bitrate MP4 Set 1080p`klanten meestal een benoemde voorinstellingstekenreeks zoals . Om te migreren, moet uw code worden bijgewerkt om de **Media Encoder Standard** mediaprocessor te gebruiken `H264 Multiple Bitrate 1080p`in plaats van AME, en een van de gelijkwaardige [systeemvoorinstellingen](media-services-mes-presets-overview.md) zoals . 

## <a name="migrating-to-media-encoder-standard"></a>Migreren naar Media Encoder-standaard

Hier is een typisch C#-codevoorbeeld dat de verouderde mediaprocessor gebruikt. 

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("AME Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Adaptive Bitrate MP4 Set 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    " H264 Adaptive Bitrate MP4 Set 1080p", 
    TaskOptions.None); 
```

Hier is de bijgewerkte versie die Media Encoder Standard gebruikt.

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("Media Encoder Standard Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Multiple Bitrate 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Multiple Bitrate 1080p", 
    TaskOptions.None); 
```

### <a name="advanced-scenarios"></a>Geavanceerde scenario's 

Als u uw eigen coderingsvoorinstelling voor AME had gemaakt met behulp van het schema, is er een [gelijkwaardig schema voor Media Encoder Standard.](media-services-mes-schema.md) Als u vragen heeft over hoe u de oudere instellingen in kaart brengen aan de nieuwe encoder, neem dan contact met ons op viamailto:amshelp@microsoft.com  
## <a name="known-differences"></a>Bekende verschillen 

Media Encoder Standard is robuuster, betrouwbaarder, heeft betere prestaties en produceert een betere kwaliteit output dan de oude AME encoder. Daarnaast doet u het volgende: 

* Media Encoder Standard produceert uitvoerbestanden met een andere naamgevingsconventie dan AME.
* Media Encoder Standard produceert artefacten zoals bestanden met de [metagegevens van](media-services-input-metadata-schema.md) het invoerbestand en de [metagegevens van het uitvoerbestand(en).](media-services-output-metadata-schema.md)

## <a name="next-steps"></a>Volgende stappen

* [Verouderde onderdelen](legacy-components.md)
* [Pagina met prijzen](https://azure.microsoft.com/pricing/details/media-services/#encoding)
