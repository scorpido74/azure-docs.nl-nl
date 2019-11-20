---
title: Migreren van Azure Media Encoder naar Media Encoder Standard | Microsoft Docs
description: In dit onderwerp wordt beschreven hoe u migreert van Azure Media Encoder naar de Media Encoder Standard-media processor.
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
ms.openlocfilehash: d2ed1d5e0cf0e42c3f916ab33f860039b5d5f781
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196465"
---
# <a name="migrate-from-azure-media-encoder-to-media-encoder-standard"></a>Migreren van Azure Media Encoder naar Media Encoder Standard

In dit artikel worden de stappen beschreven voor het migreren van de verouderde media processor van Azure Media Encoder (AAM), die wordt buiten gebruik gesteld op 30 november 2019, naar de Media Encoder Standard-media processor.  

Bij het coderen van bestanden met AAM gebruiken klanten doorgaans een vooraf ingestelde teken reeks, zoals `H264 Adaptive Bitrate MP4 Set 1080p`. Als u wilt migreren, moet uw code worden bijgewerkt om gebruik te kunnen maken van de **Media Encoder Standard** media processor in plaats van AAM en een van de equivalente [systeem voorinstellingen](media-services-mes-presets-overview.md) zoals `H264 Multiple Bitrate 1080p`. 

## <a name="migrating-to-media-encoder-standard"></a>Migreren naar Media Encoder Standard

Hier volgt een typisch C# code voorbeeld dat gebruikmaakt van de verouderde media processor. 

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

Dit is de bijgewerkte versie die gebruikmaakt van Media Encoder Standard.

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

Als u uw eigen coderings voorinstelling voor AAM hebt gemaakt met behulp van het bijbehorende schema, is er een [gelijkwaardig schema voor Media Encoder Standard](media-services-mes-schema.md). Als u vragen hebt over het toewijzen van de oudere instellingen aan de nieuwe encoder, kunt u contact met ons opnemen via mailto:amshelp@microsoft.com  
## <a name="known-differences"></a>Bekende verschillen 

Media Encoder Standard is robuuster, betrouwbaarder, heeft betere prestaties en levert een betere kwaliteit van de uitvoer dan het oudere AAM-coderings programma. Daarnaast: 

* Media Encoder Standard produceert uitvoer bestanden met een andere naam Conventie dan AAM.
* Media Encoder Standard produceert artefacten, zoals bestanden met de meta gegevens van het [invoer bestand](media-services-input-metadata-schema.md) en de [meta gegevens van het uitvoer bestand](media-services-output-metadata-schema.md).

## <a name="next-steps"></a>Volgende stappen

* [Verouderde onderdelen](legacy-components.md)
* [Pagina met prijzen](https://azure.microsoft.com/pricing/details/media-services/#encoding)
