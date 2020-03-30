---
title: Aangepaste transformatie coderen met Media Services v3 .NET - Azure | Microsoft Documenten
description: In dit onderwerp wordt uitgelegd hoe u Azure Media Services v3 gebruiken om een aangepaste transformatie te coderen met .NET.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/03/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: ebe701032e6416b3e007a28db62f5a8235bb1bb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80068035"
---
# <a name="how-to-encode-with-a-custom-transform---net"></a>Hoe te coderen met een aangepaste transformatie - .NET

Wanneer u codeert met Azure Media Services, u snel aan de slag met een van de aanbevolen ingebouwde voorinstellingen op basis van aanbevolen procedures in de branche, zoals aangetoond in de zelfstudie [voor streamingbestanden.](stream-files-tutorial-with-api.md) U ook een aangepaste voorinstelling maken om uw specifieke scenario of apparaatvereisten te targeten.

## <a name="considerations"></a>Overwegingen

Bij het maken van aangepaste voorinstellingen zijn de volgende overwegingen van toepassing:

* Alle waarden voor hoogte en breedte op AVC-inhoud moeten een veelvoud van 4 zijn.
* In Azure Media Services v3 zijn alle coderingsbitrates in bits per seconde. Dit is anders dan de presets met onze v2 API's, die kilobits per seconde als eenheid gebruikten. Als de bitrate in v2 bijvoorbeeld is opgegeven als 128 (kilobits/seconde), wordt deze in v3 ingesteld op 128000 (bits/seconde).

## <a name="prerequisites"></a>Vereisten 

[Een Media Services-account maken](create-account-cli-how-to.md)

## <a name="download-the-sample"></a>Het voorbeeld downloaden

Kloon een GitHub-repository die het volledige .NET Core-voorbeeld bevat naar uw machine met de volgende opdracht:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
Het aangepaste voorinstellingsvoorbeeld bevindt zich in de map [EncodeCustomTransform.](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/)

## <a name="create-a-transform-with-a-custom-preset"></a>Een transformatie maken met een aangepaste voorinstelling 

Wanneer u een nieuwe [transformatie maakt,](https://docs.microsoft.com/rest/api/media/transforms)moet u opgeven wat u wilt dat deze als uitvoer produceert. De vereiste parameter is een [TransformOutput](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#transformoutput)-object, zoals weergegeven in de onderstaande code. Elke **transformatie-uitvoer** bevat een **voorinstelling**. De **voorinstelling** beschrijft de stapsgewijze instructies van video- en/of audioverwerkingsbewerkingen die moeten worden gebruikt om de gewenste **TransformOutput**te genereren. Met de volgende **TransformOutput** worden aangepaste codec- en laaguitvoerinstellingen gemaakt.

Bij het maken van een [transformatie](https://docs.microsoft.com/rest/api/media/transforms) moet u controleren of er al een bestaat met de methode **Ophalen** zoals weergegeven in de volgende code. In Media Services v3 **retourneer methoden** voor entiteiten **null** als de entiteit niet bestaat (een hoofdletter-ingevoelige controle op de naam).

### <a name="example"></a>Voorbeeld

In het volgende voorbeeld wordt een reeks uitvoer gedefinieerd die we willen genereren wanneer deze transformatie wordt gebruikt. We voegen eerst een AacAudio-laag toe voor de audiocodering en twee H264Video-lagen voor de videocodering. In de videolagen wijzen we labels toe, zodat ze kunnen worden gebruikt in de namen van uitvoerbestanden. Vervolgens willen we dat de uitvoer ook miniaturen bevat. In het onderstaande voorbeeld geven we afbeeldingen op in PNG-indeling, gegenereerd bij 50% van de resolutie van de invoervideo, en bij drie tijdstempels - {25%, 50%, 75} van de lengte van de invoervideo. Ten slotte specificeren we het formaat voor de uitvoerbestanden - een voor video + audio en een andere voor de miniaturen. Aangezien we meerdere H264Layers hebben, moeten we macro's gebruiken die unieke namen per laag produceren. We kunnen een `{Label}` `{Bitrate}` of macro gebruiken, het voorbeeld toont het eerste.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>Volgende stappen

[Bestanden streamen](stream-files-tutorial-with-api.md) 
