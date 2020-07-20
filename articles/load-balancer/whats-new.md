---
title: Wat is er nieuw in Azure Load Balancer
description: Ontdek wat er nieuw is in Azure Load Balancer, zoals de laatste opmerkingen bij de release, bekende problemen, opgeloste problemen, verminderde functionaliteit en aankomende wijzigingen.
services: load-balancer
author: anavinahar
ms.service: load-balancer
ms.topic: overview
ms.date: 07/07/2020
ms.author: anavin
ms.openlocfilehash: 4725aaaf7a2c1e0aa49ea8dbe046b720727a5d54
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147472"
---
# <a name="whats-new-in-azure-load-balancer"></a>Wat is er nieuw in Azure Load Balancer?

Azure Load Balancer wordt regelmatig bijgewerkt. Blijf op de hoogte van de laatste aankondigingen. In dit artikel vindt u informatie over:

- De nieuwste releases
- Bekende problemen
- Opgeloste fouten
- Afgeschafte functionaliteit (indien van toepassing)

[Hier](https://azure.microsoft.com/updates/?category=networking&query=load%20balancer) vindt u de meest recente updates voor Azure Load Balancer en kunt u zich abonneren op de RSS-feed.

## <a name="new-features"></a>Nieuwe functies

|Functie  |Beschrijving  |Datum toegevoegd  |
|---------|---------|---------|
| Ondersteuning voor IP-gebaseerd beheer van back-endpools (preview) | Azure Load Balancer biedt ondersteuning voor het toevoegen en verwijderen van resources uit een back-endpool via een IPv4-of IPv6-adres. Dit stelt u in staat om containers, virtuele machines en virtuele-machineschaalset voor Load Balancer eenvoudig te beheren. Ook kunnen IP-adressen worden gereserveerd als onderdeel van een back-endpool voordat de gekoppelde resources worden aangemaakt. | Juli 2020 |
| Inzichten Azure Load Balancer-inzichten met Azure Monitor | Het is ontworpen als onderdeel van Azure Monitor voor netwerken, en biedt klanten topologische kaarten voor al hun Load Balancer-configuraties en statusdashboards voor Standard Load Balancers die zijn vooraf zijn geconfigureerd met metrische gegevens in het Azure-portal. [Aan de slag en meer informatie](https://azure.microsoft.com/blog/introducing-azure-load-balancer-insights-using-azure-monitor-for-networks/) | Juni 2020 |
| IPv6-ondersteuning voor Azure Load Balancer (algemeen beschikbaar) | U kunt IPv6-adressen gebruiken als front-end voor uw Azure Load Balancers. Meer informatie over het [maken van een dual stack-toepassing hier](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) |April 2020|
| Opnieuw instellen van TCP bij Time-out voor inactiviteit (algemeen beschikbaar)| Gebruik Opnieuw instellen van TCP om he gedrag van de toepassing voorspelbaarder te maken. [Meer informatie](load-balancer-tcp-reset.md)| Februari 2020 |

## <a name="next-steps"></a>Volgende stappen

Zie [Wat is Azure Load Balancer?](load-balancer-overview.md) en [veelgestelde vragen](load-balancer-faqs.md) voor meer informatie over Azure Load Balancer.