---
title: Streamingeindpunten schalen met de Azure-portal | Microsoft Documenten
description: In deze zelfstudie u de stappen doorlopen van het schalen van streamingeindpunten met de Azure-portal.
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
ms.openlocfilehash: 23eb51428dcf4961febfb592bf957bb8beeeda57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61463108"
---
# <a name="scale-streaming-endpoints-with-the-azure-portal"></a>Streaming-eindpunten schalen met Azure Portal
## <a name="overview"></a>Overzicht

> [!NOTE]
> U hebt een Azure-account nodig om deze zelfstudie te voltooien. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie. 
> 
> 

**Premium**-streaming-eindpunten zijn geschikt voor geavanceerde workloads omdat er gebruik wordt gemaakt van toegewezen, schaalbare bandbreedtecapaciteit. Klanten met een **Premium**-streaming-eindpunt krijgen standaard één streaming-eenheid (SU). Het streaming-eindpunt kan worden geschaald door SU's toe te voegen. Elke SU biedt extra bandbreedtecapaciteit voor de toepassing. Zie het [onderwerp Streaming Endpoint-overzicht](media-services-streaming-endpoints-overview.md) voor meer informatie over streaming endpoint-typen en CDN-configuratie.
 
In dit onderwerp wordt uitgelegd hoe u een streamingeindpunt schaalt.

Zie [Media Services Pricing Details](https://go.microsoft.com/fwlink/?LinkId=275107) (Informatie over Media Services-prijzen) voor informatie over prijzen.

## <a name="scale-streaming-endpoints"></a>Streaming-eindpunten schalen

Ga als volgt te werk om het aantal streaming-eenheden te wijzigen:

1. Selecteer uw Azure Media Services-account in [Azure Portal](https://portal.azure.com/).
2. Selecteer **In** het venster Instellingen de optie **Streaming-eindpunten**.
3. Klik op het streamingeindpunt dat u wilt schalen. 

    > [!NOTE] 
    > Je alleen **Premium** streaming endpoints schalen.

4. Verplaats de schuifregelaar om het aantal streaming-eenheden op te geven.

    ![Streaming-eindpunt](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

## <a name="next-steps"></a>Volgende stappen
Media Services-leertrajecten bekijken.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

