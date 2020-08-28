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
ms.custom: devx-track-csharp
ms.openlocfilehash: 353024513885651cbc791548d8b91def3de65d90
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2020
ms.locfileid: "89013466"
---
# <a name="migrate-from-azure-media-encoder-to-media-encoder-standard"></a>Migreren van Azure Media Encoder naar Media Encoder Standard

In dit artikel worden de stappen beschreven voor het migreren van de verouderde Azure Media Encoder (AAM) media processor (die buiten gebruik wordt gesteld) naar de Media Encoder Standard-media processor. Voor de pensioen datums raadpleegt u dit onderwerp over [oudere onderdelen](legacy-components.md) .

Bij het coderen van bestanden met AAM gebruiken klanten meestal een teken reeks met een naam zoals `H264 Adaptive Bitrate MP4 Set 1080p` . Als u wilt migreren, moet uw code worden bijgewerkt om gebruik te kunnen maken van de **Media Encoder Standard** media processor in plaats van AAM en een van de equivalente [systeem voorinstellingen](media-services-mes-presets-overview.md) zoals `H264 Multiple Bitrate 1080p` . 

## <a name="migrating-to-media-encoder-standard"></a>Migreren naar Media Encoder Standard

Hier volgt een voor beeld van een C#-code die gebruikmaakt van de verouderde media processor. 

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

Media Encoder Standard is robuuster, betrouwbaarder, heeft betere prestaties en levert een betere kwaliteit van de uitvoer dan het oudere AAM-coderings programma. Daarnaast doet u het volgende: 

* Media Encoder Standard produceert uitvoer bestanden met een andere naam Conventie dan AAM.
* Media Encoder Standard produceert artefacten, zoals bestanden met de meta gegevens van het [invoer bestand](media-services-input-metadata-schema.md) en de [meta gegevens van het uitvoer bestand](media-services-output-metadata-schema.md).

## <a name="next-steps"></a>Volgende stappen

* [Verouderde onderdelen](legacy-components.md)
* [Pagina met prijzen](https://azure.microsoft.com/pricing/details/media-services/#encoding)
