---
title: Afspelen met Azure Media Player - Azure | Microsoft Documenten
description: Azure Media Player is een webvideospeler die is gebouwd om media-inhoud van Microsoft Azure Media Services af te spelen op een breed scala aan browsers en apparaten.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/17/2019
ms.author: juliako
ms.openlocfilehash: d9e42e809443a2dd6cdeb989f692b96d63269f79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673579"
---
# <a name="azure-media-player-overview"></a>Overzicht van Azure Media Player

Azure Media Player is een webvideospeler die is gebouwd om media-inhoud van Microsoft Azure Media Services af te spelen op een breed scala aan browsers en apparaten. Azure Media Player maakt gebruik van industriestandaarden, zoals HTML5, Media Source Extensions (MSE) en Encrypted Media Extensions (EME) om een verrijkte adaptieve streaming-ervaring te bieden. Wanneer deze standaarden niet beschikbaar zijn op een apparaat of in een browser, gebruikt Azure Media Player Flash en Silverlight als terugvaltechnologie. Ongeacht de gebruikte afspeeltechnologie hebben ontwikkelaars een uniforme JavaScript-interface om toegang te krijgen tot API's. Hierdoor kan inhoud die door Azure Media Services wordt bediend, zonder extra inspanning worden afgespeeld op een breed scala aan apparaten en browsers.

Met Microsoft Azure Media Services kan inhoud worden weergegeven met HLS-, DASH-, Vloeiende streamingstreaming-indelingen om inhoud af te spelen. Azure Media Player houdt rekening met deze verschillende indelingen en speelt automatisch de beste koppeling af op basis van de mogelijkheden van het platform/de browser. Media Services maakt ook dynamische versleuteling van activa mogelijk met PlayReady-versleuteling of AES-128-bits envelopversleuteling. Azure Media Player maakt het mogelijk om PlayReady- en AES-128-bits versleutelde inhoud te decryptie toe wanneer deze op de juiste manier is geconfigureerd. 

> [!NOTE]
> HTTPS-weergave is vereist voor Widevine-versleutelde inhoud.

## <a name="use-azure-media-player-demo-page"></a>Demopagina van Azure Media Player gebruiken

### <a name="start-using"></a>Beginnen met het gebruik

U de [demopagina van Azure Media Player](https://aka.ms/azuremediaplayer) gebruiken om Voorbeelden van Azure Media Services of uw eigen stream af te spelen.  

Als u een nieuwe video wilt afspelen, plakt u een andere URL en drukt u op **Bijwerken**.

Als u verschillende afspeelopties wilt configureren (bijvoorbeeld technologie, taal of versleuteling), drukt u op **Geavanceerde opties**.

![Azure Media Player](./media/azure-media-player/home-page.png)

### <a name="monitor-diagnostics-of-a-video-stream"></a>Diagnostische gegevens van een videostream controleren

U de [demopagina azure mediaplayer](https://aka.ms/azuremediaplayer) gebruiken om de diagnose van een videostream te controleren. 

![Azure Media Player-diagnostiek](./media/azure-media-player/diagnostics.png)

## <a name="set-up-azure-media-player-in-your-html"></a>Azure Media Player instellen in uw HTML

Azure Media Player is eenvoudig in te stellen. Het duurt slechts een paar ogenblikken om basisweergave van media-inhoud van uw Media Services-account te krijgen. Zie [Azure Media Player-documentatie](https://aka.ms/ampdocs) voor meer informatie over het instellen en configureren van Azure Media Player. 

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en onderworpen aan de servicevoorwaarden en het privacybeleid van Google, Inc.

## <a name="next-steps"></a>Volgende stappen

- [Documentatie voor Azure Media Player](https://aka.ms/ampdocs)
- [Azure Media Player-voorbeelden](https://aka.ms/ampsamples)
