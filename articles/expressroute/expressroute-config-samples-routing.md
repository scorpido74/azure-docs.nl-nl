---
title: 'Azure ExpressRoute: voor beelden van router configuraties'
description: Deze pagina bevat de router van config-voorbeelden voor Cisco en Juniper-routers.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/06/2018
ms.author: cherylmc
ms.openlocfilehash: 2c37dadeb669fb88f858b5487379828a8dddec6c
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076671"
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>Voorbeelden van routerconfiguratie instellen en beheren van Routering
Deze pagina bevat interface en routering voorbeelden van configuraties voor IOS-XE van Cisco en Juniper MX reeks routers bij het werken met ExpressRoute. Deze zijn bedoeld om te worden voorbeelden voor alleen richtlijnen en mogen niet worden gebruikt als is. U kunt werken met de leverancier van uw zijn beschikbaar met de juiste configuraties voor uw netwerk. 

> [!IMPORTANT]
> Voorbeelden in deze pagina zijn bedoeld om de worden alleen voor hulp. U moet werken met uw netwerk team zijn beschikbaar met de juiste configuraties om te voldoen aan uw behoeften en de verkoop- en technische team van uw leverancier. Problemen met betrekking tot de configuraties die worden vermeld op deze pagina wordt niet door Microsoft ondersteuning. Neem contact op met de leverancier van uw apparaat voor ondersteuningsproblemen.
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>Instellingen voor MTU en TCP MSS op routerinterfaces
* De MTU voor de ExpressRoute-interface is 1500, dit is de gebruikelijke standaardwaarde MTU voor een Ethernet-interface op een router. Als uw router een andere MTU standaard heeft, is er niet nodig om op te geven van een waarde op de routerinterface.
* In tegenstelling tot een Azure VPN-Gateway hoeft de TCP-MSS voor een ExpressRoute-circuit niet worden opgegeven.

Voorbeelden van routerconfiguratie hieronder gelden voor alle peerings. Beoordeling [ExpressRoute-peerings](expressroute-circuit-peerings.md) en [routeringsvereisten voor ExpressRoute](expressroute-routing.md) voor meer informatie over routering.


## <a name="cisco-ios-xe-based-routers"></a>Cisco IOS-XE op basis van routers
De voorbeelden in deze sectie zijn van toepassing op elke router met de IOS-XE-OS-familie.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. interfaces en Subinterfaces configureren
U moet een sub-interface per in elke router die u verbinding met Microsoft maakt-peering. Een sub-interface kan worden geïdentificeerd met een VLAN-ID of een gestapelde combinatie van een VLAN-id's en een IP-adres.

**Dot1Q interfacedefinitie**

In dit voorbeeld bevat de interfacedefinitie van de onderliggende voor een onderliggende interface met een enkel VLAN-ID. De VLAN-ID is uniek per peering. Het laatste achttal werd van uw IPv4-adres is altijd een oneven getal.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

**QinQ interfacedefinitie**

In dit voorbeeld bevat de interfacedefinitie van de onderliggende voor een onderliggende interface met een twee VLAN-id. De buitenste VLAN-ID (s-code), als u gebruikt blijft hetzelfde tussen alle peerings. De binnenste (c-code) van de VLAN-ID is uniek per peering. Het laatste achttal werd van uw IPv4-adres is altijd een oneven getal.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>

