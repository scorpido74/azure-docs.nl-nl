---
title: Azure Load Balancer-componenten
description: Een overzicht van Azure Load Balancer-componenten
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2020
ms.author: allensu
ms.openlocfilehash: 84857315e4b6b4375ed5b78520b4c6ff0d66751a
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684979"
---
# <a name="azure-load-balancer-components"></a>Azure Load Balancer-componenten

Azure Load Balancer bestaat uit enkele belangrijke componenten. Deze kunnen worden geconfigureerd in uw abonnement via Azure Portal, Azure CLI, Azure PowerShell of sjablonen.

## <a name="frontend-ip-configuration"></a>Front-end-IP-configuratie<a name = "frontend-ip-configurations"></a>

Het IP-adres van uw Azure Load Balancer. Dit is het contactpunt voor clients. Deze IP-adressen kunnen een van de volgende zijn:

- **Openbaar IP-adres**
- **Privé IP-adres**

De aard van het IP-adres bepaalt het **type** van de gemaakte load balancer. Met de selectie privé-IP-adres wordt een interne load balancer gemaakt. Met de selectie openbaar-IP-adres wordt een openbare load balancer gemaakt.

|  | Openbare load balancer  | Interne load balancer |
| ---------- | ---------- | ---------- |
| Front-end-IP-configuratie| Openbaar IP-adres | Privé IP-adres|
| Beschrijving | Een openbare load balancer wijst het openbare IP-adres en de poort van binnenkomend verkeer toe aan het privé-IP-adres en de poort van de virtuele machine. Load balancer wijst verkeer de andere kant op in het geval van reactieverkeer vanaf de virtuele machine. Door het toepassen van regels voor taakverdeling, kunt u specifieke soorten verkeer verdelen voor meerdere virtuele machines of services. U kunt bijvoorbeeld de werkbelasting door webverkeeraanvragen over meerdere webservers spreiden.| Een interne load balancer distribueert verkeer naar resources die zich binnen een virtueel netwerk bevinden. Azure beperkt de toegang tot de front-end-IP-adressen van een virtueel netwerk met taakverdeling. Front-end-IP-adressen en virtuele netwerken communiceren nooit rechtstreeks met een interneteindpunt. Interne Line-Of-Business-toepassingen worden in Azure uitgevoerd en worden vanuit Azure of vanaf on-premises resources benaderd. |
| SKU's ondersteund | Basic, Standard | Basic, Standard |

![Voorbeeld van een gelaagde load balancer](./media/load-balancer-overview/load-balancer.png)

## <a name="backend-pool"></a>Back-end-pool

De groep virtuele machines of instanties in een schaalset voor virtuele machines die de inkomende aanvraag afhandelt. Kosteneffectief schalen om te voldoen aan grote volumes binnenkomend verkeer, wordt in computerrichtlijnen meestal aanbevolen om meer instanties toe te voegen aan de back-end-pool.

Load balancer herconfigureert zichzelf onmiddellijk via automatische herconfiguratie wanneer u instanties omhoog of omlaag schaalt. Als u virtuele machines toevoegt aan de back-endpool of eruit verwijdert, wordt de load balancer opnieuw geconfigureerd zonder dat er aanvullende bewerkingen worden uitgevoerd. Het bereik van de back-end-pool is een virtuele machine in het virtuele netwerk.

Wanneer u nadenkt over het ontwerp van uw back-end-pool, kunt u het beste ontwerpen voor het minst aantal afzonderlijke resources voor de back-end-pool om de duur van beheerbewerkingen te optimaliseren. Er is geen verschil in de prestaties of schaal van het gegevensvlak.

## <a name="health-probes"></a>Statuscontroles

Een statuscontrole wordt gebruikt om de status van de instanties in de back-end-pool te bepalen. Wanneer u een load balancer maakt, moet u een statuscontrole configureren die door uw load balancer kan worden gebruikt om te bepalen of een instantie in orde is, zodat er verkeer naartoe kan worden gestuurd.

