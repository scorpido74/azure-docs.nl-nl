---
title: Overzicht van Azure Media Player
description: Meer informatie over de Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 6f3c173c61e9f6d82a51fcaf7e11cda47eb47c1c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87011701"
---
# <a name="azure-media-player-overview"></a>Overzicht van Azure Media Player #

Azure Media Player is een web-videospeler voor het afspelen van media-inhoud van [Microsoft Azure Media Services](https://azure.microsoft.com/services/media-services/) op een groot aantal browsers en apparaten. Azure Media Player maakt gebruik van industriestandaarden zoals HTML5, Media Source Extensions (MSE) en Encrypted Media Extensions (EME) om een geavanceerde adaptieve streaming-ervaring te bieden.  Wanneer deze standaarden niet beschikbaar zijn op een apparaat of in een browser, valt Azure Media Player terug op Flash- en Silverlight-technologie. Ontwikkelaars hebben altijd toegang tot API's via een geïntegreerde JavaScript-interface, ongeacht de afspeeltechnologie die wordt gebruikt.  Dit maakt het mogelijk dat inhoud die wordt aangeboden door Azure Media Services over een breed scala aan apparaten en browsers zonder extra inspanning kan worden afgespeeld.

Met Microsoft Azure Media Services kan inhoud worden geleverd met DASH, Smooth Streaming en HLS-streaming-indelingen voor het afspelen van inhoud. Azure Media Player houdt rekening met deze verschillende indelingen en speelt automatisch de beste koppeling af op basis van de mogelijkheden van het platform of de browser. Microsoft Azure Media Services biedt ook dynamische versleuteling van assets met Common Encryption (PlayReady of Widevine) of AES-128-bits-envelopversleuteling. Azure Media Player kan met PlayReady en AES-128-bits versleutelde inhoud ontsleutelen wanneer het op de juiste wijze is geconfigureerd.  Zie de sectie [Beveiligde inhoud](azure-media-player-protected-content.md) voor informatie over hoe u dit kunt configureren.

Dien aanvragen voor nieuwe functies, ideeën of feedback a.u.b. in bij [UserVoice voor Azure Media Player](https://aka.ms/ampuservoice). Als u specifieke problemen of vragen hebt of bugs vindt, stuur dan een e-mail naar ampinfo@microsoft.com.

[Meld u aan](https://aka.ms/ampsignup) om nooit een release te missen en op de hoogte te blijven van het nieuwste wat Azure Media Player te bieden heeft.

> [!NOTE]
> Houd er rekening mee dat Azure Media Player alleen mediastromen van Azure Media Services ondersteunt.

## <a name="license"></a>Licentie ##

Azure Media Player is gelicentieerd en onderhevig aan de voorwaarden van de Gebruiksrechtovereenkomst voor Microsoft-software voor Azure Media Player. Zie het [licentiebestand](/legal/azure-media-player/azure-media-player-license) voor alle voorwaarden. Zie de [Privacyverklaring](https://www.microsoft.com/en-us/privacystatement/default.aspx) voor meer informatie.

Copyright 2015 Microsoft Corporation.

## <a name="next-steps"></a>Volgende stappen ##

- [Quickstart voor Azure Media Player](azure-media-player-quickstart.md)
