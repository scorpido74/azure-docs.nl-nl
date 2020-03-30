---
title: Wat is IP-adres 168.63.129.16? | Microsoft Docs
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
ms.openlocfilehash: 287f881fb17dd84357f540ee562e21c66c11ab95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77114357"
---
# <a name="what-is-ip-address-1686312916"></a>Wat is IP-adres 168.63.129.16?

IP-adres 168.63.129.16 is een virtueel openbaar IP-adres dat wordt gebruikt om een communicatiekanaal naar Azure-platformbronnen te vergemakkelijken. Klanten kunnen elke adresruimte definiëren voor hun privévirtuele netwerk in Azure. Daarom moeten de Azure-platformbronnen worden gepresenteerd als een uniek openbaar IP-adres. Dit virtuele openbare IP-adres faciliteert de volgende dingen:

- Hiermee kan de VM-agent communiceren met het Azure-platform om aan te geven dat het in een status 'Klaar' is.
- Hiermee kan de communicatie met de virtuele DNS-server worden gefilterde naamomzetting bieden aan de bronnen (zoals VM) die geen aangepaste DNS-server hebben. Deze filtering zorgt ervoor dat klanten alleen de hostnamen van hun resources kunnen oplossen.
- Hiermee kunnen [statussen van Azure load balancer](../load-balancer/load-balancer-custom-probe-overview.md) de status van VM's bepalen.
- Hiermee kan de VM een dynamisch IP-adres verkrijgen van de DHCP-service in Azure.
- Hiermee kunnen heartbeatberichten van gastagenten worden weergegeven voor de PaaS-rol.

## <a name="scope-of-ip-address-1686312916"></a>Toepassingsgebied van IP-adres 168.63.129.16

Het openbare IP-adres 168.63.129.16 wordt gebruikt in alle regio's en alle nationale clouds. Dit speciale openbare IP-adres is eigendom van Microsoft en zal niet veranderen. We raden u aan dit IP-adres toe te staan in een lokaal (in het VM)-firewallbeleid (uitgaande richting). De communicatie tussen dit speciale IP-adres en de resources is veilig omdat alleen het interne Azure-platform een bericht kan bron van dit IP-adres. Als dit adres is geblokkeerd, kan onverwacht gedrag optreden in verschillende scenario's. 168.63.129.16 is een [virtueel IP van het hostknooppunt](../virtual-network/security-overview.md#azure-platform-considerations) en is als zodanig niet onderworpen aan door de gebruiker gedefinieerde routes.

- De VM-agent vereist uitgaande communicatie via poorten 80, 443, 32526 met WireServer (168.63.129.16). Deze moeten worden geopend in de lokale firewall op de VM. De communicatie over deze poorten met 168.63.129.16 is niet onderworpen aan de geconfigureerde netwerkbeveiligingsgroepen.
- 168.63.129.16 kan DNS-diensten leveren aan de VM. Als dit niet gewenst is, kan dit verkeer worden geblokkeerd in de lokale firewall op de VM. Standaard is DNS-communicatie niet onderworpen aan de geconfigureerde netwerkbeveiligingsgroepen, tenzij specifiek gericht gebruikmakend van de [AzurePlatformDNS-servicetag.](../virtual-network/service-tags-overview.md#available-service-tags)
- Wanneer de VM deel uitmaakt van een backendpool van load balancer, moet de communicatie [met de statussonde](../load-balancer/load-balancer-custom-probe-overview.md) kunnen ontstaan uit 168.63.129.16. De standaardconfiguratie van de netwerkbeveiligingsgroep heeft een regel die deze communicatie mogelijk maakt. Deze regel maakt gebruik van de [AzureLoadBalancer-servicetag.](../virtual-network/service-tags-overview.md#available-service-tags) Indien gewenst kan dit verkeer worden geblokkeerd door het configureren van de netwerkbeveiligingsgroep, maar dit zal resulteren in sondes die mislukken.

In een niet-virtueel netwerkscenario (Classic) wordt de statussonde afkomstig van een privé-IP en 168.63.129.16 niet gebruikt.

## <a name="next-steps"></a>Volgende stappen

- [Beveiligingsgroepen](security-overview.md)
- [Een netwerkbeveiligingsgroep maken, wijzigen of verwijderen](manage-network-security-group.md)