U kunt de drempelwaarde onjuiste status voor de statuscontroles definiëren. Wanneer een controle niet reageert, stopt de load balancer met het verzenden van nieuwe verbindingen naar de instanties met een onjuiste status. Een controlefout heeft geen invloed op bestaande verbindingen. De verbinding wordt vervolgd totdat de toepassing:

- De stroom beëindigt
- Time-out voor inactiviteit optreedt
- De VM wordt afgesloten

Load balancer biedt verschillende typen statuscontroles voor eindpunten: TCP, HTTP en HTTPS.

Basic Load Balancer biedt geen ondersteuning voor HTTPS-controles. Basic Load Balancer sluit alle TCP-verbindingen (inclusief tot stand gebrachte verbindingen).

## <a name="load-balancing-rules"></a>Taakverdelingsregels

Een taakverdelingsregel wordt gebruikt om te bepalen hoe inkomend verkeer wordt gedistribueerd naar **alle** instanties binnen de back-end-pool. Een taakverdelingsregel wijst een gegeven front-end-IP-configuratie en -poort toe aan meerdere back-end-IP-adressen en -poorten.

Als u bijvoorbeeld verkeer op poort 80 (of een andere poort) van uw front-end-IP wilt routeren naar poort 80 van alle back-end-instanties, gebruikt u hiervoor een taakverdelingsregel.

## <a name="inbound-nat-rules"></a>Inkomende NAT-regels

Via een inkomende NAT-regel wordt binnenkomend verkeer naar een combinatie van een geselecteerd front-end-IP-adres en geselecteerde poort doorgestuurd naar een **specifieke** virtuele machine of instantie in de back-end-pool. Port forwarding wordt gedaan door de dezelfde hash-distributie als taakverdeling.

Als u bijvoorbeeld wilt dat Remote Desktop Protocol (RDP) of SSH-sessies (Secure Shell) VM-instanties in een back-end-pool scheidt. Meerdere interne eindpunten kunnen worden toegewezen aan de verschillende poorten op hetzelfde front-end-IP-adres. De front-end-IP-adressen kunnen worden gebruikt voor het extern beheren van uw VM's zonder een extra jump box.

## <a name="outbound-rules"></a>Regels voor uitgaand verkeer

Met een regel voor uitgaand verkeer wordt de uitgaande netwerkadresomzetting (NAT) geconfigureerd voor alle virtuele machines of instanties die worden geïdentificeerd door de back-end-pool. Hierdoor kunnen instanties in de back-end communiceren (uitgaand) met internet of andere eindpunten.

De Basic Load Balancer biedt geen ondersteuning voor regels voor uitgaand verkeer.

## <a name="next-steps"></a>Volgende stappen

- Zie [Een openbare Standard Load Balancer maken](quickstart-load-balancer-standard-public-portal.md) om aan de slag te gaan met een Load Balancer.
- Meer informatie over [Azure Load Balancer](load-balancer-overview.md).
- Meer informatie over [Openbaar IP-adres](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)
- Meer informatie over [Privé-IP-adres](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)
- Meer informatie over [Load Balancer van het type Standard en beschikbaarheidszones](load-balancer-standard-availability-zones.md).
- Meer informatie over [Diagnostische tests van Standard Load Balancer](load-balancer-standard-diagnostics.md).
- Meer informatie over [TCP opnieuw instellen bij inactiviteit](load-balancer-tcp-reset.md).
- Meer informatie over [Standard Load Balancer met taakverdelingsregels voor poorten met hoge beschikbaarheid](load-balancer-ha-ports-overview.md).
- Meer informatie over het gebruik van [Load Balancer met meerdere front-end-IP-configuraties](load-balancer-multivip-overview.md).
- Meer informatie over [Netwerkbeveiligingsgroepen](../virtual-network/security-overview.md).
- Meer informatie over [Testtypen](load-balancer-custom-probe-overview.md#types).
- Meer informatie over [Beperkingen van Load Balancer](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer).
- Meer informatie over het gebruik van [Port forwarding](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal).
- Meer informatie over [Regels voor uitgaand verkeer van Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview).
