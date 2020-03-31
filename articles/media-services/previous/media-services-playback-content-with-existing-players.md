---
title: Bestaande spelers gebruiken om uw inhoud af te spelen - Azure | Microsoft Documenten
description: In dit artikel worden bestaande spelers weergegeven die u gebruiken om uw inhoud af te spelen.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 7e9fcf89-0fb6-4fa4-96cb-666320684d69
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 07537d3d67e41f7e1179a709ffa19f3d84aa4539
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77565822"
---
# <a name="playing-your-content-with-existing-players"></a>Uw inhoud afspelen op bestaande spelers
Azure Media Services ondersteunt veel populaire streamingformaten, zoals Smooth Streaming, HTTP Live Streaming en MPEG-Dash. In dit onderwerp wordt je naar bestaande spelers getoetst die je gebruiken om je streams te testen.

### <a name="the-azure-portal-media-services-content-player"></a>De inhoudsspeler van Azure portal Media Services
De **Azure-portal** biedt een inhoudsspeler die u gebruiken om uw video te testen.

Klik op de gewenste video (zorg ervoor dat deze is [gepubliceerd)](media-services-portal-publish.md)en klik op de **play-knop** onderaan de portal.

Hierbij geldt het volgende:

* **MEDIA SERVICES CONTENT PLAYER** speelt af vanaf het standaard streaming-eindpunt. Als u wilt afspelen vanaf een ander streaming-eindpunt, gebruikt u een andere speler. [Azure Media Player](https://aka.ms/azuremediaplayer)bijvoorbeeld .

![AMSPlayer][AMSPlayer]

### <a name="azure-media-player"></a>Azure Media Player

Gebruik [Azure Media Player](https://aka.ms/azuremediaplayer) om uw inhoud (wissen of beveiligd) af te spelen in een van de volgende indelingen:

* Smooth Streaming
* MPEG DASH
* HLS
* Progressief MP4

### <a name="flash-player"></a>Flash Player

#### <a name="aes-encrypted-with-token"></a>AES-versleuteld met Token

[https://aestoken.azurewebsites.net](https://aestoken.azurewebsites.net)

#### <a name="playready-with-token"></a>PlayReady met token

[https://sltoken.azurewebsites.net](https://sltoken.azurewebsites.net)

### <a name="dash-players"></a>DASH-spelers

[https://dashplayer.azurewebsites.net](https://dashplayer.azurewebsites.net)

[https://dashif.org](https://dashif.org)

### <a name="other"></a>Overige
Om HLS-URL's te testen, u ook het als:

* **Safari** op een iOS-apparaat of
* **3ivx HLS Player** op Windows.

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png
