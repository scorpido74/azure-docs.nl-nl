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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75450840"
---
1. Selecteer **VPN-sites verbinden** om de pagina **Connect-sites te** openen.

    ![Verbinding](./media/virtual-wan-tutorial-connect-vpn-site-include/connect.png "verbinding maken")

   Vul de volgende velden in:

   * Voer een vooraf gedeelde sleutel in. Als u geen sleutel invoert, genereert Azure er een voor u.
   * Selecteer de instellingen voor Protocol en IPsec. Raadpleeg [standaard/aangepaste IPSec-details] (https://docs.microsoft.com/azure/virtual-wan/virtual-wan-ipsec)
   * Selecteer de juiste optie voor **Standaardroute voor uitdragen**. Met de optie **Inschakelen** kan de virtuele hub een geleerde standaardroute naar deze verbinding verspreiden. Met deze vlag kan standaard routepropagatie naar een verbinding alleen worden uitgevoerd als de standaardroute al wordt geleerd door de Virtuele WAN-hub als gevolg van het implementeren van een firewall in de hub of als een andere verbonden site tunneling heeft geforceerd ingeschakeld. De standaardroute is niet afkomstig van de virtuele WAN-hub.

2. Selecteer **Verbinden**.
3. In een paar minuten toont de site de verbindings- en verbindingsstatus.

   ![status](./media/virtual-wan-tutorial-connect-vpn-site-include/status.png "status")

   **Verbindingsstatus:** Dit is de status van de Azure-bron voor de verbinding die de VPN-site verbindt met de VPN-gateway van de Azure-hub. Zodra deze besturingsvlakbewerking succesvol is, gaan azure VPN-gateway en het on-premises VPN-apparaat over tot het tot stand brengen van connectiviteit.

   **Verbindingsstatus:** Dit is de status van de werkelijke connectiviteit (datapad) tussen de VPN-gateway van Azure in de hub en vpn-site. Het kan een van de volgende toestanden weergeven:

    * **Onbekend**: Deze status wordt meestal gezien als de backend-systemen werken om over te schakelen naar een andere status.
    * **Verbinding maken:** Azure VPN-gateway probeert de huidige on-premises VPN-site te bereiken.
    * **Verbonden**: Connectiviteit is tot stand gekomen tussen azure VPN-gateway en on-premises VPN-site.
    * **Losgekoppeld**: Deze status wordt gezien als, om welke reden dan ook (on-premises of in Azure), de verbinding is verbroken.
4. Binnen een hub VPN-site u bovendien het volgende doen: 

   * Bewerk of verwijder de VPN-verbinding.
   * Verwijder de site in de Azure-portal.
   * Download een branch-specifieke configuratie voor meer informatie over de Azure-kant met behulp van het context (...) menu naast de site. Als u de configuratie voor alle verbonden sites in uw hub wilt downloaden, selecteert u **VPN Config downloaden** in het bovenste menu.
