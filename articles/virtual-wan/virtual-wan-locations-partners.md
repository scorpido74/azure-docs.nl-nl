---
title: Azure Virtual WAN-partners en -locaties | Microsoft Documenten
description: Dit artikel bevat een lijst met Azure Virtual WAN-partners en hublocaties.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to find a Virtual WAN partner
ms.openlocfilehash: 3976f4114df6222d34f19e2bb44fd05d53057321
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123268"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>Virtuele WAN-partners en virtuele hublocaties

In dit artikel vindt u informatie over door Virtual WAN ondersteunde regio's en partners voor connectiviteit met Virtual Hub.

Azure Virtual WAN is een netwerkservice die via Azure voor een geoptimaliseerde en geautomatiseerde verbinding tussen filialen zorgt. Met Virtual WAN kunt u apparaten in filialen verbinden en configureren, zodat deze met Azure kunnen communiceren. Dit kan handmatig of met behulp van providerapparaten via een Virtual WAN-partner. Met behulp van partnerapparaten u gebruiksgemak, vereenvoudiging van de connectiviteit en configuratiebeheer.

Connectiviteit van het on-premises apparaat wordt op een geautomatiseerde manier tot de Virtual Hub gebracht. Een virtuele hub is een door Microsoft beheerd virtueel netwerk. De hub bevat verschillende service-eindpunten die verbindingen vanaf uw on-premises netwerk (vpnsite) mogelijk maken. U slechts één hub per regio hebben.

## <a name="automation-from-connectivity-partners"></a><a name="automation"></a>Automatisering van connectiviteitspartners

Apparaten die verbinding maken met Azure Virtual WAN hebben een ingebouwde automatisering om verbinding te maken. Dit wordt meestal ingesteld in de gebruikersinterface voor apparaatbeheer (of gelijkwaardig), die het connectiviteits- en configuratiebeheer tussen het VPN-filiaalapparaat instelt op een Azure Virtual Hub VPN-eindpunt (VPN-gateway).

De volgende automatisering op hoog niveau is ingesteld in het apparaatconsole/beheercentrum:

* Geschikte machtigingen voor het apparaat om toegang te krijgen tot Azure Virtual WAN Resource Group
* Uploaden van Branch Device naar Azure Virtual WAN
* Automatisch downloaden van Azure-connectiviteitsgegevens
* Configuratie van on-premises vertakkingsinrichting 

Sommige connectiviteitspartners kunnen de automatisering uitbreiden met het maken van de Azure Virtual Hub VNet en VPN Gateway. Zie [Automatiseringsrichtlijnen voor Virtuele WAN-partners](virtual-wan-configure-automation-providers.md)als u meer wilt weten over automatisering.

## <a name="connectivity-through-partners"></a><a name="partners"></a>Connectiviteit via partners

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

De volgende partners staan gepland op onze roadmap voor de nabije toekomst: 128 Technologies, Arista, Aruba HPE, Cisco Systems, F5 Networks, Open Systems, Oracle SD-WAN, SharpLink en VMWare Velocloud.

## <a name="locations"></a><a name="locations"></a>Locaties

[!INCLUDE [regions](../../includes/virtual-wan-regions-include.md)]

## <a name="next-steps"></a>Volgende stappen

* Zie de [Virtual WAN FAQ voor](virtual-wan-faq.md)meer informatie over Virtual WAN.

* Zie [Automatiseringsrichtlijnen voor Virtuele WAN-partners voor](virtual-wan-configure-automation-providers.md)meer informatie over het automatiseren van connectiviteit met Azure Virtual WAN.
