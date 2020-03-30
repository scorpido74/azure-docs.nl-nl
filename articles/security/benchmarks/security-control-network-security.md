---
title: Azure-beveiligingsbeheer - Netwerkbeveiliging
description: Beveiliging van beveiligingsbeheernetwerk
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 7916bbb28602d64e0916fce7badf16a65c242227
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77251869"
---
# <a name="security-control-network-security"></a>Beveiligingscontrole: netwerkbeveiliging

De aanbevelingen voor netwerkbeveiliging zijn gericht op het specificeren van welke netwerkprotocollen, TCP/UDP-poorten en netwerkverbonden services toegang tot Azure-services zijn toegestaan of geweigerd.

## <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Resources beveiligen met netwerkbeveiligingsgroepen of Azure Firewall in uw virtuele netwerk

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 1.1 | 9.2, 9.4, 14.1-14.3 | Klant |

Controleer of bij alle subnetimplementaties van het virtuele netwerk een netwerkbeveiligingsgroep is toegepast met netwerktoegangsbesturingselementen die specifiek zijn voor de vertrouwde poorten en bronnen van uw toepassing. Gebruik Azure Services met Private Link ingeschakeld, implementeer de service in uw Vnet of maak privé verbinding met privéeindpunten. Raadpleeg voor servicespecifieke vereisten de beveiligingsaanbeveling voor die specifieke service.

Als u een specifieke use case hebt, kan ook aan de vereisten worden voldaan door Azure Firewall te implementeren.

Algemene informatie over private link:

https://docs.microsoft.com/azure/private-link/private-link-overview

Een virtueel netwerk maken:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Een NSG maken met een beveiligingsconfiguratie:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Azure Firewall implementeren en configureren:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: De configuratie en het verkeer van Vnets, Subnetten en NIC's controleren en registreren

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 1.2 | 9.3, 12.2 | Klant |

Gebruik Azure Security Center en volg aanbevelingen voor netwerkbeveiliging om uw netwerkbronnen in Azure te beveiligen. Schakel NSG-stroomlogboeken in en stuur logboeken naar een opslagaccount voor verkeerscontrole.

NSG-stroomlogboeken inschakelen:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Inzicht in netwerkbeveiliging van Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

## <a name="13-protect-critical-web-applications"></a>1.3: Kritieke webapplicaties beschermen

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 1.3 | 9.5 | Klant |

Implementeer Azure Web Application Firewall (WAF) voor kritieke webtoepassingen voor extra inspectie van binnenkomend verkeer. Diagnostische instelling voor WAF inschakelen en logboeken opnemen in een opslagaccount, gebeurtenishub of logboekanalysewerkruimte.

Azure WAF implementeren:

https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Communicatie met bekende kwaadaardige IP-adressen weigeren

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 1.4 | 12.3 | Klant |

DDoS-standaardbeveiliging inschakelen op uw Azure Virtual Networks om te beschermen tegen DDoS-aanvallen. Gebruik Azure Security Center Integrated Threat Intelligence om communicatie met bekende kwaadaardige IP-adressen te weigeren.

Implementeer Azure Firewall op elk van de netwerkgrenzen van de &quot;organisatie met&quot; Threat Intelligence ingeschakeld en geconfigureerd voor Waarschuwing en weigering voor kwaadaardig netwerkverkeer.

Gebruik Azure Security Center Just In Time Network-toegang om NSG's te configureren om de blootstelling van eindpunten tot goedgekeurde IP-adressen gedurende een beperkte periode te beperken.

Gebruik Azure Security Center Adaptive Network Hardening om NSG-configuraties aan te bevelen die poorten en bron-IP's beperken op basis van werkelijke verkeers- en bedreigingsinformatie.

DDoS-beveiliging configureren:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Azure Firewall implementeren:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Begrijp azure security center integrated threat intelligence:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

Inzicht in Azure Security Center Adaptive Network Hardening:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening

Informatie over Azure Security Center Just In Time Network Access Control:

https://docs.microsoft.com/azure/security-center/security-center-just-in-time

## <a name="15-record-network-packets-and-flow-logs"></a>1.5: Netwerkpakketten en stroomlogboeken opnemen

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 1.5 | 12.5, 15.8 | Klant |

