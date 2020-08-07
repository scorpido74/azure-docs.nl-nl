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
ms.openlocfilehash: f91e130bfa83c6a9b116c05d7293aa70945e2dc2
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87903541"
---
# <a name="linux-support"></a>Linux Support

U hebt toegang tot de virtuele bureau blad-resources van Windows vanaf uw Linux-apparaten met de volgende ondersteunde clients, die door onze thin client-partners van Linux worden geboden. We werken samen met een aantal partners om ondersteunde Windows virtual desktop-clients in te scha kelen op meer Linux-gebaseerde besturings systemen en apparaten. Als u ondersteuning voor Windows virtueel bureau blad wilt bieden op een Linux-platform dat hier niet wordt vermeld, laat het ons weten op onze [UserVoice-pagina](https://remotedesktop.uservoice.com/forums/923035-remote-desktop-support-on-linux).

## <a name="connect-with-your-linux-device"></a>Verbinding maken met uw Linux-apparaat

De volgende partners hebben goedgekeurde Windows virtual desktop-clients voor Linux-apparaten.

|Partner|Documentatie voor partners|Partner ondersteuning|
|:------|:--------------------|:--------------|
|![IGEL-logo](./media/partners/igel.png)|[Documentatie voor IGEL-client](https://www.igel.com/igel-solution-family/windows-virtual-desktop/)|[IGEL-ondersteuning](https://www.igel.com/support/)|

## <a name="what-is-the-linux-sdk"></a>Wat is de Linux SDK?

Linux thin client-partners kunnen gebruikmaken van de virtuele bureau blad Linux SDK Api's van Windows om bron feeds op te halen, verbinding te maken met de sessies van een bureau blad of externe toepassingen en veel van de omleidingen te gebruiken die onze clients van de eerste partij ondersteunen. De SDK is compatibel met de meeste besturings systemen op basis van Ubuntu 18,04 of hoger.

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

## <a name="next-steps"></a>Volgende stappen

Bekijk onze documentatie voor de volgende clients:

- [Windows Desktop-client](connect-windows-7-10.md)
- [Webclient](connect-web.md)
- [Android-client](connect-android.md)
- [macOS-client](connect-macos.md)
- [iOS-client](connect-ios.md)
