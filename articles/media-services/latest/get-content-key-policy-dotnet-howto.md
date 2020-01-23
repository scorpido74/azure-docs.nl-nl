---
title: Een handtekening sleutel van een beleid ophalen met behulp van Azure Media Services v3 .NET
description: In dit onderwerp wordt uitgelegd hoe u een handtekening sleutel van het bestaande beleid kunt ophalen met behulp van Media Services v3 .NET SDK.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: juliako
ms.openlocfilehash: ff27ae0fd639316f03fe89ffc906561b3ef85f6f
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76515068"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>Een handtekening sleutel van het bestaande beleid ophalen

Een van de belangrijkste principes van de API v3 is het beter beveiligen van de API. V3-Api's retour neren geen geheimen of referenties voor **Get** -of **List** -bewerkingen. Zie de gedetailleerde uitleg hier: Zie [RBAC-en Media Services-accounts](rbac-overview.md) voor meer informatie.

In het voor beeld in dit artikel ziet u hoe u .NET kunt gebruiken om een handtekening sleutel op te halen uit het bestaande beleid. 
 
## <a name="download"></a>Download 

Kloon een GitHub-opslag plaats met het volledige .NET-voor beeld naar uw computer met behulp van de volgende opdracht:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
Het voor beeld van ContentKeyPolicy met geheimen bevindt zich in de map [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) .

## <a name="get-contentkeypolicy-with-secrets"></a>ContentKeyPolicy ontvangen met geheimen 

Als u de sleutel wilt weer geven, gebruikt u **GetPolicyPropertiesWithSecretsAsync**, zoals wordt weer gegeven in het onderstaande voor beeld.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="next-steps"></a>Volgende stappen

[Ontwerp van een inhoudsbeveiligingssysteem van een multi-DRM met toegangsbeheer](design-multi-drm-system-with-access-control.md) 
