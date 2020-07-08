---
title: Over virtuele hub-routering
titleSuffix: Azure Virtual WAN
description: In dit artikel wordt de route ring van virtuele hub beschreven
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 6809429b26eea30ca0569044634308d2e4dff9f7
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86025978"
---
# <a name="about-virtual-hub-routing"></a>Over virtuele hub-routering

De routerings mogelijkheden in een virtuele hub worden geboden door een router die alle route ring beheert tussen gateways met behulp van Border Gateway Protocol (BGP). Een virtuele hub kan meerdere gateways bevatten, zoals een site-naar-site-VPN-gateway, ExpressRoute-gateway, punt-naar-site-gateway Azure Firewall. Deze router biedt ook transit connectiviteit tussen virtuele netwerken die verbinding maken met een virtuele hub en die een geaggregeerde door Voer van 50 Gbps kan ondersteunen. Deze routerings mogelijkheden zijn van toepassing op standaard virtuele WAN-klanten.

Zie [route ring van virtuele hub configureren voor meer informatie over het](how-to-virtual-hub-routing.md)configureren van route ring.

## <a name="routing-concepts"></a><a name="concepts"></a>Routerings concepten

In de volgende secties worden de belangrijkste concepten in virtuele hub-route ring beschreven.

### <a name="hub-route-table"></a><a name="hub-route"></a>Route tabel van de hub

Een route tabel van de virtuele hub kan een of meer routes bevatten. Een route bevat de naam, een label, een doel type, een lijst met doel voorvoegsels en de volgende hop-informatie voor een pakket dat moet worden doorgestuurd. Een **verbinding** heeft meestal een routerings configuratie die aan een route tabel is gekoppeld of door gegeven

### <a name="connection"></a><a name="connection"></a>Verbinding

Verbindingen zijn Resource Manager-resources die een routerings configuratie hebben. De vier typen verbindingen zijn:

* **VPN-verbinding**: Hiermee wordt een VPN-site verbonden met een virtuele hub VPN-gateway.
* **ExpressRoute-verbinding**: Hiermee verbindt u een ExpressRoute-circuit met een virtuele hub ExpressRoute-gateway.
* **P2S-configuratie verbinding**: verbindt een VPN-configuratie (punt-naar-site) met een virtuele-hub-gebruikers-VPN (punt-naar-site)-gateway.
* **Virtuele hub-netwerk verbinding**: Hiermee verbindt u virtuele netwerken met een virtuele hub.

U kunt de routerings configuratie voor een virtuele netwerk verbinding instellen tijdens de installatie. Standaard worden alle verbindingen gekoppeld en door gegeven aan de standaard route tabel.

### <a name="association"></a><a name="association"></a>Organisatie

Elke verbinding is gekoppeld aan één route tabel. Als u een verbinding met een route tabel koppelt, kan het verkeer worden verzonden naar de bestemming die wordt aangegeven als routes in de route tabel. In de routerings configuratie van de verbinding wordt de bijbehorende route tabel weer gegeven.  Meerdere verbindingen kunnen worden gekoppeld aan dezelfde route tabel. Alle VPN-, ExpressRoute-en gebruikers-VPN-verbindingen zijn gekoppeld aan dezelfde routerings tabel (standaard).

Standaard zijn alle verbindingen gekoppeld aan een **standaard route tabel** in een virtuele hub. Elke virtuele hub heeft zijn eigen standaard route tabel, die kan worden bewerkt om een statische route (s) toe te voegen. Routes die statisch worden toegevoegd, hebben voor rang boven dynamisch geleerde routes voor dezelfde voor voegsels.

:::image type="content" source="./media/about-virtual-hub-routing/concepts-association.png" alt-text="Organisatie":::

### <a name="propagation"></a><a name="propagation"></a>Doorgifte

Verbindingen geven dynamische routes door aan een route tabel. Met een VPN-verbinding, een ExpressRoute-verbinding of een P2S-configuratie verbinding worden routes door gegeven van de virtuele hub naar de on-premises router met behulp van BGP. Routes kunnen worden door gegeven aan een of meer route tabellen.

Er is ook een **route tabel geen** beschikbaar voor elke virtuele hub. Door door te geven aan de geen route tabel, houdt in dat er geen routes moeten worden door gegeven van de verbinding. VPN-, ExpressRoute-en gebruikers-VPN-verbindingen sturen routes door naar dezelfde set route tabellen.

:::image type="content" source="./media/about-virtual-hub-routing/concepts-propagation.png" alt-text="Doorgifte":::

### <a name="configuring-static-routes-in-a-virtual-network-connection"></a><a name="static"></a>Statische routes configureren in een virtuele netwerk verbinding

Het configureren van statische routes biedt een mechanisme voor het stuur verkeer via een volgende hop-IP. Dit kan een virtueel netwerk apparaat (NVA) zijn dat is ingericht in een spoke-VNet dat is gekoppeld aan een virtuele hub. De statische route bestaat uit een route naam, een lijst met doel voorvoegsels en het volgende hop-IP-adres.

> [!NOTE]
> Sommige van deze nieuwe concepten voor Association, doorgifte en statische routes in een virtuele netwerk verbinding kunnen nog steeds worden geïmplementeerd en worden verwacht in de week van aug 3de.
>

## <a name="route-tables-in-basic-and-standard-virtual-wans-prior-to-the-feature-set-of-association-and-propagation"></a><a name="route"></a>Route tabellen in de Basic-en Standard-standaard-virtuele Wan's vóór de functieset van koppeling en doorgifte

Route tabellen hebben nu functies voor koppeling en doorgifte. Een vooraf bestaande route tabel is een route tabel zonder deze functies. Als u al bestaande routes in hub-route ring hebt en u de nieuwe mogelijkheden wilt gebruiken, moet u rekening houden met het volgende:

* **Standaard virtuele WAN-klanten met al bestaande routes in de virtuele hub**:

Als u nieuwe mogelijkheden voor de route tabel wilt gebruiken, moet u tot de week augustus wachten tot de implementatie in Azure is voltooid. Als u al bestaande routes in de sectie route ring voor de hub in Azure Portal hebt, moet u deze eerst verwijderen en vervolgens proberen nieuwe route tabellen te maken (beschikbaar in de sectie route tabellen van de hub in Azure Portal)

* **Eenvoudige virtuele WAN-klanten met reeds bestaande routes in de virtuele hub**: als u de nieuwe mogelijkheden van de route tabel wilt gebruiken, moet u tot een week van augustus wachten tot de implementatie in Azure is voltooid. Als u al bestaande routes in de sectie route ring voor de hub in Azure Portal hebt, moet u deze eerst verwijderen en vervolgens een **upgrade uitvoeren** van uw virtuele standaard WAN naar een standaard virtueel WAN. Zie [een virtuele WAN upgraden van Basic naar Standard](upgrade-virtual-wan.md).

## <a name="next-steps"></a>Volgende stappen

Zie [route ring van virtuele hub configureren voor meer informatie over het](how-to-virtual-hub-routing.md)configureren van route ring.

Raadpleeg de [Veelgestelde vragen](virtual-wan-faq.md)voor meer informatie over Virtual WAN.
