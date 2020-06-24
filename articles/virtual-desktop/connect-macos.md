---
title: Verbinding maken met het virtuele bureau blad van Windows vanuit macOS-Azure
description: Verbinding maken met het virtuele bureau blad van Windows met behulp van de macOS-client.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f2b4696886c898eec5895c4ccb59347cf2a49f03
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85208991"
---
# <a name="connect-with-the-macos-client"></a>Verbinding maken met de macOS-client

> Van toepassing op: macOS 10,12 of hoger

>[!IMPORTANT]
>Deze inhoud is van toepassing op de update uit het voorjaar van 2020 met Azure Resource Manager Windows Virtual Desktop-objecten. Zie [dit artikel](./virtual-desktop-fall-2019/connect-macos-2019.md) als u de release van Windows Virtual Desktop uit het najaar van 2019 zonder Azure Resource Manager-objecten gebruikt.
>
> De update van Windows Virtual Desktop uit het voorjaar van 2020 is momenteel beschikbaar als openbare preview. Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

U kunt toegang krijgen tot virtuele bureau blad-resources van Windows vanaf uw macOS-apparaten met onze Download bare client. In deze hand leiding wordt uitgelegd hoe u de-client kunt instellen.

## <a name="install-the-client"></a>De client installeren

[Down load](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12)   en installeer de-client op uw macOS-apparaat om aan de slag te gaan.

## <a name="subscribe-to-a-feed"></a>Abonneren op een feed

U kunt zich abonneren op de feed die uw beheerder heeft ontvangen om de lijst met beheerde beschik bare bronnen op uw macOS-apparaat op te halen.

Abonneren op een feed:

1. Selecteer **werk ruimte toevoegen** op de hoofd pagina om verbinding te maken met de service en uw resources op te halen.
2. Voer de URL voor de feed in. Dit kan een URL of e-mail adres zijn:
   - Als u een URL gebruikt, gebruikt u de beheerder die u hebt ontvangen. Normaal gesp roken is de URL <https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery> .
   - Als u e-mail wilt gebruiken, voert u uw e-mail adres in. Dit geeft de client de opdracht om te zoeken naar een URL die is gekoppeld aan uw e-mail adres als uw beheerder de server op die manier heeft geconfigureerd.
3. Selecteer **Toevoegen**.
4. Meld u aan met uw gebruikers account wanneer u hierom wordt gevraagd.

Nadat u zich hebt aangemeld, ziet u een lijst met beschik bare resources.

Zodra u bent geabonneerd op een feed, wordt de inhoud van de feed op regel matige basis automatisch bijgewerkt. Resources kunnen worden toegevoegd, gewijzigd of verwijderd op basis van wijzigingen die zijn aangebracht door de beheerder.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over de macOS-client raadpleegt u de documentatie [aan de slag met de MacOS-client](/windows-server/remote/remote-desktop-services/clients/remote-desktop-mac/) .
