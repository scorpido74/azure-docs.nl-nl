---
title: Migreren van Windows Azure Media Encoder naar Media Encoder Standard | Microsoft Documenten
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
ms.date: 10/17/2019
ms.author: juliako
ms.openlocfilehash: e75e3f3eecf6c34050aeaa7fe387fffb0de58a74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513198"
---
# <a name="migrate-from-windows-azure-media-encoder-to-media-encoder-standard"></a>Migreren van Windows Azure Media Encoder naar Media Encoder Standard

In dit artikel worden de stappen besproken voor het migreren van de verouderde Media Encoder (WAME)-mediaprocessor (die wordt verwijderd) naar de media-encoderstandaard-mediaprocessor van Media Encoder. Zie voor de pensioendata dit [onderwerp met oudere onderdelen.](legacy-components.md)

Bij het coderen van bestanden met WAME gebruikten `H264 Adaptive Bitrate MP4 Set 1080p`klanten meestal een benoemde voorinstellingstekenreeks zoals . Om te migreren, moet uw code worden bijgewerkt om de **Media Encoder Standard** mediaprocessor te gebruiken `H264 Multiple Bitrate 1080p`in plaats van WAME, en een van de gelijkwaardige [systeemvoorinstellingen](media-services-mes-presets-overview.md) zoals . 

## <a name="migrating-to-media-encoder-standard"></a>Migreren naar Media Encoder-standaard

Hier is een typisch C#-codevoorbeeld dat de verouderde component gebruikt. 

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("WAME Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Windows Azure Media Encoder"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Adaptive Bitrate MP4 Set 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Adaptive Bitrate MP4 Set 1080p", 
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

Als u uw eigen coderingsvoorinstelling voor WAME had gemaakt met behulp van het schema, is er een [gelijkwaardig schema voor Media Encoder Standard.](media-services-mes-schema.md)

## <a name="known-differences"></a>Bekende verschillen 

Media Encoder Standard is robuuster, betrouwbaarder, heeft betere prestaties en produceert een betere kwaliteit output dan de oude WAME encoder. Bovendien: 

* Media Encoder Standard produceert uitvoerbestanden met een andere naamgevingsconventie dan WAME.
* Media Encoder Standard produceert artefacten zoals bestanden met de [metagegevens van](media-services-input-metadata-schema.md) het invoerbestand en de [metagegevens van het uitvoerbestand(en).](media-services-output-metadata-schema.md)
* Zoals gedocumenteerd op de [prijspagina](https://azure.microsoft.com/pricing/details/media-services/#encoding) (vooral in de faq-sectie), wanneer u video's codeert met Media Encoder Standard, wordt u gefactureerd op basis van de duur van de bestanden die als uitvoer worden geproduceerd. Met WAME wordt u gefactureerd op basis van de grootte van het invoervideobestand(en) en het uitvoervideobestand(en).

## <a name="need-help"></a>Hebt u hulp nodig?

U een ondersteuningsticket openen door te navigeren naar [nieuw ondersteuningsverzoek](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-steps"></a>Volgende stappen

* [Verouderde onderdelen](legacy-components.md)
* [Pagina met prijzen](https://azure.microsoft.com/pricing/details/media-services/#encoding)
