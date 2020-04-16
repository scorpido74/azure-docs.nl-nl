---
title: Azure-beveiligingsbeheer - Netwerkbeveiliging
description: Azure Security Control Network Security
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: f508de35470043cc71bfc8607367f28c04440b57
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408302"
---
# <a name="security-control-network-security"></a>Beveiligingscontrole: netwerkbeveiliging

De aanbevelingen voor netwerkbeveiliging zijn gericht op het specificeren van welke netwerkprotocollen, TCP/UDP-poorten en netwerkverbonden services toegang tot Azure-services zijn toegestaan of geweigerd.

## <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Azure-bronnen binnen virtuele netwerken beveiligen

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 1.1 | 9.2, 9.4, 14.1, 14.2, 14.3 | Klant |

Controleer of bij alle subnetimplementaties van het virtuele netwerk een netwerkbeveiligingsgroep is toegepast met netwerktoegangsbesturingselementen die specifiek zijn voor de vertrouwde poorten en bronnen van uw toepassing. Gebruik, indien beschikbaar, Private Endpoints with Private Link om uw Azure-servicebronnen te beveiligen voor uw virtuele netwerk door vnet-identiteit uit te breiden naar de service. Wanneer Privéeindpunten en Privékoppeling niet beschikbaar zijn, gebruikt u Service-eindpunten. Raadpleeg voor servicespecifieke vereisten de beveiligingsaanbeveling voor die specifieke service. 

Als u een specifieke use case hebt, kan ook aan de vereiste worden voldaan door Azure Firewall te implementeren.

