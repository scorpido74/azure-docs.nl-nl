---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 35f506235f698fbcf42308e6f0b0f400e925df29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68361542"
---
### <a name="bastion-tier"></a>Bastion-laag

De bastionhost is een optioneel onderdeel dat u als jumpserver gebruiken om toegang te krijgen tot de toepassings- en database-exemplaren. De bastionhost-VM kan er een openbaar IP-adres aan hebben toegewezen, hoewel de aanbeveling is om een ExpressRoute-verbinding of site-to-site VPN in te stellen met uw on-premises netwerk voor beveiligde toegang. Bovendien mogen alleen SSH (poort 22, Linux) of RDP (poort 3389, Windows Server) worden geopend voor binnenkomend verkeer. Voor hoge beschikbaarheid implementeert u een bastionhost in twee beschikbaarheidszones of in één beschikbaarheidsset.

U ook SSH-agent forwarding inschakelen op uw VM's, waarmee u toegang hebt tot andere VM's in het virtuele netwerk door de referenties van uw bastionhost door te sturen. Of gebruik SSH-tunneling om toegang te krijgen tot andere instanties.

Hier is een voorbeeld van agent forwarding:

```
ssh -A -t user@BASTION_SERVER_IP ssh -A root@TARGET_SERVER_IP`
```

Deze opdracht maakt verbinding met `ssh` het bastion en wordt dan onmiddellijk weer uitgevoerd, zodat u een terminal op de doelinstantie krijgt. Mogelijk moet u een andere gebruiker dan root op de doelinstantie opgeven als uw cluster anders is geconfigureerd. Het `-A` argument stuurt de agentverbinding door, zodat uw privésleutel op uw lokale machine automatisch wordt gebruikt. Houd er rekening mee dat agent `ssh` forwarding `-A` een keten is, dus de tweede opdracht bevat ook zodat eventuele volgende SSH-verbindingen die vanuit de doelinstantie worden gestart, ook uw lokale privésleutel gebruiken.