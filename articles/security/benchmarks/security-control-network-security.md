---
title: Beveiligings beheer van Azure-netwerk beveiliging
description: Beveiligings beheer netwerk beveiliging
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 7916bbb28602d64e0916fce7badf16a65c242227
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77251869"
---
# <a name="security-control-network-security"></a>Beveiligings beheer: netwerk beveiliging

De aanbevelingen voor netwerk beveiliging richten zich op het opgeven van de netwerk protocollen, TCP/UDP-poorten en verbonden netwerk services zijn toegestaan of geweigerd toegang tot Azure-Services.

## <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Beveilig bronnen met behulp van netwerk beveiligings groepen of Azure Firewall op de Virtual Network

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 1.1 | 9,2, 9,4, 14,1-14.3 | Klant |

Zorg ervoor dat alle implementaties van Virtual Network subnet een netwerk beveiligings groep hebben die is toegepast op de vertrouwde poorten en bronnen van uw toepassing. Gebruik Azure-Services met een persoonlijke koppeling ingeschakeld, implementeer de service binnen uw Vnet of maak privé verbinding met behulp van privé-eind punten. Raadpleeg voor specifieke vereisten voor de service de beveiligings aanbeveling voor die specifieke service.

Als u een specifieke use-case hebt, kunnen aan vereisten worden voldaan door de implementatie van Azure Firewall.

Algemene informatie over privé-koppeling:

https://docs.microsoft.com/azure/private-link/private-link-overview

Een Virtual Network maken:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Een NSG maken met een beveiligings configuratie:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Azure Firewall implementeren en configureren:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: de configuratie en het verkeer van Vnets, subnetten en Nic's bewaken en vastleggen

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 1.2 | 9,3, 12,2 | Klant |

Gebruik Azure Security Center en volg aanbevelingen voor netwerk beveiliging om uw netwerk bronnen in azure te beveiligen. Schakel logboeken voor NSG-stroom in en verzend logboeken naar een opslag account voor verkeers controle.

NSG-stroom logboeken inschakelen:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Informatie over netwerk beveiliging van Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

## <a name="13-protect-critical-web-applications"></a>1,3: essentiële webtoepassingen beveiligen

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 1.3 | 9.5 | Klant |

Implementeer Azure Web Application firewall (WAF) voor essentiële webtoepassingen voor extra inspectie van inkomend verkeer. Schakel de diagnostische instelling voor WAF-en opname Logboeken in een opslag account, Event hub of Log Analytics werk ruimte in.

Azure WAF implementeren:

https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: communicatie met bekende schadelijke IP-adressen weigeren

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 1.4 | 12,3 | Klant |

Schakel DDoS Standard Protection in op uw virtuele Azure-netwerken om tegen DDoS-aanvallen te beschermen. Gebruik Azure Security Center geïntegreerde bedreigings informatie om communicatie met bekende schadelijke IP-adressen te weigeren.

Implementeer Azure Firewall op alle netwerk grenzen van de organisatie met behulp van bedreigings informatie en geconfigureerd voor &quot;waarschuwing en weiger&quot; voor schadelijk netwerk verkeer.

Gebruik Azure Security Center just-in-time-netwerk toegang om Nsg's te configureren om de bloot stelling van eind punten te beperken tot goedgekeurde IP-adressen gedurende een beperkte periode.

Gebruik Azure Security Center adaptieve netwerk beveiliging om NSG-configuraties aan te bevelen die poorten en bron-Ip's beperken op basis van daad werkelijk verkeer en bedreigings informatie.

DDoS-beveiliging configureren:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Azure Firewall implementeren:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Meer informatie over Azure Security Center geïntegreerde bedreigings informatie:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

Meer informatie over Azure Security Center adaptieve netwerk beveiliging:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening

Meer informatie over Azure Security Center just-in-time-netwerk Access Control:

https://docs.microsoft.com/azure/security-center/security-center-just-in-time

## <a name="15-record-network-packets-and-flow-logs"></a>1,5: netwerk pakketten en stroom logboeken vastleggen

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 1.5 | 12,5, 15,8 | Klant |

Registreer NSG-stroom Logboeken in een opslag account om stroom records te genereren. Als dit nodig is voor het onderzoeken van afwijkende activiteiten, schakelt u Network Watcher pakket vastleggen in.

NSG-stroom logboeken inschakelen:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Network Watcher inschakelen:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: op netwerk gebaseerde inbreuk detectie/indringings systemen (ID'S/IP-adressen) implementeren

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 1.6 | 12,6, 12,7 | Klant |

Implementeer Azure Firewall op alle netwerk grenzen van de organisatie met behulp van bedreigings informatie en geconfigureerd voor &quot;waarschuwing en weiger&quot; voor schadelijk netwerk verkeer.

Azure Firewall implementeren: https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Waarschuwingen configureren met Azure Firewall: https://docs.microsoft.com/azure/firewall/threat-intel

## <a name="17-manage-traffic-to-web-applications"></a>1,7: verkeer naar webtoepassingen beheren

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 1.7 | 12,9, 12,10 | Klant |

Implementeer Azure-toepassing gateway voor webtoepassingen waarvoor HTTPS/SSL is ingeschakeld voor vertrouwde certificaten.

Application Gateway implementeren:

https://docs.microsoft.com/azure/application-gateway/quick-create-portal

Application Gateway configureren voor het gebruik van HTTPS:

https://docs.microsoft.com/azure/application-gateway/create-ssl-portal

De taak verdeling van laag 7 met Azure Web Application-gateways begrijpen:

https://docs.microsoft.com/azure/application-gateway/overview

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: de complexiteit en administratieve overhead van netwerk beveiligings regels minimaliseren

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 1.8 | 1.5 | Klant |

Gebruik Virtual Network Service Tags om netwerk toegangs beheer te definiëren voor netwerk beveiligings groepen of Azure Firewall. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de service label (bijvoorbeeld ApiManagement) op te geven in het juiste bron-of doel veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen.

Service Tags begrijpen en gebruiken:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: standaard beveiligings configuraties voor netwerk apparaten onderhouden

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 1.9 | 11,1 | Klant |

Definieer en implementeer standaard beveiligings configuraties voor netwerk bronnen met Azure Policy.

U kunt ook Azure-blauw drukken gebruiken om grootschalige Azure-implementaties te vereenvoudigen door sleutel omgevings artefacten, zoals Azure Resource Manager sjablonen, RBAC-besturings elementen en-beleid, in één blauw definitie te voorzien. U kunt de blauw druk Toep assen op nieuwe abonnementen en beheer en beheer nauw keuriger beheren via versie beheer.

Azure Policy configureren en beheren:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy voor beelden voor netwerken:

https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network

Een Azure Blueprint maken:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

## <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 1.1 | 11,2 | Klant |

Gebruik labels voor Nsg's en andere bronnen die betrekking hebben op netwerk beveiliging en verkeers stroom. Gebruik voor afzonderlijke NSG-regels het veld &quot;beschrijving&quot; om de bedrijfs behoefte en/of-duur op te geven voor alle regels die verkeer van of naar een netwerk toestaan.

Tags maken en gebruiken:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Een Virtual Network maken:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Een NSG maken met een beveiligings configuratie:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 1,11 | 11,3 | Klant |

Gebruik Azure Policy om de configuratie van netwerk bronnen te valideren (en/of te herstellen).

Azure Policy configureren en beheren:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy voor beelden voor netwerken:

https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network

## <a name="next-steps"></a>Volgende stappen

- Zie het volgende beveiligings beheer: [logboek registratie en controle](security-control-logging-monitoring.md)
