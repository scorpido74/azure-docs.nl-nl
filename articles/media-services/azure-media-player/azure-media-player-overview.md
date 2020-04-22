---
title: Overzicht van Azure Media Player
description: Meer informatie over de Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: b6d30aebd4de272ba98fce87f23701b129eacb02
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726520"
---
# <a name="azure-media-player-overview"></a>Overzicht van Azure Media Player #

Azure Media Player is een webvideospeler die is gebouwd om media-inhoud van [Microsoft Azure Media Services](https://azure.microsoft.com/services/media-services/) af te spelen op een breed scala aan browsers en apparaten. Azure Media Player maakt gebruik van industriestandaarden, zoals HTML5, Media Source Extensions (MSE) en Encrypted Media Extensions (EME) om een verrijkte adaptieve streaming-ervaring te bieden.  Wanneer deze standaarden niet beschikbaar zijn op een apparaat of in een browser, gebruikt Azure Media Player Flash en Silverlight als terugvaltechnologie. Ongeacht de gebruikte afspeeltechnologie hebben ontwikkelaars een uniforme JavaScript-interface om toegang te krijgen tot API's.  Hierdoor kan inhoud die door Azure Media Services wordt bediend, zonder extra inspanning worden afgespeeld op een breed scala aan apparaten en browsers.

Met Microsoft Azure Media Services kan inhoud worden weergegeven met DASH-, Smooth Streaming- en HLS-streaming-indelingen om inhoud af te spelen. Azure Media Player houdt rekening met deze verschillende indelingen en speelt automatisch de beste koppeling af op basis van de mogelijkheden van het platform/de browser. Microsoft Azure Media Services maakt ook dynamische versleuteling mogelijk van assets met algemene versleuteling (PlayReady of Widevine) of AES-128-bits envelopversleuteling. Azure Media Player maakt het mogelijk om PlayReady- en AES-128-bits versleutelde inhoud te decryptie toe wanneer deze op de juiste manier is geconfigureerd.  Zie de sectie [Beveiligde inhoud](azure-media-player-protected-content.md) voor het configureren van dit.

Als u nieuwe functies wilt aanvragen, ideeën of feedback wilt geven, dient u deze in bij [UserVoice voor Azure Media Player.](https://aka.ms/ampuservoice) Als je specifieke problemen, vragen hebt of bugs vindt, stuur ons dan een lijn naar ampinfo@microsoft.com.

[Meld u aan](https://aka.ms/ampsignup) om nooit een release te missen en op de hoogte te blijven van het laatste nieuws dat Azure Media Player te bieden heeft.

> [!NOTE]
> Houd er rekening mee dat Azure Media Player alleen mediastreams van Azure Media Services ondersteunt.

## <a name="license"></a>Licentie ##

Azure Media Player heeft een licentie en is onderworpen aan de voorwaarden die zijn beschreven in de licentievoorwaarden voor Microsoft Software voor Azure Media Player. Zie [licentiebestand](azure-media-player-license.md) voor volledige voorwaarden. Zie de [privacyverklaring](https://www.microsoft.com/en-us/privacystatement/default.aspx) voor meer informatie.

Copyright 2015 Microsoft Corporation.

## <a name="next-steps"></a>Volgende stappen ##

- [Snel start azure mediaspeler](azure-media-player-quickstart.md)