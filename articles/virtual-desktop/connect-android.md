---
title: Verbinding maken met Windows Virtual Desktop vanuit Android - Azure
description: Verbinding maken met Windows Virtual Desktop met de Android-client.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: d2990c82efbcdac7d453f920301787b8c83db1e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295364"
---
# <a name="connect-with-the-android-client"></a>Verbinding maken met de Android-client

> Geldt voor: Android 4.1 en hoger, Chromebooks met ChromeOS 53 en hoger.

>[!NOTE]
> De mogelijkheid om toegang te krijgen tot Windows Virtual Desktop-bronnen vanuit de Android-client is momenteel beschikbaar in preview.

U hebt toegang tot Windows Virtual Desktop-bronnen vanaf uw Android-apparaat met onze downloadbare client. U de Android-client ook gebruiken op Chromebook-apparaten die de Google Play Store ondersteunen. In deze handleiding wordt uitgelegd hoe u de Android-client instelt.

## <a name="install-the-android-client"></a>De Android-client installeren

Download [en](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx) installeer de client op je Android-apparaat om aan de slag te gaan.

## <a name="subscribe-to-a-feed"></a>Abonneer u op een feed

Abonneer je op de feed van je beheerder om de lijst met beheerde bronnen te krijgen die je openen op je Android-apparaat.

Je abonneren op een feed:

1. Tik in het **+** verbindingscentrum op Extern bureaublad en tik vervolgens op **Extern resourceoverzicht**.
2. Voer de feed-URL in het **veld URL van** de feed in. De feed-URL kan een URL of een e-mailadres zijn.
   - Als u een URL gebruikt, gebruikt u <https://rdweb.wvd.microsoft.com>de URL die uw beheerder u heeft gegeven, normaal gesproken .
   - Als u e-mail wilt gebruiken, voert u uw e-mailadres in. De client zoekt naar een URL die aan uw e-mailadres is gekoppeld als uw beheerder de server op die manier heeft geconfigureerd.
3. Tik op **VOLGENDE**.
4. Geef uw referenties op wanneer daarom wordt gevraagd.
   - Geef **voor gebruikersnaam**de gebruikersnaam met toestemming om toegang te krijgen tot bronnen.
   - Geef **bij Wachtwoord**het wachtwoord dat is gekoppeld aan de gebruikersnaam.
   - U ook worden gevraagd om extra factoren te bieden als uw beheerder verificatie op die manier heeft geconfigureerd.

Nadat u zich hebt geabonneerd, moet het verbindingscentrum de externe bronnen weergeven.

Zodra u zich hebt geabonneerd op een feed, wordt de inhoud van de feed regelmatig automatisch bijgewerkt. Bronnen kunnen worden toegevoegd, gewijzigd of verwijderd op basis van wijzigingen die door uw beheerder zijn aangebracht.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over het gebruik van de Android-client, bekijk [aan de slag met de Android-client](/windows-server/remote/remote-desktop-services/clients/remote-desktop-android/).
