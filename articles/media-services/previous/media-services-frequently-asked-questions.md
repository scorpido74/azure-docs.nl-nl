---
title: Veelgestelde vragen van Azure Media Services
description: In dit artikel vindt u antwoorden op de veelgestelde vragen over Azure Media Services.
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
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: bdb5a43ad6669bfcd6e93ef4e3bf1646314e4606
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705867"
---
# <a name="media-services-v2-frequently-asked-questions"></a>Media Services v2 veelgestelde vragen

In dit artikel worden veelgestelde vragen van de gebruikerscommunity van Azure Media Services (AMS) beantwoord.

## <a name="general-ams-faqs"></a>Algemene VEELGestelde VRAGEN van AMS

V: Hoe stream je naar Apple iOS-apparaten?

A: voeg "(format=m3u8-aapl)" pad toe aan het "/Manifest"-gedeelte van de URL om de streaming origin-server te vertellen HLS-inhoud terug te sturen voor consumptie op apple iOS-native apparaten (zie [het leveren van inhoud)](media-services-deliver-content-overview.md)

V: Hoe schaal je indexering?

A: De gereserveerde eenheden zijn hetzelfde voor coderings- en indexeringstaken. Volg instructies voor [het schalen van gereserveerde eenheden.](media-services-scale-media-processing-overview.md) **Houd er rekening mee** dat de prestaties van indexeren niet worden beïnvloed door het type gereserveerde eenheid.

V: Ik heb een video geüpload, gecodeerd en gepubliceerd. Wat zou de reden zijn dat de video niet wordt afgespeeld wanneer ik het probeer te streamen?

A: Een van de meest voorkomende redenen is dat u niet het streaming eindpunt hebt van waaruit u probeert terug te spelen in de **status Lopend.**  

V: Kan ik componeren op een livestream?

A: Componeren op livestreams wordt momenteel niet aangeboden in Azure Media Services, dus u moet vooraf opstellen op uw computer.

V: Kan ik Azure CDN gebruiken met Live Streaming?

A: Media Services ondersteunt integratie met Azure CDN (zie Voor meer informatie [Over streaming eindpunten beheren in een Media Services-account).](media-services-portal-manage-streaming-endpoints.md)  U Live streaming gebruiken met CDN. Azure Media Services biedt Smooth Streaming, HLS- en MPEG-DASH-uitgangen. Al deze indelingen gebruiken HTTP voor het overbrengen van gegevens en krijgen voordelen van HTTP-caching. In live streaming worden de werkelijke video/audiogegevens verdeeld in fragmenten en deze afzonderlijke fragmenten worden in de cache opgeslagen in CDN. Alleen gegevens hoeven te worden vernieuwd, zijn de manifestgegevens. CDN vernieuwt regelmatig manifestgegevens.

V: Ondersteunt Azure Media-services het opslaan van afbeeldingen?

A: Als u alleen JPEG- of PNG-afbeeldingen wilt opslaan, moet u deze in Azure Blob-opslag bewaren. Het heeft geen voordeel om ze in uw Media Services-account te plaatsen, tenzij u ze wilt koppelen aan uw video- of audio-elementen. Of als u de afbeeldingen als overlays in de video-encoder moet gebruiken. Media Encoder Standard ondersteunt het overleggen van afbeeldingen bovenop video's, en dat is wat jpeg en PNG wordt vermeld als ondersteunde invoerindelingen. Zie [Overlays maken voor](media-services-advanced-encoding-with-mes.md#overlay)meer informatie .

V: Hoe kan ik assets kopiëren van het ene Media Services-account naar het andere?

A: Als u elementen van het ene Media Services-account naar het andere wilt kopiëren met .NET, gebruikt u [de extensiemethode IAsset.Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) die beschikbaar is in de Azure [Media Services .NET SDK](https://github.com/Azure/azure-sdk-for-media-services-extensions/) Extensions-opslagplaats. Voor meer informatie, zie [dit](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) forum draad.

V: Wat zijn de ondersteunde tekens voor het benoemen van bestanden bij het werken met AMS?

A: Media Services gebruikt de waarde van de eigenschap IAssetFile.Name bij het bouwen van URL's voor de streaming-inhoud (bijvoorbeeld http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Om deze reden is procentcodering niet toegestaan. De waarde van de eigenschap **Naam** kan geen van de volgende tekens hebben [die zijn gereserveerd](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters): !*'();::@&=+$,/?%#[]". Ook kan er maar één '.' voor de bestandsnaamextensie.

V: Hoe maak je verbinding met REST?

A: Zie Toegang tot de Azure Media [Services API met Azure AD-verificatie](media-services-use-aad-auth-to-access-ams-api.md)voor informatie over hoe u verbinding maken met de AMS-API. 

V: Hoe kan ik een video draaien tijdens het coderingsproces?

A: De [Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) ondersteunt rotatie door hoeken van 90/180/270. Het standaardgedrag is 'Automatisch', waarbij het de rotatiemetagegevens in het binnenkomende MP4/MOV-bestand probeert te detecteren en dit probeert te compenseren. Voeg het volgende **element Bronnen** toe aan een van de [hier](media-services-mes-presets-overview.md)gedefinieerde json-voorinstellingen:

    "Version": 1.0,
    "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
    ],
    "Codecs": [

    ...


## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
