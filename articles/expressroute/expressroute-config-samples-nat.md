---
title: 'Azure ExpressRoute: routerconfiguratievoorbeelden - NAT'
description: Deze pagina biedt routerconfiguratievoorbeelden voor Cisco- en Juniper-routers.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/06/2018
ms.author: cherylmc
ms.openlocfilehash: ef2fd40db422c459ca966e802344ef45f7ec01de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74072116"
---
# <a name="router-configuration-samples-to-set-up-and-manage-nat"></a>Routerconfiguratievoorbeelden voor het instellen en beheren van NAT

Deze pagina biedt NAT-configuratievoorbeelden voor cisco ASA- en Juniper SRX-serierouters bij het werken met ExpressRoute. Deze zijn uitsluitend bedoeld als monsters voor begeleiding en mogen niet worden gebruikt zoals het is. U met uw leverancier samenwerken om de juiste configuraties voor uw netwerk te bedenken.

> [!IMPORTANT]
> Voorbeelden op deze pagina zijn bedoeld als puur voor begeleiding. U moet samenwerken met het verkoop-/technische team van uw leverancier en uw netwerkteam om met de juiste configuraties te komen om aan uw behoeften te voldoen. Microsoft ondersteunt geen problemen met betrekking tot configuraties die op deze pagina worden vermeld. U moet contact opnemen met uw apparaatleverancier voor ondersteuningsproblemen.
> 
> 

* Onderstaande voorbeelden van routerconfiguratie zijn van toepassing op Azure Public- en Microsoft-peerings. U mag NAT niet configureren voor Azure private peering. Bekijk [ExpressRoute-peerings](expressroute-circuit-peerings.md) en [ExpressRoute NAT-vereisten](expressroute-nat.md) voor meer informatie.

* U moet aparte NAT IP-pools gebruiken voor connectiviteit met internet en ExpressRoute. Het gebruik van dezelfde NAT IP-pool op het internet en ExpressRoute zal resulteren in asymmetrische routing en verlies van connectiviteit.


## <a name="cisco-asa-firewalls"></a>Cisco ASA firewalls
### <a name="pat-configuration-for-traffic-from-customer-network-to-microsoft"></a>PAT-configuratie voor verkeer van klantennetwerk naar Microsoft
    object network MSFT-PAT
      range <SNAT-START-IP> <SNAT-END-IP>


    object-group network MSFT-Range
      network-object <IP> <Subnet_Mask>

    object-group network on-prem-range-1
      network-object <IP> <Subnet-Mask>

    object-group network on-prem-range-2
      network-object <IP> <Subnet-Mask>

    object-group network on-prem
      network-object object on-prem-range-1
      network-object object on-prem-range-2

    nat (outside,inside) source dynamic on-prem pat-pool MSFT-PAT destination static MSFT-Range MSFT-Range

### <a name="pat-configuration-for-traffic-from-microsoft-to-customer-network"></a>PAT-configuratie voor verkeer van Microsoft naar klantennetwerk

**Interfaces en richting:**

    Source Interface (where the traffic enters the ASA): inside
    Destination Interface (where the traffic exits the ASA): outside

**Configuratie:**

NAT-pool:

    object network outbound-PAT
        host <NAT-IP>

Doelserver:

    object network Customer-Network
        network-object <IP> <Subnet-Mask>

Objectgroep voor IP-adressen van klanten

    object-group network MSFT-Network-1
        network-object <MSFT-IP> <Subnet-Mask>

    object-group network MSFT-PAT-Networks
        network-object object MSFT-Network-1

NAT-opdrachten:

    nat (inside,outside) source dynamic MSFT-PAT-Networks pat-pool outbound-PAT destination static Customer-Network Customer-Network


## <a name="juniper-srx-series-routers"></a>Jeneverbes SRX-serie routers
### <a name="1-create-redundant-ethernet-interfaces-for-the-cluster"></a>1. Redundante Ethernet-interfaces voor het cluster maken
    interfaces {
        reth0 {
            description "To Internal Network";
            vlan-tagging;
            redundant-ether-options {
                redundancy-group 1;
            }
            unit 100 {
                vlan-id 100;
                family inet {
                    address <IP-Address/Subnet-mask>;
                }
            }
        }
        reth1 {
            description "To Microsoft via Edge Router";
            vlan-tagging;
            redundant-ether-options {
                redundancy-group 2;
            }
            unit 100 {
                description "To Microsoft via Edge Router";
                vlan-id 100;
                family inet {
                    address <IP-Address/Subnet-mask>;
                }
            }
        }
    }


### <a name="2-create-two-security-zones"></a>2. Twee veiligheidszones maken
* Vertrouwenszone voor intern netwerk en onbetrouwbare zone voor extern netwerk met Edge-routers
* Geschikte interfaces toewijzen aan de zones
* Services toestaan op de interfaces

    beveiliging { zones { security-zone Trust { host-inbound-traffic { system-services { ping;                   } protocollen { bgp;                   } } interfaces { reth0.100;               } } security-zone Ontrust { host-inbound-traffic { system-services { ping;                   } protocollen { bgp;                   } } interfaces { reth1.100;               }           }       }   }


