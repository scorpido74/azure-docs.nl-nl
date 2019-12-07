---
title: Afspelen met Azure Media Player-Azure | Microsoft Docs
description: Azure Media Player is een webvideo speler die is ontworpen om media-inhoud af te spelen van Microsoft Azure Media Services op een groot aantal verschillende browsers en apparaten.
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
ms.openlocfilehash: 8f1639a21e3783f587904c2a8085fd2f555ff5b8
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74900360"
---
# <a name="azure-media-player-overview"></a>Overzicht van Azure Media Player

Azure Media Player is een webvideo speler die is ontworpen om media-inhoud af te spelen van Microsoft Azure Media Services op een groot aantal verschillende browsers en apparaten. Azure Media Player maakt gebruik van industrie standaarden, zoals HTML5, media source Extensions (MSE) en versleutelde Media-extensies (EME) om een geavanceerde adaptieve streaming-ervaring te bieden. Wanneer deze standaarden niet beschikbaar zijn op een apparaat of in een browser, Azure Media Player gebruikt Flash en Silverlight als terugval technologie. Ontwikkel aars hebben altijd toegang tot Api's via een geïntegreerde Java script-interface, ongeacht de afspeel technologie die wordt gebruikt. Dit maakt het mogelijk dat inhoud die wordt aangeboden door Azure Media Services over een breed scala aan apparaten en browsers zonder extra inspanning kan worden gespeeld.

Met Microsoft Azure Media Services kan inhoud worden geleverd met HLS, DASH, Smooth Streaming streaming-indelingen voor het afspelen van inhoud. Azure Media Player houdt rekening met deze verschillende indelingen en speelt automatisch de beste koppeling op basis van de mogelijkheden van platform/browser. Media Services biedt ook dynamische versleuteling van assets met PlayReady-versleuteling of AES-128 bits-envelop versleuteling. Azure Media Player staat het ontsleutelen van PlayReady-en AES-128 bits versleutelde inhoud toe wanneer deze op de juiste wijze zijn geconfigureerd. 

> [!NOTE]
> HTTPS Play is vereist voor Widevine versleutelde inhoud.

[Start uw gratis proef versie](https://azure.microsoft.com/pricing/free-trial/)

## <a name="use-azure-media-player-demo-page"></a>Azure Media Player demo pagina gebruiken

### <a name="start-using"></a>Beginnen met gebruiken

U kunt de [Azure Media Player-demo pagina](https://aka.ms/azuremediaplayer) gebruiken om Azure Media Services-voor beelden of uw eigen stream af te spelen.  

Plak een andere URL en druk op **bijwerken**om een nieuwe video af te spelen.

Als u verschillende afspeel opties wilt configureren (bijvoorbeeld Tech, taal of versleuteling), drukt u op **Geavanceerde opties**.

![Azure Media Player](./media/azure-media-player/home-page.png)

### <a name="monitor-diagnostics-of-a-video-stream"></a>Diagnostische gegevens van een video stroom bewaken

U kunt de [Azure Media Player-demo pagina](https://aka.ms/azuremediaplayer) gebruiken om de diagnose van een video stroom te bewaken. 

![Diagnostische gegevens Azure Media Player](./media/azure-media-player/diagnostics.png)

## <a name="set-up-azure-media-player-in-your-html"></a>Azure Media Player in uw HTML instellen

Azure Media Player eenvoudig in te stellen. Het kan even duren voordat de media-inhoud wordt afgespeeld vanuit uw Media Services-account. Raadpleeg de [Azure Media Player-documentatie](https://aka.ms/ampdocs) voor meer informatie over het instellen en configureren van Azure Media Player. 

## <a name="next-steps"></a>Volgende stappen

- [Documentatie voor Azure Media Player](https://aka.ms/ampdocs)
- [Azure Media Player-voor beelden](https://aka.ms/ampsamples)
