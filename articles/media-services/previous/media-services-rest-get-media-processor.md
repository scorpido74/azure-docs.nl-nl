---
title: Hoe krijg je een Media Processor instantie met behulp van REST | Microsoft Documenten
description: Meer informatie over het maken van een mediaprocessorcomponent om media-inhoud voor Azure Media Services te coderen, te converteren, te versleutelen of te decoderen.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: f9ff1997-0da6-4528-aaed-792837e5be41
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 3d7b3922c9bb7eb79cd6436ba1b265714678fcc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774908"
---
# <a name="how-to-get-a-media-processor-instance"></a>Een instantie voor mediaprocessor krijgen
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [Rest](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Overzicht
Mediaprocessors zijn een onderdeel dat een specifieke video- of audioverwerkingstaak verwerkt, zoals het coderen, converteren van formaten, versleutelen of decoderen van media-inhoud. Voor alle taken die bij Media Services worden ingediend, is een mediaprocessor nodig om de video- of audio-inhoud te coderen, te versleutelen of te converteren. 

## <a name="azure-media-processors"></a>Azure-mediaprocessors 

In het volgende onderwerp worden lijsten met mediaprocessors weergegeven:

* [Mediaprocessors coderen](scenarios-and-availability.md#encoding-media-processors)
* [Mediaprocessors voor analyse](scenarios-and-availability.md#analytics-media-processors)

>[!NOTE]
>Wanneer u toegang krijgt tot entiteiten in Media Services, moet u specifieke koptekstvelden en -waarden instellen in uw HTTP-aanvragen. Zie [Setup for Media Services REST API Development voor](media-services-rest-how-to-use.md)meer informatie.

## <a name="connect-to-media-services"></a>Verbinding met Media Services maken

Zie [Toegang tot de Azure Media Services API met Azure AD-verificatie](media-services-use-aad-auth-to-access-ams-api.md)voor informatie over hoe u verbinding maken met de AMS-API. 


## <a name="get-a-media-processor"></a>Een mediaprocessor

De volgende REST-oproep laat zien hoe je een mediaprocessor instantie op naam (in dit geval, **Media Encoder Standard).** 

Aanvraag:

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.19
    Host: media.windows.net

Reactie:

    . . .

    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "Description":"Media Encoder Standard",
             "Name":"Media Encoder Standard",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }


## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Volgende stappen
Nu u weet hoe u een mediaprocessorexemplaar krijgen, gaat u naar het artikel [Hoe u een asset-artikel](media-services-rest-get-started.md) codeert, waarin wordt aangetoond hoe u de Media Encoder-standaard gebruiken om een actief te coderen.

