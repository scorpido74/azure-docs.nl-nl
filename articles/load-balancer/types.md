---
title: Azure Load Balancer typen
description: Overzicht van Azure Load Balancer typen
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
ms.openlocfilehash: 31e2bf19967bb8870ee6ab75687bb3fcc37373f7
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629971"
---
# <a name="azure-load-balancer-types"></a>Azure Load Balancer typen

Azure Load Balancer heeft twee typen en twee SKU'S.

## <a name="public-load-balancer"></a><a name = "publicloadbalancer"></a>Open bare load balancer

Een openbaar load balancer wijst het open bare IP-adres en de poort van binnenkomend verkeer toe aan het particuliere IP-adres en de poort van de virtuele machine. Load Balancer wijst verkeer toe op de andere manier rond het antwoord verkeer van de virtuele machine. U kunt specifieke typen verkeer distribueren over meerdere Vm's of services door regels voor taak verdeling toe te passen. U kunt bijvoorbeeld de werkbelasting door webverkeeraanvragen over meerdere webservers spreiden.

>[!NOTE]
>U kunt slechts één openbaar load balancer en één interne load balancer implementeren per beschikbaarheidsset.

In de volgende afbeelding ziet u een eind punt met gelijke taak verdeling voor Internet verkeer dat wordt gedeeld door drie Vm's voor de open bare en TCP-poort 80. Deze drie VM's maken deel uit van een set met taakverdeling.

![Public load balancer-voor beeld](./media/load-balancer-overview/load-balancer.png)

*Afbeelding: webverkeer balanceren met behulp van een open bare load balancer*

Internetclients verzenden webpagina aanvragen naar het open bare IP-adres van een web-app op TCP-poort 80. Azure Load Balancer distribueert de aanvragen over de drie Vm's in de set met gelijke taak verdeling. Zie [concepten van Load Balancer](concepts.md)voor meer informatie over Load Balancer-algoritmen.

Azure Load Balancer netwerk verkeer wordt standaard gelijkmatig verdeeld over meerdere VM-exemplaren. U kunt ook sessieaffiniteit configureren. Zie [de distributie modus configureren voor Azure Load Balancer](load-balancer-distribution-mode.md)voor meer informatie.

## <a name="internal-load-balancer"></a><a name = "internalloadbalancer"></a>Interne load balancer

Met een interne load balancer wordt verkeer gedistribueerd naar bronnen die zich binnen een virtueel netwerk bevinden. Azure beperkt de toegang tot de front-end-IP-adressen van een virtueel netwerk waarop taak verdeling van toepassing is. 

Front-end-IP-adressen en virtuele netwerken worden nooit rechtstreeks blootgesteld aan een Internet-eind punt. Interne Line-Of-Business-toepassingen worden in Azure uitgevoerd en worden vanuit Azure of vanaf on-premises resources benaderd.

Een interne load balancer maakt de volgende typen taak verdeling mogelijk:

* **Binnen een virtueel netwerk**: taak verdeling van virtuele machines in het virtuele netwerk naar een set vm's die zich in hetzelfde virtuele netwerk bevinden.
* **Voor een cross-premises virtueel netwerk**: taak verdeling van on-premises computers naar een set vm's die zich in hetzelfde virtuele netwerk bevinden.
* **Voor toepassingen met meerdere lagen**: taak verdeling voor toepassingen met meerdere lagen op internet waarbij de back-endservers niet op internet zijn gericht. Voor de back-end-lagen is taak verdeling van verkeer van de Internet gerichte laag vereist. Zie de volgende afbeelding.
* **Voor Line-Of-Business-toepassingen**: taakverdeling voor Line-Of-Business-toepassingen die worden gehost in Azure zonder extra load balancer-hardware of -software. Dit scenario bevat on-premises servers die zich in de set computers bevinden waarvan het verkeer wordt verdeeld.

![Voorbeeld van een interne Load Balancer](./media/load-balancer-overview/load-balancer.png)

*Afbeelding: toepassingen met meerdere lagen verdelen met behulp van zowel open bare als interne load balancer*

## <a name="load-balancer-sku-comparison"></a><a name="skus"></a> Vergelijking van Load Balancer-SKU's

De Load Balancer ondersteunt zowel de basis-als standaard-Sku's. Deze Sku's wijken af van de schaal, functies en prijzen van het scenario. Elk scenario dat mogelijk is met basis load balancer, kan worden gemaakt met standaard load balancer.

Als u de verschillen met elkaar wilt vergelijken en wilt weten wat ze precies inhouden, kunt u de volgende tabel raadplegen. Zie [overzicht van Azure Standard Load Balancer](load-balancer-standard-overview.md)voor meer informatie.

>[!NOTE]
> Micro soft adviseert standaard load balancer.
Zelfstandige virtuele machines, beschikbaarheidssets en virtuele-machineschaalsets kunnen worden verbonden met slechts één SKU, niet met beide. De Load Balancer en de open bare IP-adres-SKU moeten overeenkomen wanneer u deze gebruikt met open bare IP-adressen. De Load Balancer en de open bare IP-Sku's zijn niet onveranderbaar.

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

Zie [limieten voor load balancers](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer)voor meer informatie. Zie [Overzicht](load-balancer-standard-overview.md), [Prijzen](https://aka.ms/lbpricing) en [SLA](https://aka.ms/lbsla) voor meer details over Standard Load Balancer.

## <a name="next-steps"></a>Volgende stappen

- Zie [een open bare Standard Load Balancer maken](quickstart-load-balancer-standard-public-portal.md) om aan de slag te gaan met het gebruik van een Load Balancer.
- Meer informatie over het gebruik van [Standard Load Balancer en Beschikbaarheidszones](load-balancer-standard-availability-zones.md).
- Meer informatie over [status controles](load-balancer-custom-probe-overview.md).
- Meer informatie over het gebruik [van Load Balancer voor uitgaande verbindingen](load-balancer-outbound-connections.md).
- Meer informatie over Standard Load Balancer met de taakverdelings [regels van ha-poorten](load-balancer-ha-ports-overview.md).
- Meer informatie over [netwerk beveiligings groepen](../virtual-network/security-overview.md).