---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8861396db6f6b680ddb55ce020e5579dc25b118e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175988"
---
Het is belangrijk om te beseffen dat er twee manieren zijn om een authenticiteitsgroeplistener in Azure te configureren. De manieren verschillen in het type Azure-loadbalancer dat u gebruikt wanneer u de listener maakt. In de volgende tabel worden de verschillen beschreven:

| Type load balancer | Implementatie | Gebruiken wanneer: |
| --- | --- | --- |
| **Externe** |Gebruikt het *openbare virtuele IP-adres* van de cloudservice dat de virtuele machines (VM's) host. |U moet toegang krijgen tot de luisteraar van buiten het virtuele netwerk, ook vanaf het internet. |
| **Intern** |Gebruikt een *interne load balancer* met een privéadres voor de luisteraar. |U hebt alleen toegang tot de listener vanuit hetzelfde virtuele netwerk. Deze toegang omvat site-to-site VPN in hybride scenario's. |

> [!IMPORTANT]
> Voor een listener die gebruikmaakt van de openbare VIP (externe load balancer) van de cloudservice, hoeft u geen kosten in rekening te brengen zolang de client, listener en databases zich in dezelfde Azure-regio bevinden. Anders worden alle gegevens die via de listener worden geretourneerd, als uitgang beschouwd en worden deze in rekening gebracht tegen normale gegevensoverdrachtssnelheden. 
> 
> 

Een ILB kan alleen worden geconfigureerd op virtuele netwerken met een regionaal bereik. Bestaande virtuele netwerken die zijn geconfigureerd voor een affiniteitsgroep, kunnen geen ILB gebruiken. Zie [Overzicht interne load balancer](../articles/load-balancer/load-balancer-internal-overview.md)voor meer informatie .

