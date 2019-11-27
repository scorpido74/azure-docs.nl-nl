---
title: Migreren van Windows Azure Media Encoder naar Media Encoder Standard | Microsoft Docs
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
ms.date: 10/17/2019
ms.author: juliako
ms.openlocfilehash: 862643cb4eb26d7d88aa81d05433066a927a69aa
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2019
ms.locfileid: "74424038"
---
# <a name="migrate-from-windows-azure-media-encoder-to-media-encoder-standard"></a>Migreren van Windows Azure Media Encoder naar Media Encoder Standard

In dit artikel worden de stappen beschreven voor het migreren van de verouderde Windows Azure Media Encoder (WAME)-media processor, die wordt ingetrokken op 1 maart 2020, naar de Media Encoder Standard-media processor.

Bij het coderen van bestanden met WAME gebruikt klanten doorgaans een vooraf ingestelde teken reeks, zoals `H264 Adaptive Bitrate MP4 Set 1080p`. Als u wilt migreren, moet uw code worden bijgewerkt om gebruik te kunnen maken van de **Media Encoder Standard** media processor in plaats van WAME, en een van de equivalente [systeem instellingen](media-services-mes-presets-overview.md) zoals `H264 Multiple Bitrate 1080p`. 

## <a name="migrating-to-media-encoder-standard"></a>Migreren naar Media Encoder Standard

Hier volgt een typisch C# voor beeld van code dat gebruikmaakt van het verouderde onderdeel. 

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

Als u uw eigen coderings voorinstelling voor WAME hebt gemaakt met behulp van het bijbehorende schema, is er een [gelijkwaardig schema voor Media Encoder Standard](media-services-mes-schema.md).

## <a name="known-differences"></a>Bekende verschillen 

Media Encoder Standard is robuuster, betrouwbaarder, heeft betere prestaties en produceert een betere kwaliteit van de uitvoer dan het oudere WAME-coderings programma. Bovendien: 

* Media Encoder Standard produceert uitvoer bestanden met een andere naamgevings Conventie dan WAME.
* Media Encoder Standard produceert artefacten, zoals bestanden met de meta gegevens van het [invoer bestand](media-services-input-metadata-schema.md) en de [meta gegevens van het uitvoer bestand](media-services-output-metadata-schema.md).
* Zoals is beschreven op de [pagina met prijzen](https://azure.microsoft.com/pricing/details/media-services/#encoding) (met name in het gedeelte Veelgestelde vragen), wordt u gefactureerd op basis van de duur van de bestanden die worden geproduceerd als uitvoer wanneer u Video's codeert met behulp van Media Encoder Standard. Met WAME wordt u gefactureerd op basis van de grootte van de invoer video bestanden en video bestand (en).

## <a name="need-help"></a>Hulp nodig?

U kunt een ondersteunings ticket openen door te navigeren naar de [nieuwe ondersteunings aanvraag](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-steps"></a>Volgende stappen

* [Verouderde onderdelen](legacy-components.md)
* [Pagina met prijzen](https://azure.microsoft.com/pricing/details/media-services/#encoding)
