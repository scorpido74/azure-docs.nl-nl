---
title: Verbinding maken met Windows Virtual Desktop Windows 10 of 7 - Azure
description: Verbinding maken met Windows Virtual Desktop met de Windows Desktop-client.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b6436059cc8b3637edfc2a146d0aab3e2beae6a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154335"
---
# <a name="connect-with-the-windows-desktop-client"></a>Verbinding maken met de Windows Desktop-client

> Van toepassing op: Windows 7, Windows 10 en Windows 10 IoT Enterprise

U hebt toegang tot Windows Virtual Desktop-bronnen op apparaten met Windows 7, Windows 10 en Windows 10 IoT Enterprise met de Windows Desktop-client.

> [!IMPORTANT]
> Windows Virtual Desktop biedt geen ondersteuning voor de RADC-client (RemoteApp- en Desktop Connections) of de MSTSC-client (Remote Desktop Connection).

> [!IMPORTANT]
> Windows Virtual Desktop ondersteunt momenteel de Extern bureaublad-client niet vanuit de Windows Store. Ondersteuning voor deze client zal worden toegevoegd in een toekomstige release.

## <a name="install-the-windows-desktop-client"></a>De Windows Desktop-client installeren

Kies de client die overeenkomt met uw versie van Windows:

- [Windows 64-bits](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32-bits](https://go.microsoft.com/fwlink/?linkid=2098960)
- [Windows ARM64](https://go.microsoft.com/fwlink/?linkid=2098961)

U de client installeren voor de huidige gebruiker, waarvoor geen beheerdersrechten nodig zijn, of uw beheerder kan de client installeren en configureren zodat alle gebruikers op het apparaat er toegang toe hebben.

Na installatie kan de client vanuit het menu Start worden gestart door te zoeken naar **Extern bureaublad.**

## <a name="subscribe-to-a-feed"></a>Abonneer u op een feed

Download de lijst met beheerde bronnen die voor u beschikbaar zijn door u te abonneren op de feed die door uw beheerder wordt verstrekt. Door u te abonneren, zijn de bronnen beschikbaar op uw lokale pc.

Je abonneren op een feed:

1. Open de Windows Desktop-client.
2. Selecteer **Abonneren** op de hoofdpagina om verbinding te maken met de service en uw bronnen op te halen.
3. Meld u aan met uw gebruikersaccount wanneer u daarom wordt gevraagd.

Nadat u zich hebt aangemeld, ziet u een lijst met de bronnen die u openen.

U resources op een van de twee methoden starten.

- Dubbelklik vanaf de hoofdpagina van de client op een resource om deze te starten.
- Start een bron zoals andere apps in het startmenu normaal gesproken zouden worden gebruikt.
  - U ook zoeken naar de apps in de zoekbalk.

Eenmaal geabonneerd op een feed, wordt de inhoud van de feed regelmatig automatisch bijgewerkt. Bronnen kunnen worden toegevoegd, gewijzigd of verwijderd op basis van wijzigingen die door uw beheerder zijn aangebracht.

## <a name="next-steps"></a>Volgende stappen

Ga aan de slag met de Windows [Desktop-client](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/)voor meer informatie over het gebruik van de Windows Desktop-client.
