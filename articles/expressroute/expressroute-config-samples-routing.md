---
title: 'Azure ExpressRoute: routerconfiguratievoorbeelden'
description: Deze pagina biedt router config samples voor Cisco en Juniper routers.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/26/2020
ms.author: osamaz
ms.openlocfilehash: 3603bc45b920dc62eb8bf6f2eb8557f98e21638e
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024809"
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>Routerconfiguratievoorbeelden voor het instellen en beheren van routering
Deze pagina biedt interface- en routeringsconfiguratievoorbeelden voor routers uit de Cisco IOS-XE- en Juniper MX-serie wanneer u met Azure ExpressRoute werkt.

> [!IMPORTANT]
> Voorbeelden op deze pagina zijn puur ter begeleiding. U moet samenwerken met het verkoop-/technische team van uw leverancier en uw netwerkteam om de juiste configuraties te vinden om aan uw behoeften te voldoen. Microsoft ondersteunt geen problemen met betrekking tot configuraties die op deze pagina worden vermeld. Neem contact op met uw apparaatleverancier voor ondersteuningsproblemen.
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>MsS-instellingen voor MTU en TCP op routerinterfaces
De maximale transmissie-eenheid (MTU) voor de ExpressRoute-interface is 1500, wat de typische standaard MTU is voor een Ethernet-interface op een router. Tenzij uw router standaard een andere MTU heeft, hoeft u geen waarde op te geven op de routerinterface.

In tegenstelling tot een Azure VPN-gateway hoeft de maximale segmentgrootte (MSS) van TCP voor een ExpressRoute-circuit niet te worden opgegeven.

De routerconfiguratievoorbeelden in dit artikel zijn van toepassing op alle peerings. Bekijk [expressroute-peerings](expressroute-circuit-peerings.md) en [routeringsvereisten voor ExpressRoute](expressroute-routing.md) voor meer informatie over routering.


## <a name="cisco-ios-xe-based-routers"></a>Cisco IOS-XE gebaseerde routers
De voorbeelden in deze sectie zijn van toepassing op elke router met de IOS-XE OS-familie.

### <a name="configure-interfaces-and-subinterfaces"></a>Interfaces en subinterfaces configureren
U hebt één subinterface per peering nodig in elke router die u met Microsoft verbindt. Een subinterface kan worden geïdentificeerd met een VLAN-id of een gestapeld paar VLAN-id's en een IP-adres.

**Definitie van de Dot1Q-interface**

Dit voorbeeld biedt de subinterfacedefinitie voor een subinterface met één VLAN-id. De VLAN ID is uniek per peering. Het laatste octet van uw IPv4-adres zal altijd een oneven getal zijn.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

**QinQ-interfacedefinitie**

Dit voorbeeld biedt de subinterfacedefinitie voor een subinterface met twee VLAN-id's. De buitenste VLAN-ID (s-tag) blijft, indien gebruikt, hetzelfde voor alle peerings. De innerlijke VLAN ID (c-tag) is uniek per peering. Het laatste octet van uw IPv4-adres zal altijd een oneven getal zijn.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>

### <a name="set-up-ebgp-sessions"></a>eBGP-sessies instellen
U moet een BGP-sessie met Microsoft instellen voor elke peering. Stel een BGP-sessie in met behulp van het volgende voorbeeld. Als het IPv4-adres dat u voor uw subinterface hebt gebruikt a.b.c.d was, is het IP-adres van de BGP-buurman (Microsoft) a.b.c.d+1. Het laatste octet van het IPv4-adres van de BGP-buurman zal altijd een even getal zijn.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>Voorvoegsels instellen die tijdens de BGP-sessie kunnen worden geadverteerd
Configureer uw router om bepaalde voorvoegsels aan Microsoft te adverteren met behulp van het volgende voorbeeld.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="route-maps"></a>Routekaarten
Gebruik routekaarten en voorvoegsellijsten om voorvoegsels te filteren die in uw netwerk worden gepropageerd. Bekijk het volgende voorbeeld en zorg ervoor dat u de juiste voorvoegsellijsten hebt ingesteld.

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

### <a name="configure-bfd"></a>BFD configureren

Je configureert BFD op twee plaatsen: een op interfaceniveau en een op BGP-niveau. Het voorbeeld hier is voor de QinQ-interface. 

    interface GigabitEthernet<Interface_Number>.<Number>
     bfd interval 300 min_rx 300 multiplier 3
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>
    
    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      neighbor <IP#2_used_by_Azure> activate
      neighbor <IP#2_used_by_Azure> fall-over bfd
     exit-address-family
    !


## <a name="juniper-mx-series-routers"></a>Jeneverbes MX-serie routers
De voorbeelden in deze sectie zijn van toepassing op elke juniper MX-serie router.

### <a name="configure-interfaces-and-subinterfaces"></a>Interfaces en subinterfaces configureren

**Definitie van de Dot1Q-interface**

Dit voorbeeld biedt de subinterfacedefinitie voor een subinterface met één VLAN-id. De VLAN ID is uniek per peering. Het laatste octet van uw IPv4-adres zal altijd een oneven getal zijn.

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


**QinQ-interfacedefinitie**

Dit voorbeeld biedt de subinterfacedefinitie voor een subinterface met twee VLAN-id's. De buitenste VLAN-ID (s-tag) blijft, indien gebruikt, hetzelfde voor alle peerings. De innerlijke VLAN ID (c-tag) is uniek per peering. Het laatste octet van uw IPv4-adres zal altijd een oneven getal zijn.

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

### <a name="set-up-ebgp-sessions"></a>eBGP-sessies instellen
U moet een BGP-sessie met Microsoft instellen voor elke peering. Stel een BGP-sessie in met behulp van het volgende voorbeeld. Als het IPv4-adres dat u voor uw subinterface hebt gebruikt a.b.c.d was, is het IP-adres van de BGP-buurman (Microsoft) a.b.c.d+1. Het laatste octet van het IPv4-adres van de BGP-buurman zal altijd een even getal zijn.

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

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>Voorvoegsels instellen die tijdens de BGP-sessie kunnen worden geadverteerd
Configureer uw router om bepaalde voorvoegsels aan Microsoft te adverteren met behulp van het volgende voorbeeld.

    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
        route-filter 
    <Prefix_to_be_advertised/Subnet_Mask> exact;
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


### <a name="route-policies"></a>Routebeleid
U routekaarten en voorvoegingslijsten gebruiken om voorvoegsels te filteren die in uw netwerk worden gepropageerd. Bekijk het volgende voorbeeld en zorg ervoor dat u de juiste voorvoegsellijsten hebt ingesteld.

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

### <a name="configure-bfd"></a>BFD configureren
Configureer alleen BFD onder de sectie Protocol BGP.

    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
                bfd-liveness-detection {
                       minimum-interval 3000;
                       multiplier 3;
                }
            }                               
        }                                   
    }


## <a name="next-steps"></a>Volgende stappen
Zie de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md) voor meer informatie.



