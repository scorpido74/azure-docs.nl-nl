---
title: Een ondertekeningssleutel ophalen van een beleid met Azure Media Services v3 .NET
description: In dit onderwerp ziet u hoe u een ondertekeningssleutel uit het bestaande beleid haalt met Behulp van Media Services v3 .NET SDK.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/15/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 2dea262fadb61adc9e219b76f9ac048c11e650d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065965"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>Een ondertekeningssleutel ophalen uit het bestaand beleid

Een van de belangrijkste principes van de API v3 is het beter beveiligen van de API. v3 API's geven geen geheimen of referenties terug op **bewerkingen op get** of **list.** Zie de gedetailleerde uitleg hier: Voor meer informatie, zie [RBAC en Media Services accounts](rbac-overview.md)

In het voorbeeld in dit artikel ziet u hoe u .NET gebruiken om een ondertekeningssleutel uit het bestaande beleid te halen. 
 
## <a name="download"></a>Download 

Kloon een GitHub-repository die het volledige .NET-monster bevat naar uw machine met de volgende opdracht:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
Het voorbeeld ContentKeyPolicy met geheimen bevindt zich in de map [EncryptWithDRM.](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM)

## <a name="get-contentkeypolicy-with-secrets"></a>Krijg ContentKeyPolicy met geheimen 

Gebruik **GetPolicyPropertiesWithSecretsAsync**, zoals in het onderstaande voorbeeld, om bij de sleutel te komen.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="next-steps"></a>Volgende stappen

[Ontwerp van een inhoudsbeveiligingssysteem van een multi-DRM met toegangsbeheer](design-multi-drm-system-with-access-control.md) 
