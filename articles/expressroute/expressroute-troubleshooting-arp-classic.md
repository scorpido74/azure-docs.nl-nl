---
title: 'Azure ExpressRoute: ARP-tabellen - Probleemoplossing: klassiek'
description: Deze pagina bevat instructies voor het verkrijgen van de ARP-tabellen voor een ExpressRoute-circuit - klassiek implementatiemodel.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 01/30/2017
ms.author: charwen
ms.openlocfilehash: a7a24fc6674adca21e01d2502263c9767510469e
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618628"
---
# <a name="getting-arp-tables-in-the-classic-deployment-model"></a>ARP-tabellen in het klassieke implementatiemodel krijgen
> [!div class="op_single_selector"]
> * [PowerShell - Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell - Klassiek](expressroute-troubleshooting-arp-classic.md)
> 
> 

In dit artikel vindt u de stappen voor het verkrijgen van de ARP-tabellen (Address Resolution Protocol) voor uw Azure ExpressRoute-circuit.

> [!IMPORTANT]
> Dit document is bedoeld om u te helpen bij het diagnosticeren en oplossen van eenvoudige problemen. Het is niet bedoeld als vervanging voor Microsoft-ondersteuning. Als u het probleem niet oplossen met behulp van de volgende richtlijnen, opent u een ondersteuningsaanvraag met [Microsoft Azure Help+-ondersteuning.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
> 
> 

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>ARP- en ARP-tabellen (Address Resolution Protocol) en ARP
ARP is een Layer 2-protocol dat is gedefinieerd in [RFC 826.](https://tools.ietf.org/html/rfc826) ARP wordt gebruikt om een Ethernet-adres (MAC-adres) in kaart te brengen naar een IP-adres.

Een ARP-tabel biedt een toewijzing van het IPv4-adres en MAC-adres voor een bepaalde peering. De ARP-tabel voor een ExpressRoute-circuitpeering biedt de volgende informatie voor elke interface (primair en secundair):

1. Het toewijzen van een on-premises IP-adres van de routerinterface naar een MAC-adres
2. Het toewijzen van een IP-adres van een ExpressRoute-routerinterface naar een MAC-adres
3. De leeftijd van de mapping

ARP-tabellen kunnen helpen bij het valideren van laag 2-configuratie en bij het oplossen van eenvoudige problemen met layer 2-connectiviteit.

Hieronder volgt een voorbeeld van een ARP-tabel:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


In de volgende sectie vindt u informatie over het weergeven van de ARP-tabellen die worden gezien door de Expresroute-edgerouters.

## <a name="prerequisites-for-using-arp-tables"></a>Voorwaarden voor het gebruik van ARP-tabellen
Zorg ervoor dat u het volgende hebt voordat u verdergaat:

* Een geldig ExpressRoute-circuit dat is geconfigureerd met ten minste één peering. Het circuit moet volledig zijn geconfigureerd door de connectiviteitsprovider. U (of uw connectiviteitsprovider) moet ten minste één van de peerings (Azure private, Azure public of Microsoft) op dit circuit configureren.
* IP-adresbereiken die worden gebruikt voor het configureren van de peerings (Azure private, Azure public en Microsoft). Bekijk de voorbeelden van IP-adrestoewijzingop de [pagina Routeringsvereisten expressroute](expressroute-routing.md) om inzicht te krijgen in hoe IP-adressen worden toegewezen aan interfaces aan uw kant en aan de ExpressRoute-kant. U informatie krijgen over de peeringconfiguratie door de [pagina met peering-peering-peering-pagina van ExpressRoute te](expressroute-howto-routing-classic.md)bekijken.
* Informatie van uw netwerkteam of connectiviteitsprovider over de MAC-adressen van de interfaces die met deze IP-adressen worden gebruikt.
* De nieuwste Windows PowerShell-module voor Azure (versie 1.50 of hoger).

## <a name="arp-tables-for-your-expressroute-circuit"></a>ARP-tabellen voor uw ExpressRoute-circuit
In deze sectie vindt u instructies over het weergeven van de ARP-tabellen voor elk type peering met PowerShell. Voordat u verdergaat, moet u of uw connectiviteitsprovider de peering configureren. Elk circuit heeft twee paden (primair en secundair). U de ARP-tabel voor elk pad onafhankelijk controleren.

### <a name="arp-tables-for-azure-private-peering"></a>ARP-tabellen voor Azure private peering
De volgende cmdlet biedt de ARP-tabellen voor azure private peering:

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure private peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Primary

        # ARP table for Azure private peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Secondary

Hieronder volgt voorbeelduitvoer voor een van de paden:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>ARP-tabellen voor azure public peering:
De volgende cmdlet biedt de ARP-tabellen voor openbaar peering in Azure:

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure public peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Primary

        # ARP table for Azure public peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Secondary

Hieronder volgt voorbeelduitvoer voor een van de paden:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


Hieronder volgt voorbeelduitvoer voor een van de paden:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1 ffff.eeee.dddd
          0 Microsoft         64.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>ARP-tabellen voor Microsoft-peering
De volgende cmdlet biedt de ARP-tabellen voor Microsoft-peering:

    # ARP table for Microsoft peering--primary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Primary

    # ARP table for Microsoft peering--secondary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Secondary


Voorbeelduitvoer wordt hieronder weergegeven voor een van de paden:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Hoe deze informatie te gebruiken
De ARP-tabel van een peering kan worden gebruikt om laag 2-configuratie en connectiviteit te valideren. In deze sectie vindt u een overzicht van hoe ARP-tabellen er in verschillende scenario's uitzien.

### <a name="arp-table-when-a-circuit-is-in-an-operational-expected-state"></a>ARP-tabel wanneer een circuit in een operationele (verwachte) toestand is
* De ARP-tabel heeft een vermelding voor de on-premises zijde met een geldig IP- en MAC-adres en een vergelijkbare vermelding voor de Microsoft-kant.
* Het laatste octet van het on-premises IP-adres is altijd een oneven getal.
* Het laatste octet van het Microsoft IP-adres is altijd een even nummer.
* Hetzelfde MAC-adres wordt aan de Microsoft-kant weergegeven voor alle drie peerings (primair/secundair).

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

### <a name="arp-table-when-its-on-premises-or-when-the-connectivity-provider-side-has-problems"></a>ARP-tabel wanneer deze on-premises is of wanneer de kant van de connectiviteitsprovider problemen heeft
 Er wordt slechts één item weergegeven in de ARP-tabel. Het toont de toewijzing tussen het MAC-adres en het IP-adres dat wordt gebruikt aan de Microsoft-kant.

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

> [!NOTE]
> Als u een probleem als dit ondervindt, opent u een ondersteuningsverzoek bij uw connectiviteitsprovider om dit op te lossen.
> 
> 

### <a name="arp-table-when-the-microsoft-side-has-problems"></a>ARP-tabel wanneer de Microsoft-kant problemen heeft
* U ziet geen ARP-tabel die wordt weergegeven voor een peering als er problemen zijn aan de Microsoft-kant.
* Open een ondersteuningsaanvraag met [Microsoft Azure Help+-ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Geef op dat u een probleem hebt met layer 2-connectiviteit.

## <a name="next-steps"></a>Volgende stappen
* Validatie Layer 3-configuraties voor uw ExpressRoute-circuit:
  * Ontvang een routeoverzicht om de status van BGP-sessies te bepalen.
  * Ontvang een routetabel om te bepalen welke voorvoegsels worden geadverteerd op ExpressRoute.
* Valideer gegevensoverdracht door bytes in en uit te bekijken.
* Open een ondersteuningsaanvraag met [Microsoft Azure Help+-ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) als u nog steeds problemen ondervindt.

