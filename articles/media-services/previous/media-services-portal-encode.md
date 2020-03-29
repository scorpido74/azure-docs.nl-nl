---
title: Een asset coderen met Media Encoder Standard in de Azure-portal | Microsoft Documenten
description: In deze zelfstudie u de stappen doorlopen waarop een asset wordt gecodeerd met Behulp van Media Encoder Standard in de Azure-portal.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 107d9e9a-71e9-43e5-b17c-6e00983aceab
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 7c147d99eaabee70316521d1f2bdc41933162ab1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69542603"
---
# <a name="encode-an-asset-by-using-media-encoder-standard-in-the-azure-portal"></a>Een asset coderen met behulp van Media Encoder Standard in de Azure-portal

> [!NOTE]
> U hebt een Azure-account nodig om deze zelfstudie te voltooien. Zie gratis [proefversie van Azure voor](https://azure.microsoft.com/pricing/free-trial/)meer informatie. 
> 
> 

Een van de meest voorkomende scenario's in het werken met Azure Media Services is het leveren van adaptieve bitrate streaming aan uw klanten. Media Services ondersteunt de volgende adaptieve bitrate streaming technologieën: Apple HTTP Live Streaming (HLS), Microsoft Smooth Streaming, en Dynamic Adaptive Streaming over HTTP (DASH, ook wel MPEG-DASH). Om je video's voor te bereiden op adaptieve bitrate streaming, codeer je eerst je bronvideo als multi-bitrate bestanden. Je Media Encoder Standard gebruiken om je video's te coderen.  

Media Services biedt dynamische pakketten. Met dynamische verpakkingen u uw multibitrate MP4's leveren in HLS, Smooth Streaming en MPEG-DASH, zonder opnieuw te verpakken in deze streamingformaten. Wanneer u dynamische verpakkingen gebruikt, u de bestanden opslaan en betalen in single-storage-indeling. Media Services bouwt en bedient de juiste respons op basis van de aanvraag van een klant.

U moet het bronbestand coderen in een set multi-bitrate MP4-bestanden om van dynamische pakketten gebruik te maken. De coderingsstappen worden later in dit artikel gedemonstreerd.

Zie [Mediaverwerking schalen met behulp van de Azure-portal](media-services-portal-scale-media-processing.md)voor meer informatie over het schalen van mediaverwerking.

## <a name="encode-in-the-azure-portal"></a>Coderen in de Azure-portal

Ga als het gaat om het coderen van uw inhoud met Media Encoder Standard:

1. Selecteer uw Azure Media Services-account in [Azure Portal](https://portal.azure.com/).
2. Selecteer **Instellingen** > **-middelen**. Selecteer de asset die u wilt coderen.
3. Selecteer de knop **Coderen**.
4. Selecteer in het deelvenster **Een asset coderen** de processor **Media Encoder Standard** en een standaardinstelling. Zie [automatisch een bitrate ladder genereren](media-services-autogen-bitrate-ladder-with-mes.md) en [Standaardinstellingen voor taken in Media Encoder Standard](media-services-mes-presets-overview.md) voor informatie over standaardinstellingen. Het is belangrijk om de standaardinstelling te kiezen die het meest geschikt is voor uw invoervideo. Als u bijvoorbeeld weet dat uw invoervideo een resolutie van 1920 x 1080 pixels heeft, kunt u de standaardinstelling **H264 Multiple Bitrate 1080p** gebruiken. Als u een video met lage resolutie hebt (640 x 360), kunt u beter niet de standaardinstelling **H264 Multiple Bitrate 1080p** gebruiken.
   
   U kunt de naam van de uitvoerasset en de naam van de taak bewerken, zodat u uw resources beter kunt beheren.
   
   ![Assets coderen](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Selecteer **Maken**.

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Volgende stappen
* [Houd de voortgang van uw coderingstaak](media-services-portal-check-job-progress.md) in de Azure-portal in de gaten.  

