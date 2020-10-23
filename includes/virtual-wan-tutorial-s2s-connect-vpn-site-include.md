---
title: bestand opnemen
description: bestand opnemen
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/08/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 2f5f653fdaf9df5d1bb2cf87d06047510ad46d7b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91856710"
---
1. Selecteer **VPN-sites verbinden** om de pagina **Sites verbinden** te openen.

    ![Schermopname van het deelvenster Verbonden sites voor een virtuele HUB die gereed is voor een Vooraf gedeelde sleutel en de bijbehorende instellingen.](./media/virtual-wan-tutorial-connect-vpn-site-include/connect.png "verbinding maken")

   Vul de volgende velden in:

   * Voer een vooraf gedeelde sleutel in. Als u geen sleutel invoert, wordt door Azure automatisch een sleutel voor u gegenereerd.
   * Selecteer het protocol en de IPsec-instellingen. Zie [Standaard/aangepaste IPsec](../articles/virtual-wan/virtual-wan-ipsec.md) voor meer informatie.
   * Selecteer de juiste optie voor **Standaardroute doorgeven**. Met de optie **Inschakelen** kan de virtuele hub een bekende standaardroute naar deze verbinding doorgeven. Als deze vlag is ingeschakeld, wordt de standaardroute alleen doorgegeven als deze al bekend is bij de virtuele WAN-hub als gevolg van het implementeren van een firewall in de hub, of als voor een andere verbonden site geforceerd tunnelen is ingeschakeld. De standaardroute is niet afkomstig van de Virtual WAN-hub.

2. Selecteer **Verbinding maken**.
3. Na enkele minuten worden de verbinding en de connectiviteitsstatus weergegeven op de site.

   ![Schermopname van een V P N site-naar-site-verbinding en connectiviteitsstatus.](./media/virtual-wan-tutorial-connect-vpn-site-include/status.png "status")

   **Verbindingsstatus:** Dit is de status van de Azure-resource voor de verbinding die de VPN-site verbindt met de VPN-gateway van de Azure-hub. Wanneer dit controlesysteem goed werkt, wordt een verbinding tot stand gebracht tussen de Azure VPN-gateway en het on-premises VPN-apparaat.

   **Verbindingsstatus:** Dit is de daadwerkelijke status van de verbinding (gegevenspad) tussen de VPN-gateway van Azure in de hub en de VPN-site. De status kan een van de volgende waarden hebben:

    * **Onbekend**: Deze status wordt meestal weergegeven als de backendsystemen overgaan naar een andere status.
    * **Verbinding maken**: De Azure VPN-gateway probeert de daadwerkelijke on-premise VPN-site te bereiken.
    * **Verbinding gemaakt**: Er is een verbinding tot stand gebracht tussen de Azure VPN-gateway en de on-premise VPN-site.
    * **Verbinding verbroken**: Deze status wordt weergegeven als de verbinding om een bepaalde reden is verbroken (on-premises of in Azure).
4. Binnen een hub-VPN-site kunt u ook het volgende doen: 

   * De VPN-verbinding bewerken of verwijderen.
   * De site verwijderen in Azure Portal.
   * Een configuratie voor een specifieke vertakking downloaden voor meer informatie over de Azure-zijde met behulp van het contextmenu (...) naast de site. Als u de configuratie voor alle verbonden sites in uw hub wilt downloaden, selecteert u **VPN-configuratie downloaden** in het bovenste menu.
