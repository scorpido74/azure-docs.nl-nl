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
ms.openlocfilehash: 8c5de43ed29856451ad67e02a426b07cc34a0d54
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422417"
---
# <a name="linux-support"></a>Linux Support

Partners kunnen de Linux SDK voor Windows Virtual Desktop gebruiken om een zelfstandige Windows Virtual Desktop-client te bouwen. U het ook gebruiken om Windows Virtual Desktop-ondersteuning in te schakelen op uw clienttoepassing. Deze snelle gids zal uitleggen wat de Linux SDK is en hoe te beginnen met het gebruik ervan.

## <a name="connect-with-your-linux-device"></a>Verbinding maken met uw Linux-apparaat

De volgende partners hebben Windows Virtual Desktop-clients goedgekeurd voor Linux-apparaten.

|Partner|Partnerdocumentatie|Ondersteuning van partners|
|:------|:--------------------|:--------------|
|![IGEL-logo](./media/partners/igel.png)|[IGEL-clientdocumentatie](https://www.igel.com/igel-solution-family/windows-virtual-desktop/)|[Ondersteuning voor IGEL](https://www.igel.com/support/)|

## <a name="what-is-the-linux-sdk"></a>Wat is de Linux SDK?

U de SDK-API's gebruiken om bronfeeds op te halen, verbinding te maken met desktop- of externe toepassingssessies en veel van de omleidingen te gebruiken die onze first-party clients ondersteunen.

> [!NOTE]
> De SDK is momenteel in ontwikkeling. We werken dit document bij met instructies om toegang te krijgen tot de SDK wanneer het algemeen beschikbaar is.

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
