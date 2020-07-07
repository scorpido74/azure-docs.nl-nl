---
title: Virtueel bureau blad van Windows-Linux Support-Azure
description: Een beknopt overzicht van Linux-ondersteuning voor virtueel bureau blad van Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8c5de43ed29856451ad67e02a426b07cc34a0d54
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80422417"
---
# <a name="linux-support"></a>Linux Support

Partners kunnen de Linux SDK voor Windows Virtual Desktop gebruiken om een zelfstandige virtueel-bureaubladclient voor Windows te bouwen. U kunt dit ook gebruiken om ondersteuning van Windows virtueel bureau blad in te scha kelen voor uw client toepassing. In deze korte hand leiding wordt uitgelegd wat de Linux SDK is en hoe u deze kunt gebruiken.

## <a name="connect-with-your-linux-device"></a>Verbinding maken met uw Linux-apparaat

De volgende partners hebben goedgekeurde Windows virtual desktop-clients voor Linux-apparaten.

|Partner|Documentatie voor partners|Partner ondersteuning|
|:------|:--------------------|:--------------|
|![IGEL-logo](./media/partners/igel.png)|[Documentatie voor IGEL-client](https://www.igel.com/igel-solution-family/windows-virtual-desktop/)|[IGEL-ondersteuning](https://www.igel.com/support/)|

## <a name="what-is-the-linux-sdk"></a>Wat is de Linux SDK?

U kunt de SDK-Api's gebruiken om bron feeds op te halen, verbinding te maken met de sessies van een bureau blad of externe toepassing en veel van de omleidingen te gebruiken die onze clients van de eerste partij ondersteunen.

> [!NOTE]
> De SDK is momenteel in ontwikkeling. Dit document wordt bijgewerkt met instructies voor toegang tot de SDK wanneer deze algemeen beschikbaar is.

### <a name="supported-linux-distributions"></a>Ondersteunde Linux-distributies

De SDK is compatibel met de meeste besturings systemen op basis van Ubuntu 18,04 of hoger. Als u een andere Linux-distributie hebt, kunt u met u samen werken om erachter te komen hoe u uw behoeften het beste wordt ondersteund.

### <a name="feature-support"></a>Functie ondersteuning

De SDK ondersteunt meerdere verbindingen met Desktop-en externe toepassings sessies. De volgende omleidingen worden ondersteund:

| Omleiding       | Ondersteund |
| :---------------- | :-------: |
| Toetsenbord          | &#10004;  |
| Muis             | &#10004;  |
| Audio in          | &#10004;  |
| Audio-out         | &#10004;  |
| Klem bord (tekst)  | &#10004;  |
| Klem bord (afbeelding) | &#10004;  |
| Klem bord (bestand)  | &#10004;  |
| Cards         | &#10004;  |
| Station/map      | &#10004;  |

De SDK biedt ook ondersteuning voor meerdere monitor configuraties, op voor waarde dat de monitors die u voor uw sessie selecteert, aaneengesloten zijn.

Dit document wordt bijgewerkt tijdens het toevoegen van ondersteuning voor nieuwe functies en omleidingen. Ga naar onze [UserVoice-pagina](https://go.microsoft.com/fwlink/?linkid=2116523)als u nieuwe functies en andere verbeteringen wilt Voorst Ellen.

## <a name="get-started-with-the-linux-sdk"></a>Aan de slag met de Linux SDK

Voordat u een Linux-client voor Windows virtueel bureau blad kunt ontwikkelen, moet u het volgende doen:

1. Bouw en implementeer een Windows Virtual Desktop-omgeving voor het testen of productie gebruik.
2. Test de beschik bare clients van de eerste partij om vertrouwd te raken met de gebruikers ervaring van het virtuele Windows-bureau blad.

## <a name="next-steps"></a>Volgende stappen

Bekijk onze documentatie voor de volgende clients:

- [Windows Desktop-client](connect-windows-7-and-10.md)
- [Webclient](connect-web.md)
- [Android-client](connect-android.md)
- [macOS-client](connect-macos.md)
- [iOS-client](connect-ios.md)
