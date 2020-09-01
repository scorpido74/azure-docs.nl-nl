---
title: Streaming-eind punten schalen met de Azure Portal | Microsoft Docs
description: In deze zelf studie wordt u begeleid bij de stappen voor het schalen van streaming-eind punten met de Azure Portal.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 1008b3a3-2fa1-4146-85bd-2cf43cd1e00e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 814d6b55e5ae04bf27dfb70d4e7b5ea2d5a4315c
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89266813"
---
# <a name="scale-streaming-endpoints-with-the-azure-portal"></a>Streaming-eindpunten schalen met Azure Portal

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Overzicht

> [!NOTE]
> U hebt een Azure-account nodig om deze zelfstudie te voltooien. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie. 
> 
> 

**Premium**-streaming-eindpunten zijn geschikt voor geavanceerde workloads omdat er gebruik wordt gemaakt van toegewezen, schaalbare bandbreedtecapaciteit. Klanten met een **Premium**-streaming-eindpunt krijgen standaard één streaming-eenheid (SU). Het streaming-eindpunt kan worden geschaald door SU's toe te voegen. Elke SU biedt extra bandbreedtecapaciteit voor de toepassing. Zie het onderwerp [overzicht van streaming-eind punten](media-services-streaming-endpoints-overview.md) voor meer informatie over streaming-eindpunt typen en CDN-configuratie.
 
In dit onderwerp wordt uitgelegd hoe u een streaming-eind punt kunt schalen.

Zie [Media Services Pricing Details](https://azure.microsoft.com/pricing/details/media-services/) (Informatie over Media Services-prijzen) voor informatie over prijzen.

## <a name="scale-streaming-endpoints"></a>Streaming-eind punten schalen

Ga als volgt te werk om het aantal streaming-eenheden te wijzigen:

1. Selecteer uw Azure Media Services-account in [Azure Portal](https://portal.azure.com/).
2. Selecteer in het venster **instellingen** **streaming-eind punten**.
3. Klik op het streaming-eind punt dat u wilt schalen. 

    > [!NOTE] 
    > U kunt alleen **Premium** streaming-eind punten schalen.

4. Verplaats de schuif regelaar om het aantal streaming-eenheden op te geven.

    ![Streaming-eindpunt](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

## <a name="next-steps"></a>Volgende stappen
Media Services-leertrajecten bekijken.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

