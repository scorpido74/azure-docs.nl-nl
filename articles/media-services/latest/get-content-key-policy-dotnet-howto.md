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
ms.date: 04/15/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 2dea262fadb61adc9e219b76f9ac048c11e650d5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80065965"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>Een ondertekeningssleutel ophalen uit het bestaand beleid

Een van de belangrijkste principes van de API v3 is het beter beveiligen van de API. V3-Api's retour neren geen geheimen of referenties voor **Get** -of **List** -bewerkingen. Zie de gedetailleerde uitleg hier: Zie [RBAC-en Media Services-accounts](rbac-overview.md) voor meer informatie.

In het voor beeld in dit artikel ziet u hoe u .NET kunt gebruiken om een handtekening sleutel op te halen uit het bestaande beleid. 
 
## <a name="download"></a>Downloaden 

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
