---
title: 'Azure ExpressRoute: ARP-tabellen - Probleemoplossing'
description: Deze pagina geeft instructies voor het verkrijgen van de ARP-tabellen voor een ExpressRoute-circuit
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 01/30/2017
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: b31e9eb852c69d5f02eb855e319d2a4901942994
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618526"
---
# <a name="getting-arp-tables-in-the-resource-manager-deployment-model"></a>ARP-tabellen in het implementatiemodel resourcebeheer opdoen
> [!div class="op_single_selector"]
> * [PowerShell - Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell - Klassiek](expressroute-troubleshooting-arp-classic.md)
> 
> 

In dit artikel loopt u door de stappen om de ARP-tabellen voor uw ExpressRoute-circuit te leren.

> [!IMPORTANT]
> Dit document is bedoeld om u te helpen bij het diagnosticeren en oplossen van eenvoudige problemen. Het is niet bedoeld als vervanging voor Microsoft-ondersteuning. U moet een ondersteuningsticket openen met [Microsoft-ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) als u het probleem niet oplossen met behulp van de onderstaande richtlijnen.
> 
> 

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>ARP- en ARP-tabellen (Address Resolution Protocol) en ARP
Address Resolution Protocol (ARP) is een laag 2-protocol dat is gedefinieerd in [RFC 826](https://tools.ietf.org/html/rfc826). ARP wordt gebruikt om het Ethernet-adres (MAC-adres) in kaart te brengen met een ip-adres.

De ARP-tabel biedt een toewijzing van het ipv4-adres en het MAC-adres voor een bepaalde peering. De ARP-tabel voor een ExpressRoute-circuitpeering biedt de volgende informatie voor elke interface (primair en secundair)

1. Ip-adres van de on-premises routerinterface toewijzen aan het MAC-adres
2. Ip-adres van de ExpressRoute-routerinterface toewijzen aan het MAC-adres
3. Leeftijd van het in kaart brengen

ARP-tabellen kunnen helpen bij het valideren van laag 2-configuratie en het oplossen van problemen met basisproblemen met layer 2-connectiviteit. 

Voorbeeld ARP-tabel: 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1   ffff.eeee.dddd
          0 Microsoft         10.0.0.2   aaaa.bbbb.cccc


In de volgende sectie vindt u informatie over hoe u de ARP-tabellen van de ExpressRoute-edgerouters bekijken. 

## <a name="prerequisites-for-learning-arp-tables"></a>Voorwaarden voor het leren van ARP-tabellen
Zorg ervoor dat u het volgende hebt voordat u verder gaat

* Een Valid ExpressRoute-circuit geconfigureerd met ten minste één peering. Het circuit moet volledig zijn geconfigureerd door de connectiviteitsprovider. U (of uw connectiviteitsprovider) moet ten minste één van de peerings (Azure private, Azure public en Microsoft) op dit circuit hebben geconfigureerd.
* IP-adresbereiken die worden gebruikt voor het configureren van de peerings (Azure private, Azure public en Microsoft). Bekijk de voorbeelden van ip-adrestoewijzingen op de [pagina Routeringsvereisten](expressroute-routing.md) van ExpressRoute om inzicht te krijgen in hoe ip-adressen worden toegewezen aan interfaces aan uw kant en aan de ExpressRoute-kant. U informatie krijgen over de peeringconfiguratie door de [pagina met peering-peering-peering-pagina van ExpressRoute te](expressroute-howto-routing-arm.md)bekijken.
* Informatie van uw netwerkteam / connectiviteitsprovider op de MAC-adressen van interfaces die met deze IP-adressen worden gebruikt.
* U moet beschikken over de nieuwste PowerShell-module voor Azure (versie 1.50 of nieuwer).

> [!NOTE]
> Als laag 3 wordt geleverd door de serviceprovider en de ARP-tabellen leeg zijn in de onderstaande portal/uitvoer, vernieuwt u de circuitconfiguratie met de vernieuwingsknop op de portal. Deze bewerking past de juiste routeconfiguratie toe op uw circuit. 
>
>

## <a name="getting-the-arp-tables-for-your-expressroute-circuit"></a>De ARP-tabellen voor uw ExpressRoute-circuit opvragen
In deze sectie vindt u instructies over hoe u de ARP-tabellen per peering bekijken met PowerShell. U of uw connectiviteitsprovider moet het peering hebben geconfigureerd voordat u verder gaat. Elk circuit heeft twee paden (primair en secundair). U de ARP-tabel voor elk pad onafhankelijk controleren.

### <a name="arp-tables-for-azure-private-peering"></a>ARP-tabellen voor Azure private peering
De volgende cmdlet biedt de ARP-tabellen voor Azure private peering

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure private peering - Primary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Primary

        # ARP table for Azure private peering - Secondary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Secondary 

Voorbeelduitvoer wordt hieronder weergegeven voor een van de paden

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1   ffff.eeee.dddd
          0 Microsoft         10.0.0.2   aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>ARP-tabellen voor azure public peering
De volgende cmdlet biedt de ARP-tabellen voor Azure public peering

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure public peering - Primary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Primary

        # ARP table for Azure public peering - Secondary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Secondary 


Voorbeelduitvoer wordt hieronder weergegeven voor een van de paden

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1   ffff.eeee.dddd
          0 Microsoft         64.0.0.2   aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>ARP-tabellen voor Microsoft-peering
De volgende cmdlet biedt de ARP-tabellen voor Microsoft-peering

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Microsoft peering - Primary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Primary

        # ARP table for Microsoft peering - Secondary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Secondary 


Voorbeelduitvoer wordt hieronder weergegeven voor een van de paden

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Hoe deze informatie te gebruiken
De ARP-tabel van een peering kan worden gebruikt om de configuratie en connectiviteit van laag 2 te valideren. In dit gedeelte vindt u een overzicht van hoe ARP-tabellen er onder verschillende scenario's uitzien.

### <a name="arp-table-when-a-circuit-is-in-operational-state-expected-state"></a>ARP-tabel wanneer een circuit in operationele staat is (verwachte status)
* De ARP-tabel heeft een vermelding voor de on-premises zijde met een geldig IP-adres en MAC-adres en een vergelijkbare vermelding voor de Microsoft-kant. 
* Het laatste octet van het on-premises ip-adres zal altijd een oneven getal zijn.
* Het laatste octet van het Microsoft ip-adres zal altijd een even nummer zijn.
* Hetzelfde MAC-adres wordt weergegeven aan de Microsoft-kant voor alle 3 peerings (primaire / secundaire). 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

### <a name="arp-table-when-on-premises--connectivity-provider-side-has-problems"></a>ARP-tabel wanneer on-premises / connectiviteit provider kant problemen heeft
Als er problemen zijn met de on-premises of connectiviteitsprovider, u zien dat er slechts één vermelding in de ARP-tabel wordt weergegeven of dat het on-premises MAC-adres onvolledig wordt weergegeven. Hier ziet u de toewijzing tussen het MAC-adres en het IP-adres dat in de Microsoft-zijde wordt gebruikt. 
  
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------    
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

of
       
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------   
         0 On-Prem           65.0.0.1   Incomplete
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


> [!NOTE]
> Open een ondersteuningsverzoek bij uw connectiviteitsprovider om dergelijke problemen te opsporen. Als de ARP-tabel geen IP-adressen heeft van de interfaces die zijn toegewezen aan MAC-adressen, controleert u de volgende informatie:
> 
> 1. Als het eerste IP-adres van het /30-subnet dat is toegewezen voor de koppeling tussen de MSEE-PR en MSEE wordt gebruikt op de interface van MSEE-PR. Azure gebruikt altijd het tweede IP-adres voor MEEs.
> 2. Controleer of de VLAN-tags van de klant (C-Tag) en service (S-Tag) overeenkomen met zowel het MSEE-PR- als het MSEE-paar.
> 

### <a name="arp-table-when-microsoft-side-has-problems"></a>ARP-tabel wanneer Microsoft-zijde problemen heeft
* U ziet geen ARP-tabel die wordt weergegeven voor een peering als er problemen zijn aan de Microsoft-kant. 
* Open een ondersteuningsticket met [Microsoft-ondersteuning.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Geef op dat u een probleem hebt met de verbinding met laag 2. 

## <a name="next-steps"></a>Volgende stappen
* Layer 3-configuraties valideren voor uw ExpressRoute-circuit
  * Routeoverzicht ophalen om de status van BGP-sessies te bepalen 
  * Routetabel ophalen om te bepalen welke voorvoegsels worden geadverteerd op ExpressRoute
* Gegevensoverdracht valideren door bytes in/ uit te bekijken
* Open een ondersteuningsticket met [Microsoft-ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) als u nog steeds problemen ondervindt.

