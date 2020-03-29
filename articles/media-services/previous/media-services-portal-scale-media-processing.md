---
title: Mediaverwerking schalen met de Azure-portal | Microsoft Documenten
description: In deze zelfstudie u de stappen doorlopen die mediaverwerking kunnen schalen met behulp van de Azure-portal.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: e500f733-68aa-450c-b212-cf717c0d15da
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: c840764dc978a8dacb3450c0aca5e5d93284b8a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61127531"
---
# <a name="change-the-reserved-unit-type"></a>Het gereserveerde eenheidstype wijzigen
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Portal](media-services-portal-scale-media-processing.md)
> * [Rest](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [Php](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>Overzicht

Media Services-accounts worden gekoppeld aan een gereserveerde-eenheidstype, waarmee wordt bepaald hoe snel de mediaverwerkingstaken worden verwerkt. U kunt kiezen uit de volgende gereserveerde-eenheidstypen: **S1**, **S2** en **S3**. Een coderingstaak wordt bijvoorbeeld sneller uitgevoerd wanneer u het gereserveerde-eenheidstype **S2** gebruikt (in vergelijking met het type **S1**).

Naast het opgeven van het type gereserveerde eenheid, u opgeven om uw account in te richten bij **Gereserveerde eenheden** (RU's). Op basis van het aantal ingerichte RU's wordt bepaald hoeveel mediataken tegelijk kunnen worden verwerkt voor een bepaald account.

>[!NOTE]
>RU's werken voor het parallel verwerken van alle media, waaronder indexeringstaken via Azure Media Indexer. Indexeringstaken worden echter niet sneller verwerkt met snellere gereserveerde eenheden, terwijl dit bij coderen wel het geval is.

> [!IMPORTANT]
> Controleer het [overzichtsonderwerp](media-services-scale-media-processing-overview.md) om meer informatie te krijgen over het schalen van mediaverwerkingsonderwerp.
> 
> 

## <a name="scale-media-processing"></a>Mediaverwerking schalen
Ga als volgt te werk om het gereserveerde eenheidstype en het aantal gereserveerde eenheden te wijzigen:

1. Selecteer uw Azure Media Services-account in [Azure Portal](https://portal.azure.com/).
2. Selecteer **in** het venster Instellingen **de optie Media gereserveerde eenheden**.
   
    Als u het aantal gereserveerde eenheden voor het geselecteerde gereserveerde eenheidstype wilt wijzigen, gebruikt u de schuifregelaar **Media bediende eenheden** boven aan het scherm.
   
    Als u het **type gereserveerde eenheid wilt**wijzigen, klikt u op de balk Snelheid van **gereserveerde verwerkingseenheden.** Selecteer vervolgens de prijscategorie die u nodig hebt: S1, S2 of S3.
   
3. Klik op de knop OPSLAAN om uw wijzigingen op te slaan.
   
    De nieuwe gereserveerde eenheden worden toegewezen wanneer u op SAVE drukt.

## <a name="next-steps"></a>Volgende stappen
Media Services-leertrajecten bekijken.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

