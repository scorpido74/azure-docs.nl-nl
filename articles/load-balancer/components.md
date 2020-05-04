---
title: Azure Load Balancer onderdelen
description: Overzicht van Azure Load Balancer onderdelen
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
ms.openlocfilehash: 4a84c43b57ec4f632a2bfabb10d112e4975249bf
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/03/2020
ms.locfileid: "82733104"
---
# <a name="azure-load-balancer-components"></a>Azure Load Balancer onderdelen

Azure Load Balancer bevat verschillende belang rijke onderdelen voor de werking ervan. Deze onderdelen kunnen worden geconfigureerd in uw abonnement via Azure Portal, Azure CLI, Azure PowerShell of sjablonen.

## <a name="frontend-ip-configurations"></a>Front-end-IP-configuraties

Het IP-adres van de load balancer. Het is het contact punt voor clients. Deze adressen kunnen een van de volgende zijn:

- **Openbaar IP-adres**
- **Privé-IP-adres**

De selectie van het IP-adres bepaalt het **type** Load Balancer gemaakt. Met de selectie van privé-IP-adressen maakt u een interne load balancer. Met de selectie van een openbaar IP-adres maakt u een open bare load balancer.

|  | Openbare load balancer  | Interne load balancer |
| ---------- | ---------- | ---------- |
| Front-end-IP-configuratie| Openbaar IP-adres | Privé IP-adres|
| Beschrijving | Een openbaar load balancer wijst het open bare IP-adres en de poort van binnenkomend verkeer toe aan het particuliere IP-adres en de poort van de virtuele machine. Load Balancer wijst verkeer toe op de andere manier rond het antwoord verkeer van de virtuele machine. U kunt specifieke typen verkeer distribueren over meerdere Vm's of services door de regels voor taak verdeling toe te passen. U kunt bijvoorbeeld de werkbelasting door webverkeeraanvragen over meerdere webservers spreiden.| Met een interne load balancer wordt verkeer gedistribueerd naar bronnen die zich binnen een virtueel netwerk bevinden. Azure beperkt de toegang tot de frontend-IP-adressen van een virtueel netwerk waarop taak verdeling van toepassing is. Front-end-IP-adressen en virtuele netwerken worden nooit rechtstreeks blootgesteld aan een Internet-eind punt. Interne Line-Of-Business-toepassingen worden in Azure uitgevoerd en worden vanuit Azure of vanaf on-premises resources benaderd. |
| Sku's ondersteund | Basis, standaard | Basis, standaard |

![Voor beeld van een gelaagde load balancer](./media/load-balancer-overview/load-balancer.png)

## <a name="backend-pool"></a>Back-endpool

De groep virtuele machines of exemplaren in een schaalset voor virtuele machines die de inkomende aanvraag verstuurt. Om de kosten effectief te schalen om te voldoen aan grote volumes van binnenkomend verkeer, raden computer richtlijnen meestal toe om meer exemplaren toe te voegen aan de back-end-groep. 

De Load Balancer wordt direct opnieuw geconfigureerd via automatische herconfiguratie wanneer u instanties omhoog of omlaag schaalt. Als u Vm's toevoegt aan of verwijdert uit de back-end-groep, worden de load balancer opnieuw geconfigureerd zonder dat er extra bewerkingen worden uitgevoerd. Het bereik van de back-end-groep is een virtuele machine in het virtuele netwerk. 

Als u overweegt om uw back-end-pool te ontwerpen, ontwerp dan het minste aantal afzonderlijke back-endservers bronnen om de lengte van beheer bewerkingen te optimaliseren. Er is geen verschil in de prestaties of schaal van het gegevens vlak.

## <a name="health-probes"></a>Statuscontroles

Een status test wordt gebruikt om de status van de exemplaren in de back-end-pool te bepalen. U kunt de drempel waarde voor de status van uw Health-tests definiëren. Wanneer een test niet reageert, wordt het verzenden van nieuwe verbindingen naar de beschadigde instanties gestopt door de load balancer. Een test fout heeft geen invloed op bestaande verbindingen. De verbinding wordt vervolgd totdat de toepassing:

- Hiermee wordt de stroom beëindigd
- Time-out voor inactiviteit treedt op
- De VM wordt afgesloten

Load Balancer biedt verschillende typen Health probe voor eind punten:

- TCP
- HTTP
- HTTPS

De Basic-load balancer biedt geen ondersteuning voor HTTPS-tests. Basic load balancer sluit alle TCP-verbindingen (inclusief tot stand gebrachte verbindingen).

## <a name="load-balancing-rules"></a>Taakverdelings regels

Met taakverdelings regels wordt het load balancer wat er moet gebeuren. Een taakverdelings regel wijst een opgegeven frontend-IP-configuratie en-poort toe aan meerdere back-end-IP-adressen en poorten.

## <a name="inbound-nat-rules"></a>Inkomende NAT-regels

Binnenkomende NAT-regels sturen verkeer van het frontend-IP-adres naar een back-end-exemplaar in het virtuele netwerk. Het door sturen van poorten wordt uitgevoerd door dezelfde hash-gebaseerde distributie als taak verdeling. 

Voor beeld van het gebruik van is Remote Desktop Protocol (RDP) of SSH-sessies (Secure Shell) voor het scheiden van VM-exemplaren in een virtueel netwerk. Meerdere interne eind punten kunnen worden toegewezen aan poorten op hetzelfde front-end-IP-adres. De front-end-IP-adressen kunnen worden gebruikt voor het extern beheren van uw Vm's zonder een extra Jump box.

## <a name="outbound-rules"></a>Uitgaande regels

Met een uitgaande regel wordt de uitgaande netwerkadresomzetting (NAT) geconfigureerd voor alle virtuele machines of exemplaren die worden geïdentificeerd door de back-end-pool.

Basic load balancer biedt geen ondersteuning voor uitgaande regels.
![Azure Load Balancer](./media/load-balancer-overview/load-balancer-overview.png)

## <a name="next-steps"></a>Volgende stappen

- Zie [een open bare Standard Load Balancer maken](quickstart-load-balancer-standard-public-portal.md) om aan de slag te gaan met het gebruik van een Load Balancer.
- Meer informatie over [Azure Load Balancer](load-balancer-overview.md).
- Meer informatie over [openbaar IP-adres](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)
- Meer informatie over [privé-IP-adres](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)
- Meer informatie over het gebruik van [Standard Load Balancer en Beschikbaarheidszones](load-balancer-standard-availability-zones.md).
- Meer informatie over [Standard Load Balancer diagnostische gegevens](load-balancer-standard-diagnostics.md).
- Meer informatie over [TCP Reset bij niet-actief](load-balancer-tcp-reset.md).
- Meer informatie over Standard Load Balancer met de taakverdelings [regels van ha-poorten](load-balancer-ha-ports-overview.md).
- Meer informatie over het gebruik van [Load Balancer met meerdere frontends](load-balancer-multivip-overview.md).
- Meer informatie over [netwerk beveiligings groepen](../virtual-network/security-overview.md).
- Meer informatie over [test typen](load-balancer-custom-probe-overview.md#types).
- Meer informatie over de [limieten van load balancers](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer).
- Meer informatie over het gebruik van [Port Forwarding](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal).
- Meer informatie over de [Uitgaande regels van Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview).
