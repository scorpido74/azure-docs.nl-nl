---
title: Verbinding maken met het virtuele bureau blad van Windows vanuit iOS-Azure
description: Verbinding maken met het virtuele bureau blad van Windows met behulp van de iOS-client.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: helohr
ms.openlocfilehash: 764ed4fefd1a3aba1f0b7812fa2965505aa34161
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338693"
---
# <a name="connect-with-the-ios-client"></a>Verbinding maken met de iOS-client

> Van toepassing op: iOS 8,0 of hoger. Compatibel met iPhone, iPad en iPod Touch.

>[!NOTE]
> De iOS-client is momenteel nog in preview.

U kunt toegang krijgen tot virtuele bureau blad-resources van Windows vanaf uw iOS-apparaat met onze Download bare client. In deze hand leiding wordt uitgelegd hoe u de iOS-client kunt instellen.

## <a name="install-the-ios-beta-client"></a>De iOS bèta-client installeren
De iOS bèta-client installeren:

1. Installeer de [Apple TestFlight](https://apps.apple.com/us/app/testflight/id899247664) -app op uw IOS-apparaat.
2. Open een browser op uw iOS-apparaat en ga naar [aka.MS/rdiosbeta](https://aka.ms/rdiosbeta).
3. Klik onder het **label stap 2: Meld u aan**bij de bèta versie en selecteer **testen starten**.
4. Wanneer u bent omgeleid naar de TestFlight-app, selecteert u **accepteren**en selecteert u **installeren**.

## <a name="subscribe-to-a-feed"></a>Abonneren op een feed

Abonneer u op de feed van uw beheerder om de lijst met beheerde resources te verkrijgen die u op uw iOS-apparaat kunt gebruiken.

Abonneren op een feed:

1. Tik **+** in het verbindings centrum en tik vervolgens op **werk ruimte toevoegen**.
2. Voer de URL van de feed in het veld URL van de **feed** in. De feed-URL kan een URL of een e-mail adres zijn.
   - Als u een URL gebruikt, gebruikt u de beheerder die u hebt ontvangen. Normaal gesp roken is <https://rdweb.wvd.microsoft.com>de URL.
   - Als u e-mail wilt gebruiken, voert u uw e-mail adres in. Dit geeft de client de opdracht om te zoeken naar een URL die is gekoppeld aan uw e-mail adres als uw beheerder de server op die manier heeft geconfigureerd.
3. Tik op **volgende**.
4. Geef uw referenties op wanneer u hierom wordt gevraagd.
   - Geef voor de **gebruikers naam**de gebruikers naam op met machtigingen voor toegang tot resources.
   - Geef bij **wacht woord**het wacht woord op dat is gekoppeld aan de gebruikers naam.
   - U wordt mogelijk ook gevraagd extra factoren op te geven als uw beheerder de verificatie op die manier heeft geconfigureerd.
5. Tik op **Opslaan**.

Daarna moeten de externe bronnen worden weer gegeven in het verbindings centrum.

Zodra u bent geabonneerd op een feed, wordt de inhoud van de feed regel matig automatisch bijgewerkt. Resources kunnen worden toegevoegd, gewijzigd of verwijderd op basis van wijzigingen die zijn aangebracht door de beheerder.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de documentatie aan de [slag met de IOS-client](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-ios) voor meer informatie over het gebruik van de IOS bèta-client.
