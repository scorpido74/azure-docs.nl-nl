---
title: Wat is het IP-adres 168.63.129.16? | Microsoft Docs
description: Meer informatie over IP-adres 168.63.129.16, met name voor het gebruik van een communicatie kanaal naar Azure-platform bronnen.
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
ms.openlocfilehash: 0f0bfa693086a3a097df219132d696a1d04e6f56
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87286033"
---
# <a name="what-is-ip-address-1686312916"></a>Wat is het IP-adres 168.63.129.16?

IP-adres 168.63.129.16 is een virtueel openbaar IP-adres dat wordt gebruikt om een communicatie kanaal naar Azure-platform bronnen te vergemakkelijken. Klanten kunnen elke adres ruimte voor hun particuliere virtuele netwerk in azure definiëren. Daarom moeten de resources van het Azure-platform worden gepresenteerd als een uniek openbaar IP-adres. Dit virtuele open bare IP-adres vereenvoudigt de volgende dingen:

- Hiermee kan de VM-agent communiceren met het Azure-platform om aan te geven dat deze de status gereed heeft.
- Maakt communicatie met de virtuele DNS-server mogelijk om gefilterde naam omzetting te bieden aan de resources (zoals VM) die geen aangepaste DNS-server hebben. Deze filtering zorgt ervoor dat klanten alleen de hostnamen van hun resources kunnen omzetten.
- Hiermee worden [status controles van Azure Load Balancer](../load-balancer/load-balancer-custom-probe-overview.md) ingeschakeld om de status van vm's te bepalen.
- Hiermee kan de virtuele machine een dynamisch IP-adres verkrijgen van de DHCP-service in Azure.
- Hiermee schakelt u heartbeat-berichten van de gast agent in voor de functie PaaS.

## <a name="scope-of-ip-address-1686312916"></a>Bereik van IP-adres 168.63.129.16

Het open bare IP-adres 168.63.129.16 wordt gebruikt in alle regio's en alle nationale Clouds. Dit speciale open bare IP-adres is eigendom van micro soft en kan niet worden gewijzigd. U wordt aangeraden dit IP-adres toe te staan in een lokaal (in het VM-) firewall beleid (uitgaande richting). De communicatie tussen dit speciale IP-adres en de bronnen is veilig, omdat alleen het interne Azure-platform een bericht kan bron van dit IP-adres. Als dit adres wordt geblokkeerd, kan onverwacht gedrag optreden in verschillende scenario's. 168.63.129.16 is een [virtueel IP-adres van het host-knoop punt](../virtual-network/security-overview.md#azure-platform-considerations) en is niet onderhevig aan door de gebruiker gedefinieerde routes.

- De VM-agent vereist uitgaande communicatie via de poorten 80, 443, 32526 met WireServer (168.63.129.16). Deze moeten worden geopend in de lokale firewall op de VM. De communicatie over deze poorten met 168.63.129.16 is niet van toepassing op de geconfigureerde netwerk beveiligings groepen.
- 168.63.129.16 kunnen DNS-services leveren aan de virtuele machine. Als dit niet gewenst is, kan dit verkeer worden geblokkeerd in de lokale firewall op de VM. Standaard is DNS-communicatie niet van toepassing op de geconfigureerde netwerk beveiligings groepen, tenzij specifiek is gericht op het [AzurePlatformDNS](../virtual-network/service-tags-overview.md#available-service-tags) -service label. Als u DNS-verkeer wilt blok keren naar Azure DNS via NSG, maakt u een regel voor uitgaande verbindingen om verkeer te weigeren aan [AzurePlatformDNS](../virtual-network/service-tags-overview.md#available-service-tags)en geeft u "*" op als "doel poort bereik" en "elk" als protocol.
- Wanneer de virtuele machine deel uitmaakt van een load balancer back-end-groep, moet de [status test](../load-balancer/load-balancer-custom-probe-overview.md) communicatie worden toegestaan vanuit 168.63.129.16. De standaard configuratie van de netwerk beveiligings groep heeft een regel die deze communicatie mogelijk maakt. Deze regel maakt gebruik van het [AzureLoadBalancer](../virtual-network/service-tags-overview.md#available-service-tags) -service label. Als dit verkeer gewenst kan worden geblokkeerd door de netwerk beveiligings groep te configureren, resulteert dit echter in tests die mislukken.

In een scenario met een niet-virtueel netwerk (klassiek), wordt de status test gebrond op basis van een privé-IP-adres en 168.63.129.16 wordt niet gebruikt.

## <a name="next-steps"></a>Volgende stappen

- [Beveiligings groepen](security-overview.md)
- [Een netwerkbeveiligingsgroep maken, wijzigen of verwijderen](manage-network-security-group.md)
