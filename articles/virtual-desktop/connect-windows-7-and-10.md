---
title: Verbinding maken met Windows Virtual Desktop Windows 10 of 7-Azure
description: Verbinding maken met het virtuele bureau blad van Windows met behulp van de Windows-desktop-client.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2b16818856ca8196b82eb8f618cf22b5fc1b6854
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612687"
---
# <a name="connect-with-the-windows-desktop-client"></a>Verbinding maken met de Windows Desktop-client

> Van toepassing op: Windows 7, Windows 10 en Windows 10 IoT Enter prise

U hebt toegang tot de virtuele Bureau bladen van Windows op apparaten met Windows 7, Windows 10 en Windows 10 IoT Enter prise met behulp van de Windows-bureaubladclient.

>[!NOTE]
>De Windows-client wordt automatisch standaard ingesteld op Windows Virtual Desktop 2019 release. Als de client echter detecteert dat de gebruiker ook Azure Resource Manager resources heeft, worden de resources automatisch toegevoegd of krijgt de gebruiker een melding dat ze beschikbaar zijn. 

> [!IMPORTANT]
> Windows virtueel bureau blad biedt geen ondersteuning voor de client RemoteApp-en bureaublad verbindingen (RADC) of de Verbinding met extern bureaublad-client (MSTSC).

> [!IMPORTANT]
> Het virtuele bureau blad van Windows biedt momenteel geen ondersteuning voor de Extern bureaublad-client vanuit de Windows Store. Ondersteuning voor deze client wordt in een toekomstige versie toegevoegd.

## <a name="install-the-windows-desktop-client"></a>De Windows-Desktop-Client installeren

Kies de client die overeenkomt met uw versie van Windows:

- [Windows 64-bits](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32-bits](https://go.microsoft.com/fwlink/?linkid=2098960)
- [Windows-ARM64](https://go.microsoft.com/fwlink/?linkid=2098961)

U kunt de client installeren voor de huidige gebruiker, waarvoor geen beheerders rechten zijn vereist, of uw beheerder kan de client installeren en configureren zodat alle gebruikers op het apparaat toegang hebben.

Nadat de client is geïnstalleerd, kan deze vanuit het menu Start worden gestart door te zoeken naar **extern bureaublad**.

## <a name="subscribe-to-a-feed"></a>Abonneren op een feed

Haal de lijst van beheerde beschik bare bronnen op door u te abonneren op de feed die door uw beheerder wordt aangeboden. Als u een abonnement maakt, worden de resources beschikbaar op uw lokale PC.

Abonneren op een feed:

1. Open de Windows-desktop-client.
2. Selecteer **Abonneren** op de hoofd pagina om verbinding te maken met de service en uw resources op te halen.
3. Meld u aan met uw gebruikers account wanneer u hierom wordt gevraagd.

Nadat u zich hebt aangemeld, ziet u een lijst met de resources die u kunt openen.

U kunt resources op een van de volgende twee manieren starten.

- Dubbel klik op de hoofd pagina van de client op een resource om deze te starten.
- Start een resource op dezelfde manier als andere apps in het menu Start.
  - U kunt ook zoeken naar de apps in de zoek balk.

Zodra u bent geabonneerd op een feed, wordt de inhoud van de feed regel matig automatisch bijgewerkt. Resources kunnen worden toegevoegd, gewijzigd of verwijderd op basis van wijzigingen die zijn aangebracht door de beheerder.

## <a name="next-steps"></a>Volgende stappen

Ga voor meer informatie over het gebruik van de Windows-desktop-client naar [aan de slag met de Windows desktop-client](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/).