### <a name="3-create-security-policies-between-zones"></a>3. Beveiligingsbeleid maken tussen zones
    security {
        policies {
            from-zone Trust to-zone Untrust {
                policy allow-any {
                    match {
                        source-address any;
                        destination-address any;
                        application any;
                    }
                    then {
                        permit;
                    }
                }
            }
            from-zone Untrust to-zone Trust {
                policy allow-any {
                    match {
                        source-address any;
                        destination-address any;
                        application any;
                    }
                    then {
                        permit;
                    }
                }
            }
        }
    }


### <a name="4-configure-nat-policies"></a>4. NAT-beleid configureren
* Maak twee NAT-groepen. Een zal worden gebruikt om NAT verkeer uitgaande naar Microsoft en andere van Microsoft naar de klant.
* Regels maken voor NAT het desbetreffende verkeer
  
       security {
           nat {
               source {
                   pool SNAT-To-ExpressRoute {
                       routing-instance {
                           External-ExpressRoute;
                       }
                       address {
                           <NAT-IP-address/Subnet-mask>;
                       }
                   }
                   pool SNAT-From-ExpressRoute {
                       routing-instance {
                           Internal;
                       }
                       address {
                           <NAT-IP-address/Subnet-mask>;
                       }
                   }
                   rule-set Outbound_NAT {
                       from routing-instance Internal;
                       to routing-instance External-ExpressRoute;
                       rule SNAT-Out {
                           match {
                               source-address 0.0.0.0/0;
                           }
                           then {
                               source-nat {
                                   pool {
                                       SNAT-To-ExpressRoute;
                                   }
                               }
                           }
                       }
                   }
                   rule-set Inbound-NAT {
                       from routing-instance External-ExpressRoute;
                       to routing-instance Internal;
                       rule SNAT-In {
                           match {
                               source-address 0.0.0.0/0;
                           }
                           then {
                               source-nat {
                                   pool {
                                       SNAT-From-ExpressRoute;
                                   }
                               }
                           }
                       }
                   }
               }
           }
       }

### <a name="5-configure-bgp-to-advertise-selective-prefixes-in-each-direction"></a>5. Configureer BGP om selectieve voorvoegsels in elke richting te adverteren
Raadpleeg voorbeelden op de pagina [Routeringsconfiguratievoorbeelden.](expressroute-config-samples-routing.md)

### <a name="6-create-policies"></a>6. Beleid maken
    routing-options {
                  autonomous-system <Customer-ASN>;
    }
    policy-options {
        prefix-list Microsoft-Prefixes {
            <IP-Address/Subnet-Mask;
            <IP-Address/Subnet-Mask;
        }
        prefix-list private-ranges {
            10.0.0.0/8;
            172.16.0.0/12;
            192.168.0.0/16;
            100.64.0.0/10;
        }
        policy-statement Advertise-NAT-Pools {
            from {
                protocol static;
                route-filter <NAT-Pool-Address/Subnet-mask> prefix-length-range /32-/32;
            }
            then accept;
        }
        policy-statement Accept-from-Microsoft {
            term 1 {
                from {
                    instance External-ExpressRoute;
                    prefix-list-filter Microsoft-Prefixes orlonger;
                }
                then accept;
            }
            term deny {
                then reject;
            }
        }
        policy-statement Accept-from-Internal {
            term no-private {
                from {
                    instance Internal;
                    prefix-list-filter private-ranges orlonger;
                }
                then reject;
            }
            term bgp {
                from {
                    instance Internal;
                    protocol bgp;
                }
                then accept;
            }
            term deny {
                then reject;
            }
        }
    }
    routing-instances {
        Internal {
            instance-type virtual-router;
            interface reth0.100;
            routing-options {
                static {
                    route <NAT-Pool-IP-Address/Subnet-mask> discard;
                }
                instance-import Accept-from-Microsoft;
            }
            protocols {
                bgp {
                    group customer {
                        export <Advertise-NAT-Pools>;
                        peer-as <Customer-ASN-1>;
                        neighbor <BGP-Neighbor-IP-Address>;
                    }
                }
            }
        }
        External-ExpressRoute {
            instance-type virtual-router;
            interface reth1.100;
            routing-options {
                static {
                    route <NAT-Pool-IP-Address/Subnet-mask> discard;
                }
                instance-import Accept-from-Internal;
            }
            protocols {
                bgp {
                    group edge-router {
                        export <Advertise-NAT-Pools>;
                        peer-as <Customer-Public-ASN>;
                        neighbor <BGP-Neighbor-IP-Address>;
                    }
                }
            }
        }
    }

## <a name="next-steps"></a>Volgende stappen
Zie de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md) voor meer informatie.

