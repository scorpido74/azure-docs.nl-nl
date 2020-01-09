---
title: bestand opnemen
description: bestand opnemen
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8b338f25e9771f5947fd494cfb00d0f6cb9ef67a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75450840"
---
1. Selecteer **VPN-sites verbinden** om de pagina **verbinding maken met sites** te openen.

    ![verbinding maken met](./media/virtual-wan-tutorial-connect-vpn-site-include/connect.png "verbinding maken")

   Vul de volgende velden in:

   * Geef een vooraf gedeelde sleutel op. Als u geen sleutel invoert, wordt er door Azure automatisch een voor u gegenereerd.
   * Selecteer het protocol en de IPsec-instellingen. Raadpleeg [standaard/aangepaste IPSec-Details] (https://docs.microsoft.com/azure/virtual-wan/virtual-wan-ipsec)
   * Selecteer de juiste optie voor het **door geven van de standaard route**. Met de optie **Enable** kan de virtuele hub een geleerde standaard route naar deze verbinding door geven. Met deze vlag wordt de standaard route doorgifte alleen naar een verbinding alleen als de standaard route al door de virtuele WAN-hub is geleerd als gevolg van het implementeren van een firewall in de hub, of als een andere verbonden site geforceerde tunneling heeft ingeschakeld. De standaard route is niet afkomstig van de virtuele WAN-hub.

2. Selecteer **Verbinden**.
3. In een paar minuten wordt de verbinding en connectiviteits status weer gegeven op de site.

   ![status](./media/virtual-wan-tutorial-connect-vpn-site-include/status.png "status")

   **Verbindings status:** Dit is de status van de Azure-resource voor de verbinding die de VPN-site verbindt met de VPN-gateway van de Azure-hub. Zodra de bewerking voor het besturings vlak is voltooid, wordt de verbinding met Azure VPN gateway en het on-premises VPN-apparaat voortgezet.

   **Verbindings status:** Dit is de daad werkelijke status van connectiviteit (gegevenspad) tussen de VPN-gateway van Azure in de hub en de VPN-site. Er kunnen een van de volgende statussen worden weer gegeven:

    * **Onbekend**: deze status wordt doorgaans weer gegeven als de back-end-systemen met de overgang naar een andere status werken.
    * **Verbinding maken**: er wordt geprobeerd een Azure VPN-gateway te bereiken met de daad werkelijke on-PREMISES VPN-site.
    * **Verbonden**: de verbinding wordt tot stand gebracht tussen de Azure VPN-gateway en de on-PREMISES VPN-site.
    * De **verbinding is verbroken**: deze status wordt gezien als de verbinding is verbroken om een van de volgende redenen (on-premises of in Azure).
4. Binnen een hub-VPN-site kunt u ook het volgende doen: 

   * Bewerk of verwijder de VPN-verbinding.
   * Verwijder de site in de Azure Portal.
   * Down load een vertakking-specifieke configuratie voor meer informatie over de Azure-zijde met behulp van het context menu (...) naast de site. Als u de configuratie voor alle verbonden sites in uw hub wilt downloaden, selecteert u **VPN-configuratie downloaden** in het bovenste menu.