### <a name="2-setting-up-ebgp-sessions"></a>2. instellen van eBGP-sessies
U moet een BGP-sessie met Microsoft voor elke peering instellen. Het onderstaande voorbeeld kunt u het instellen van een BGP-sessie met Microsoft. Als het IPv4-adres dat u hebt gebruikt voor uw sub-interface a.b.c.d is, worden de IP-adres van de BGP-neighbor (Microsoft) a.b.c.d+1. Het laatste achttal werd van de BGP-neighbor IPv4-adres wordt altijd een even getal zijn.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. het instellen van voor voegsels die worden geadverteerd via de BGP-sessie
U kunt uw router voor het adverteren van select voorvoegsels naar Microsoft configureren. U kunt doen met behulp van het voorbeeld hieronder.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="4-route-maps"></a>4. route kaarten
Kunt u route-kaarten en voorvoegsel geeft een lijst te filteren voorvoegsels doorgegeven in uw netwerk. U kunt het voorbeeld hieronder gebruiken om de taak te volbrengen. Zorg ervoor dat u geschikte voorvoegsel een lijst met setup hebt.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
      neighbor <IP#2_used_by_Azure> route-map <MS_Prefixes_Inbound> in
     exit-address-family
    !
    route-map <MS_Prefixes_Inbound> permit 10
     match ip address prefix-list <MS_Prefixes>
    !


## <a name="juniper-mx-series-routers"></a>Juniper MX reeks routers
De voorbeelden in deze sectie zijn van toepassing op alle routers Juniper MX-serie.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. interfaces en Subinterfaces configureren

**Dot1Q interfacedefinitie**

In dit voorbeeld bevat de interfacedefinitie van de onderliggende voor een onderliggende interface met een enkel VLAN-ID. De VLAN-ID is uniek per peering. Het laatste achttal werd van uw IPv4-adres is altijd een oneven getal.

    interfaces {
        vlan-tagging;
        <Interface_Number> {
            unit <Number> {
                vlan-id <VLAN_ID>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }
            }
        }
    }


**QinQ interfacedefinitie**

In dit voorbeeld bevat de interfacedefinitie van de onderliggende voor een onderliggende interface met een twee VLAN-id. De buitenste VLAN-ID (s-code), als u gebruikt blijft hetzelfde tussen alle peerings. De binnenste (c-code) van de VLAN-ID is uniek per peering. Het laatste achttal werd van uw IPv4-adres is altijd een oneven getal.

    interfaces {
        <Interface_Number> {
            flexible-vlan-tagging;
            unit <Number> {
                vlan-tags outer <S-tag> inner <C-tag>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }                           
            }                               
        }                                   
    }                           

### <a name="2-setting-up-ebgp-sessions"></a>2. instellen van eBGP-sessies
U moet een BGP-sessie met Microsoft voor elke peering instellen. Het onderstaande voorbeeld kunt u het instellen van een BGP-sessie met Microsoft. Als het IPv4-adres dat u hebt gebruikt voor uw sub-interface a.b.c.d is, worden de IP-adres van de BGP-neighbor (Microsoft) a.b.c.d+1. Het laatste achttal werd van de BGP-neighbor IPv4-adres wordt altijd een even getal zijn.

    routing-options {
        autonomous-system <Customer_ASN>;
    }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. het instellen van voor voegsels die worden geadverteerd via de BGP-sessie
U kunt uw router voor het adverteren van select voorvoegsels naar Microsoft configureren. U kunt doen met behulp van het voorbeeld hieronder.

    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
        route-filter <Prefix_to_be_advertised/Subnet_Mask> exact;
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }


### <a name="4-route-maps"></a>4. route kaarten
Kunt u route-kaarten en voorvoegsel geeft een lijst te filteren voorvoegsels doorgegeven in uw netwerk. U kunt het voorbeeld hieronder gebruiken om de taak te volbrengen. Zorg ervoor dat u geschikte voorvoegsel een lijst met setup hebt.

    policy-options {
        prefix-list MS_Prefixes {
            <IP_Prefix_1/Subnet_Mask>;
            <IP_Prefix_2/Subnet_Mask>;
        }
        policy-statement <MS_Prefixes_Inbound> {
            term 1 {
                from {
        prefix-list MS_Prefixes;
                }
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                import <MS_Prefixes_Inbound>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

## <a name="next-steps"></a>Volgende stappen
Zie de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md) voor meer informatie.

