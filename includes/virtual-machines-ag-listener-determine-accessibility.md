---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8861396db6f6b680ddb55ce020e5579dc25b118e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "67175988"
---
Het is belang rijk om te realiseren dat er twee manieren zijn om een beschikbaarheids groep-listener te configureren in Azure. De manieren verschillen van het type Azure-load balancer dat u gebruikt bij het maken van de listener. In de volgende tabel worden de verschillen beschreven:

| Type Load Balancer | Implementatie | Gebruiken wanneer: |
| --- | --- | --- |
| **Buiten** |Maakt gebruik van het *open bare virtuele IP-adres* van de Cloud service die als host fungeert voor de virtuele machines (vm's). |U moet toegang hebben tot de listener van buiten het virtuele netwerk, met inbegrip van het internet. |
| **Intern** |Maakt gebruik van een *interne Load Balancer* met een privé adres voor de listener. |U kunt alleen toegang krijgen tot de listener vanuit hetzelfde virtuele netwerk. Deze toegang omvat site-naar-site VPN in hybride scenario's. |

> [!IMPORTANT]
> Voor een listener die gebruikmaakt van de open bare VIP van de Cloud service (extern load balancer), zolang de client, listener en data bases zich in dezelfde Azure-regio bevinden, worden er geen kosten in rekening gebracht. Als dat niet het geval is, worden alle gegevens die via de listener worden geretourneerd beschouwd als uitgaand verkeer en worden de normale tarieven voor gegevens overdracht in rekening gebracht. 
> 
> 

Een ILB kan alleen worden geconfigureerd op virtuele netwerken met een regionaal bereik. Bestaande virtuele netwerken die zijn geconfigureerd voor een affiniteits groep kunnen geen ILB gebruiken. Zie [overzicht van interne Load Balancer](../articles/load-balancer/load-balancer-internal-overview.md)voor meer informatie.

