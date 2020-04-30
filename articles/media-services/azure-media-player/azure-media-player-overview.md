---
title: Overzicht van Azure Media Player
description: Meer informatie over de Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: b6d30aebd4de272ba98fce87f23701b129eacb02
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81726520"
---
# <a name="azure-media-player-overview"></a>Overzicht van Azure Media Player #

Azure Media Player is een webvideo speler die is ontworpen om media-inhoud af te spelen van [Microsoft Azure Media Services](https://azure.microsoft.com/services/media-services/) op een groot aantal verschillende browsers en apparaten. Azure Media Player maakt gebruik van industrie standaarden, zoals HTML5, media source Extensions (MSE) en versleutelde Media-extensies (EME) om een geavanceerde adaptieve streaming-ervaring te bieden.  Wanneer deze standaarden niet beschikbaar zijn op een apparaat of in een browser, Azure Media Player gebruikt Flash en Silverlight als terugval technologie. Ontwikkel aars hebben altijd toegang tot Api's via een geïntegreerde Java script-interface, ongeacht de afspeel technologie die wordt gebruikt.  Dit maakt het mogelijk dat inhoud die wordt aangeboden door Azure Media Services over een breed scala aan apparaten en browsers zonder extra inspanning kan worden gespeeld.

Met Microsoft Azure Media Services kan inhoud worden geleverd met STREEPJES, Smooth Streaming en HLS streaming-indelingen voor het afspelen van inhoud. Azure Media Player houdt rekening met deze verschillende indelingen en speelt automatisch de beste koppeling op basis van de mogelijkheden van platform/browser. Microsoft Azure Media Services biedt ook dynamische versleuteling van assets met common Encryption (PlayReady of Widevine) of AES-128 bits-envelop versleuteling. Azure Media Player staat het ontsleutelen van PlayReady-en AES-128 bits versleutelde inhoud toe wanneer deze op de juiste wijze zijn geconfigureerd.  Zie de sectie [beveiligde inhoud](azure-media-player-protected-content.md) voor informatie over het configureren ervan.

Als u nieuwe functies wilt aanvragen, geeft u ideeën of feedback. Ga naar [UserVoice voor Azure Media Player](https://aka.ms/ampuservoice). Als u een of meer problemen ondervindt, kunt u hier een regel door nemen ampinfo@microsoft.com.

[Meld](https://aka.ms/ampsignup) u aan om een release nooit te missen en op de hoogte te blijven van de nieuwste die Azure Media Player te bieden heeft.

> [!NOTE]
> Houd er rekening mee dat Azure Media Player alleen media stromen van Azure Media Services ondersteunt.

## <a name="license"></a>Licentie ##

Azure Media Player is gelicentieerd en onderhevig aan de voor waarden die worden beschreven in de licentie voorwaarden voor micro soft-software voor Azure Media Player. Zie [licentie bestand](azure-media-player-license.md) voor volledige voor waarden. Zie de [privacyverklaring](https://www.microsoft.com/en-us/privacystatement/default.aspx) voor meer informatie.

Copyright 2015 micro soft Corporation.

## <a name="next-steps"></a>Volgende stappen ##

- [Snelstartgids Azure Media Player](azure-media-player-quickstart.md)