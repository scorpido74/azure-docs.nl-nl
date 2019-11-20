---
title: Wat is het IP-adres 168.63.129.16? | Microsoft Docs
description: Meer informatie over IP-adres 168.63.129.16 en hoe het werkt met uw resources.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: v-jesits
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/15/2019
ms.author: genli
ms.openlocfilehash: e061d503254ba7aa7735a97a060fc63f96b3fb61
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196675"
---
# <a name="what-is-ip-address-1686312916"></a>Wat is het IP-adres 168.63.129.16?

IP-adres 168.63.129.16 is een virtueel openbaar IP-adres dat wordt gebruikt om een communicatie kanaal naar Azure-platform bronnen te vergemakkelijken. Klanten kunnen elke adres ruimte voor hun particuliere virtuele netwerk in azure definiëren. Daarom moeten de resources van het Azure-platform worden gepresenteerd als een uniek openbaar IP-adres. Dit virtuele open bare IP-adres vereenvoudigt de volgende dingen:

- Hiermee kan de VM-agent communiceren met het Azure-platform om aan te geven dat deze de status gereed heeft.
- Maakt communicatie met de virtuele DNS-server mogelijk om gefilterde naam omzetting te bieden aan de resources (zoals VM) die geen aangepaste DNS-server hebben. Deze filtering zorgt ervoor dat klanten alleen de hostnamen van hun resources kunnen omzetten.
- Hiermee worden [status controles van Azure Load Balancer](../load-balancer/load-balancer-custom-probe-overview.md) ingeschakeld om de status van vm's te bepalen.
- Hiermee kan de virtuele machine een dynamisch IP-adres verkrijgen van de DHCP-service in Azure.
- Hiermee schakelt u heartbeat-berichten van de gast agent in voor de functie PaaS.

## <a name="scope-of-ip-address-1686312916"></a>Bereik van IP-adres 168.63.129.16

Het open bare IP-adres 168.63.129.16 wordt gebruikt in alle regio's en alle nationale Clouds. Dit speciale open bare IP-adres is eigendom van micro soft en kan niet worden gewijzigd. Het wordt toegestaan door de standaard regel voor de netwerk beveiligings groep. U wordt aangeraden dit IP-adres in alle lokale firewall-beleids regels in zowel binnenkomende als uitgaande richting toe te staan. De communicatie tussen dit speciale IP-adres en de bronnen is veilig, omdat alleen het interne Azure-platform een bericht kan bron van dit IP-adres. Als dit adres wordt geblokkeerd, kan onverwacht gedrag optreden in verschillende scenario's.
De volgende poorten moeten worden geopend om communicatie met WireServer: 80, 443 en 32526 toe te staan.

[Azure Load Balancer status tests](../load-balancer/load-balancer-custom-probe-overview.md) zijn afkomstig van dit IP-adres. Als u dit IP-adres blokkeert, zullen uw tests mislukken.

In een scenario met een niet-virtueel netwerk (klassiek), wordt de status test gebrond op basis van een privé-IP-adres en 168.63.129.16 wordt niet gebruikt.

## <a name="next-steps"></a>Volgende stappen

- [Beveiligings groepen](security-overview.md)
- [Een netwerkbeveiligingsgroep maken, wijzigen of verwijderen](manage-network-security-group.md)