- [Informatie over eindpunten van virtual network service](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

- [Informatie over Azure Private Link](https://docs.microsoft.com/azure/private-link/private-link-overview)

- [Een virtueel netwerk maken](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [Een NSG maken met een beveiligingsconfiguratie](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

- [Azure Firewall implementeren en configureren](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: De configuratie en het verkeer van virtuele netwerken, subnetten en NIC's controleren en registreren

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 1.2 | 9.3, 12.2, 12.8 | Klant |

Gebruik Azure Security Center en volg aanbevelingen voor netwerkbeveiliging om uw netwerkbronnen in Azure te beveiligen. Schakel NSG-stroomlogboeken in en stuur logboeken naar een opslagaccount voor verkeerscontrole. U ook NSG-stroomlogboeken naar een Log Analytics Workspace verzenden en Traffic Analytics gebruiken om inzicht te geven in de verkeersstroom in uw Azure-cloud. Enkele voordelen van Traffic Analytics zijn de mogelijkheid om netwerkactiviteiten te visualiseren en hotspots te identificeren, beveiligingsbedreigingen te identificeren, verkeersstroompatronen te begrijpen en netwerkverkeerde configuraties te lokaliseren.

- [NSG-stroomlogboeken inschakelen](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [Traffic Analytics inschakelen en gebruiken](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

- [Netwerkbeveiliging van Azure Security Center begrijpen](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

## <a name="13-protect-critical-web-applications"></a>1.3: Kritieke webapplicaties beschermen

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 1.3 | 9.5 | Klant |

Implementeer Azure Web Application Firewall (WAF) voor kritieke webtoepassingen voor extra inspectie van binnenkomend verkeer. Diagnostische instelling voor WAF inschakelen en logboeken opnemen in een opslagaccount, gebeurtenishub of logboekanalysewerkruimte.

- [Azure WAF implementeren](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Communicatie met bekende kwaadaardige IP-adressen weigeren

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 1.4 | 12.3 | Klant |

DDoS-standaardbeveiliging inschakelen op uw Azure Virtual Networks om te beschermen tegen DDoS-aanvallen. Gebruik Azure Security Center Integrated Threat Intelligence om communicatie met bekende kwaadaardige IP-adressen te weigeren.

Implementeer Azure Firewall op elk van de netwerkgrenzen van de organisatie met Threat Intelligence ingeschakeld en geconfigureerd om te 'Waarschuwen en weigeren' voor kwaadaardig netwerkverkeer.

Gebruik Azure Security Center Just In Time Network-toegang om NSG's te configureren om de blootstelling van eindpunten tot goedgekeurde IP-adressen gedurende een beperkte periode te beperken.

Gebruik Azure Security Center Adaptive Network Hardening om NSG-configuraties aan te bevelen die poorten en bron-IP's beperken op basis van werkelijke verkeers- en bedreigingsinformatie.

- [DDoS-beveiliging configureren](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

- [Azure Firewall implementeren](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

- [Informatie over azure security center integrated threat intelligence](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

- [Inzicht in Azure Security Center Adaptive Network Hardening](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)

- [Informatie over Azure Security Center Just In Time Network Access Control](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)

## <a name="15-record-network-packets"></a>1.5: Netwerkpakketten opnemen

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 1.5 | 12.5 | Klant |

Schakel Network Watcher packet capture in om afwijkende activiteiten te onderzoeken.

- [Zo schakel je Network Watcher in](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Netwerkgebaseerde inbraakdetectie-/inbraakpreventiesystemen (IDS/IPS) implementeren

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 1.6 | 12.6, 12.7 | Klant |

Selecteer een aanbieding uit de Azure Marketplace die IDS/IPS-functionaliteit ondersteunt met payload-inspectiemogelijkheden.  Als inbraakdetectie en/of preventie op basis van payload-inspectie geen vereiste is, kan Azure Firewall met Threat Intelligence worden gebruikt. Azure Firewall Threat intelligence-gebaseerde filtering kan verkeer van en naar bekende kwaadaardige IP-adressen en domeinen waarschuwen en weigeren. De IP-adressen en domeinen zijn afkomstig uit de Microsoft Threat Intelligence-feed.

Implementeer de firewalloplossing van uw keuze op elk van de netwerkgrenzen van uw organisatie om kwaadaardig verkeer te detecteren en/of te ontkennen.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Azure Firewall implementeren](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

- [Waarschuwingen configureren met Azure Firewall](https://docs.microsoft.com/azure/firewall/threat-intel)

## <a name="17-manage-traffic-to-web-applications"></a>1.7: Verkeer naar webtoepassingen beheren

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 1.7 | 12.9, 12.10 | Klant |

Azure Application Gateway voor webtoepassingen implementeren met HTTPS/SSL ingeschakeld voor vertrouwde certificaten.

- [Application Gateway implementeren](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)

- [Toepassingsgateway configureren om HTTPS te gebruiken](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)

- [Inzicht in layer 7 load balancing met Azure webapplication gateways](https://docs.microsoft.com/azure/application-gateway/overview)

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimaliseer de complexiteit en administratieve overhead van netwerkbeveiligingsregels

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 1.8 | 1.5 | Klant |

Gebruik Virtual Network Service Tags om netwerktoegangsbesturingselementen te definiëren in netwerkbeveiligingsgroepen of Azure Firewall. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de servicetag (bijvoorbeeld ApiManagement) op te geven in het juiste bron- of bestemmingsveld van een regel, u het verkeer voor de desbetreffende service toestaan of weigeren. Microsoft beheert de adresvoorvoegsels van de servicetag en werkt de servicetag automatisch bij wanneer adressen worden gewijzigd.

U ook toepassingsbeveiligingsgroepen gebruiken om complexe beveiligingsconfiguratie te vereenvoudigen. Met behulp van toepassingsbeveiligingsgroepen kunt u netwerkbeveiliging configureren als een natuurlijk verlengstuk van de structuur van een toepassing, waarbij u virtuele machines kunt groeperen en netwerkbeveiligingsbeleid kunt definiëren op basis van die groepen.

- [Servicetags begrijpen en gebruiken](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

- [Toepassingsbeveiligingsgroepen begrijpen en gebruiken](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups)

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Standaardbeveiligingsconfiguraties voor netwerkapparaten onderhouden

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 1.9 | 11.1 | Klant |

Standaardbeveiligingsconfiguraties voor netwerkresources definiëren en implementeren met Azure Policy.

U Azure Blueprints ook gebruiken om azure-implementaties op grote schaal te vereenvoudigen door belangrijke omgevingsartefacten te verpakken, zoals Azure Resources Manager-sjablonen, RBAC-besturingselementen en beleidsregels, in één blauwdrukdefinitie. U de blauwdruk toepassen op nieuwe abonnementen en besturingselement en beheer verfijnen via versiebeheer.

- [Azure-beleid configureren en beheren](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Azure-beleidsvoorbeelden voor netwerken](https://docs.microsoft.com/azure/governance/policy/samples/#network)

- [Een Azure Blueprint maken](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

## <a name="110-document-traffic-configuration-rules"></a>1.10: Regels voor documentconfiguratie

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 1,10 | 11.2 | Klant |

Tags gebruiken voor NSGs en andere bronnen met betrekking tot netwerkbeveiliging en verkeersstroom. Voor afzonderlijke NSG-regels gebruikt u het veld 'Beschrijving' om de bedrijfsbehoefte en/of -duur (enz.) op te geven voor regels die verkeer van/naar een netwerk toestaan.

Gebruik een van de ingebouwde Azure-beleidsdefinities met betrekking tot tags, zoals 'Tag en de waarde ervan vereisen' om ervoor te zorgen dat alle resources met tags worden gemaakt en om u op de hoogte te stellen van bestaande niet-gelabelde bronnen.

U Azure PowerShell of Azure CLI gebruiken om acties op te zoeken of uit te voeren op basis van resources op basis van hun tags.

- [Tags maken en gebruiken](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [Een virtueel netwerk maken](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [Een NSG maken met een Security Config](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Gebruik geautomatiseerde hulpprogramma's om netwerkbronconfiguraties te bewaken en wijzigingen te detecteren

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 1.11 | 11.3 | Klant |

Gebruik Azure Activity Log om bronconfiguraties te bewaken en wijzigingen in uw Azure-bronnen te detecteren. Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer er wijzigingen in kritieke resources plaatsvinden.

- [Azure Activity Log-gebeurtenissen weergeven en ophalen](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

- [Waarschuwingen maken in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

## <a name="next-steps"></a>Volgende stappen

- Zie de volgende beveiligingscontrole: [logboekregistratie en -controle](security-control-logging-monitoring.md)