Record NSG flow logs in een opslagaccount om stroomrecords te genereren. Schakel Network Watcher-pakketopname in indien nodig voor het onderzoeken van afwijkende activiteiten.

NSG-stroomlogboeken inschakelen:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Zo schakel je Network Watcher in:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Netwerkgebaseerde inbraakdetectie-/inbraakpreventiesystemen (IDS/IPS) implementeren

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 1.6 | 12.6, 12.7 | Klant |

Implementeer Azure Firewall op elk van de netwerkgrenzen van de &quot;organisatie met&quot; Threat Intelligence ingeschakeld en geconfigureerd voor Waarschuwing en weigering voor kwaadaardig netwerkverkeer.

Azure Firewall implementeren:https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Waarschuwingen configureren met Azure Firewall:https://docs.microsoft.com/azure/firewall/threat-intel

## <a name="17-manage-traffic-to-web-applications"></a>1.7: Verkeer naar webtoepassingen beheren

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 1.7 | 12.9, 12.10 | Klant |

Azure Application Gateway voor webtoepassingen implementeren met HTTPS/SSL ingeschakeld voor vertrouwde certificaten.

Application Gateway implementeren:

https://docs.microsoft.com/azure/application-gateway/quick-create-portal

Toepassingsgateway configureren om HTTPS te gebruiken:

https://docs.microsoft.com/azure/application-gateway/create-ssl-portal

Inzicht in layer 7 load balancing met Azure webapplication gateways:

https://docs.microsoft.com/azure/application-gateway/overview

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimaliseer de complexiteit en administratieve overhead van netwerkbeveiligingsregels

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 1.8 | 1.5 | Klant |

Gebruik Virtual Network Service Tags om netwerktoegangsbesturingselementen te definiëren in netwerkbeveiligingsgroepen of Azure Firewall. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de servicetag (bijvoorbeeld ApiManagement) op te geven in het juiste bron- of bestemmingsveld van een regel, u het verkeer voor de desbetreffende service toestaan of weigeren. Microsoft beheert de adresvoorvoegsels van de servicetag en werkt de servicetag automatisch bij wanneer adressen worden gewijzigd.

Servicetags begrijpen en gebruiken:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Standaardbeveiligingsconfiguraties voor netwerkapparaten onderhouden

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 1.9 | 11.1 | Klant |

Standaardbeveiligingsconfiguraties voor netwerkresources definiëren en implementeren met Azure Policy.

U Azure Blueprints ook gebruiken om azure-implementaties op grote schaal te vereenvoudigen door belangrijke omgevingsartefacten te verpakken, zoals Azure Resource Manager-sjablonen, RBAC-besturingselementen en beleidsregels, in één blauwdrukdefinitie. U de blauwdruk toepassen op nieuwe abonnementen en besturingselement en beheer verfijnen via versiebeheer.

Azure-beleid configureren en beheren:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Voorbeelden van Azure-beleid voor netwerken:

https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network

Een Azure Blueprint maken:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

## <a name="110-document-traffic-configuration-rules"></a>1.10: Regels voor documentconfiguratie

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 1.1 | 11.2 | Klant |

Tags gebruiken voor NSGs en andere bronnen met betrekking tot netwerkbeveiliging en verkeersstroom. Voor afzonderlijke NSG-regels &quot;&quot; gebruikt u het veld Beschrijving om de bedrijfsbehoefte en/of -duur (enz.) op te geven voor regels die verkeer van/naar een netwerk toestaan.

Tags maken en gebruiken:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Een virtueel netwerk maken:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Een NSG maken met een Beveiligingsconfig:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Gebruik geautomatiseerde hulpprogramma's om netwerkbronconfiguraties te bewaken en wijzigingen te detecteren

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 1.11 | 11.3 | Klant |

Gebruik Azure Policy om configuratie voor netwerkbronnen te valideren (en/of te herstellen).

Azure-beleid configureren en beheren:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Voorbeelden van Azure-beleid voor netwerken:

https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network

## <a name="next-steps"></a>Volgende stappen

- Zie de volgende beveiligingscontrole: [Logboekregistratie en -controle](security-control-logging-monitoring.md)
