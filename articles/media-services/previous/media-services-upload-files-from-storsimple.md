---
title: Bestanden vanuit Azure StorSimple uploaden naar een Azure Media Services-account | Microsoft Docs
description: Dit artikel geeft een kort overzicht van Azure StorSimple Data Manager. Het artikel bevat ook koppelingen naar zelfstudies die laten zien hoe u gegevens extraheert uit StorSimple en deze vervolgens als assets uploadt naar een Azure Media Services-account.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 1dd09328-262b-43ef-8099-73241b49a925
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 5e6a834e98d3b8447a68d149bb40e75d74959f39
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87038467"
---
# <a name="upload-files-into-an-azure-media-services-account-from-azure-storsimple"></a>Bestanden vanuit Azure StorSimple uploaden naar een Azure Media Services-account  

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie [Media Services v3](../latest/index.yml). Zie ook [migratie richtlijnen van v2 naar v3](../latest/migrate-from-v2-to-v3.md)
>
> 
> Azure StorSimple Data Manager bevindt zich momenteel in Private Preview. 
> 

## <a name="overview"></a>Overzicht

In Media Services uploadt u de digitale bestanden naar (of neemt u deze op in) een asset. De Asset kan video, audio, afbeeldingen, miniatuur verzamelingen, tekst sporen en ondertitelings bestanden (en de meta gegevens over deze bestanden) bevatten. Zodra de bestanden zijn geüpload, wordt uw inhoud veilig opgeslagen in de Cloud voor verdere verwerking en streaming.

[Azure StorSimple](../../storsimple/index.yml) gebruikt cloudopslag als een uitbreiding van de on-premises oplossing en verdeelt gegevens automatisch over de on-premises opslag en de cloudopslag. Het StorSimple-apparaat ontdubbelt en comprimeert uw gegevens voordat deze naar de cloud worden verzonden, zodat grote bestanden zeer efficiënt in de cloud kunnen worden opgeslagen. De [StorSimple Data Manager](../../storsimple/storsimple-data-manager-overview.md)-service biedt API's waarmee u gegevens kunt extraheren uit StorSimple om deze vervolgens als AMS-assets te presenteren.

## <a name="get-started"></a>Aan de slag

1. [Maak een Media Services-account](media-services-portal-create-account.md) waarnaar u de assets wilt overbrengen.
2. Geef u op voor de preview van Data Manager, zoals wordt beschreven in het artikel [StorSimple Data Manager](../../storsimple/storsimple-data-manager-overview.md).
3. Maak een StorSimple Data Manager-account.
4. Maak een taak voor gegevenstransformatie die bij uitvoering gegevens extraheert van een StorSimple-apparaat en deze als assets overbrengt naar een AMS-account. 

    Op het moment dat de taak wordt gestart, wordt er een opslagwachtrij gemaakt. Deze wachtrij wordt gevuld met berichten over getransformeerde blobs wanneer deze gereed zijn. De naam van deze wachtrij is hetzelfde als de naam van de taakdefinitie. U kunt deze wachtrij gebruiken om te bepalen wanneer een asset gereed is en vervolgens de gewenste Media Services-bewerking aanroepen om een bewerking op de asset uit te voeren. U kunt deze wachtrij bijvoorbeeld om een Azure-functie te activeren waarin de benodigde Media Services-code is opgenomen.

## <a name="see-also"></a>Zie tevens

[De .NET SDK gebruiken om taken in de Data Manager te activeren](../../storsimple/storsimple-data-manager-dotnet-jobs.md)

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Volgende stappen

U kunt nu de geüploade assets coderen. Zie [Assets coderen](media-services-portal-encode.md) voor meer informatie.
