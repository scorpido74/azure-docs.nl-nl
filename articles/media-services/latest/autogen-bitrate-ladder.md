---
title: Video's coderen met Standard Encoder in Media Services - Azure | Microsoft Documenten
description: In dit onderwerp wordt uitgelegd hoe u de standaardencoder in Media Services gebruiken om een invoervideo te coderen met een automatisch gegenereerde bitrate-ladder, op basis van de invoerresolutie en bitrate.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: c25c32f35adc1c017f0f4c012c82bd7e0af8d452
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60733314"
---
#  <a name="encode-with-an-auto-generated-bitrate-ladder"></a>Coderen met een automatisch gegenereerde bitrate-ladder

## <a name="overview"></a>Overzicht

In dit artikel wordt uitgelegd hoe u de standaardencoder in Media Services gebruiken om een invoervideo te coderen in een automatisch gegenereerde bitrateladder (bitrate-resolutieparen) op basis van de invoerresolutie en bitrate. Deze ingebouwde encoderinstelling, of vooraf ingestelde, zal nooit hoger zijn dan de invoerresolutie en bitrate. Als de ingang bijvoorbeeld 720p bij 3 Mbps is, blijft de uitvoer op zijn best 720p en begint het met snelheden lager dan 3 Mbps.

### <a name="encoding-for-streaming"></a>Codering voor streaming

Wanneer u de **AdaptiveStreaming-voorinstelling** in **Transform**gebruikt, krijgt u een uitvoer die geschikt is voor levering via streamingprotocollen zoals HLS en DASH. Bij het gebruik van deze voorinstelling bepaalt de service op intelligente wijze hoeveel videolagen moeten worden gegenereerd en met welke bitrate en resolutie. De uitvoerinhoud bevat MP4-bestanden waarbij AAC-gecodeerde audio en H.264-gecodeerde video niet worden interleaved.

Zie [Een bestand streamen](stream-files-dotnet-quickstart.md)als u een voorbeeld wilt zien van de manier waarop deze voorinstelling wordt gebruikt.

## <a name="output"></a>Uitvoer

In deze sectie worden drie voorbeelden weergegeven van de uitvoervideolagen die door de encoder van Media Services zijn geproduceerd als gevolg van codering met de **voorinstelling AdaptiveStreaming.** In alle gevallen bevat de uitvoer een mp4-audiobestand met stereogeluid gecodeerd op 128 kbps.

### <a name="example-1"></a>Voorbeeld 1
Bron met hoogte "1080" en framerate "29.970" produceert 6 videolagen:

|Laag|Height|Breedte|Bitrate (kbps)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Voorbeeld 2
Bron met hoogte "720" en framerate "23.970" produceert 5 videolagen:

|Laag|Height|Breedte|Bitrate (kbps)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Voorbeeld 3
Bron met hoogte "360" en framerate "29.970" produceert 3 videolagen:

|Laag|Height|Breedte|Bitrate (kbps)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een bestand streamen](stream-files-dotnet-quickstart.md)
