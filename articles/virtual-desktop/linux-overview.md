---
title: Windows Virtual Desktop Linux-ondersteuning - Azure
description: Een kort overzicht Linux ondersteuning voor Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 967fd1fa182b7c8e581fd74cc287c5a6ba0e4038
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127821"
---
# <a name="linux-support"></a>Linux Support

U de Linux SDK voor Windows Virtual Desktop gebruiken om een zelfstandige Windows Virtual Desktop-client te bouwen. U het ook gebruiken om Windows Virtual Desktop-ondersteuning in te schakelen op uw clienttoepassing. Deze snelle gids zal uitleggen wat de Linux SDK is en hoe te beginnen met het gebruik ervan.

## <a name="what-is-the-linux-sdk"></a>Wat is de Linux SDK?

U de SDK-API's gebruiken om bronfeeds op te halen, verbinding te maken met desktop- of externe toepassingssessies en veel van de omleidingen te gebruiken die onze first-party clients ondersteunen.

> [!NOTE]
> De SDK is momenteel in ontwikkeling. We werken dit document bij met instructies om toegang te krijgen tot de SDK wanneer het beschikbaar is.

### <a name="supported-linux-distributions"></a>Ondersteunde Linux-distributies

De SDK is compatibel met de meeste besturingssystemen op basis van Ubuntu 18.04 of hoger. Als u een andere Linux-distributie hebt, kunnen we met u samenwerken om erachter te komen hoe u uw behoeften het beste ondersteunen.

### <a name="feature-support"></a>Ondersteuning voor functies

De SDK ondersteunt meerdere verbindingen met desktop- en externe toepassingssessies. De volgende omleidingen worden ondersteund:

| Omleiding       | Ondersteund |
| :---------------- | :-------: |
| Toetsenbord          | &#10004;  |
| Muis             | &#10004;  |
| Audio in          | &#10004;  |
| Audio uit         | &#10004;  |
| Klembord (tekst)  | &#10004;  |
| Klembord (afbeelding) | &#10004;  |
| Klembord (bestand)  | &#10004;  |
| Smartcard         | &#10004;  |
| Station/map      | &#10004;  |

De SDK ondersteunt ook meerdere beeldschermconfiguraties, zolang de beeldschermen die u voor uw sessie selecteert, aaneengesloten zijn.

We werken dit document bij als we ondersteuning toevoegen voor nieuwe functies en omleidingen. Als u nieuwe functies en andere verbeteringen wilt voorstellen, gaat u naar onze [UserVoice-pagina.](https://go.microsoft.com/fwlink/?linkid=2116523)

## <a name="get-started-with-the-linux-sdk"></a>Aan de slag met de Linux SDK

Voordat u een Linux-client voor Windows Virtual Desktop ontwikkelen, moet u de volgende dingen doen:

1. Een Windows Virtual Desktop-omgeving bouwen en implementeren voor testen of productiegebruik.
2. Test de beschikbare first-party clients om vertrouwd te raken met de Windows Virtual Desktop-gebruikerservaring.

## <a name="next-steps"></a>Volgende stappen

Bekijk onze documentatie voor de volgende klanten:

- [Windows-bureaubladclient](connect-windows-7-and-10.md)
- [Webclient](connect-web.md)
- [Android-client](connect-android.md)
- [macOS-client](connect-macos.md)
- [iOS-client](connect-ios.md)
