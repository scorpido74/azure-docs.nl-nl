---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 02/24/2020
ms.topic: include
ms.openlocfilehash: c77849b2285283a34e6adf84dc3845a4076407af
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77597934"
---
## <a name="attack-scenario"></a>Aanvals scenario

Brute force-aanvallen zijn vaak gericht op beheer poorten als middel om toegang te krijgen tot een virtuele machine. Als dit lukt, kan een aanvaller de controle over de virtuele machine overnemen en een aanvaller binnen in uw omgeving tot stand brengen.

Een manier om de bloot stelling aan een beveiligings aanval te verminderen is het beperken van de hoeveelheid tijd die een poort is geopend. Beheer poorten hoeven niet te allen tijde open zijn. Ze hoeven alleen te zijn geopend terwijl u verbonden bent met de virtuele machine, bijvoorbeeld om beheer-of onderhouds taken uit te voeren. Wanneer just-in-time is ingeschakeld, maakt Security Center gebruik van [netwerk beveiligings groep](../articles/virtual-network/security-overview.md#security-rules) (NSG) en Azure firewall regels, waarmee de toegang tot beheer poorten wordt beperkt zodat deze niet kunnen worden benaderd door aanvallers.

![Just-in-time-scenario](../articles/security-center/media/security-center-just-in-time/just-in-time-scenario.png)

## <a name="how-does-jit-access-work"></a>Hoe werkt JIT-toegang?

Wanneer just-in-time is ingeschakeld, wordt binnenkomend verkeer naar uw Azure-Vm's door Security Center vergrendeld door een NSG-regel te maken. U selecteert de poorten op de VM waarop het inkomende verkeer wordt vergrendeld. Deze poorten worden bepaald door de just-in-time-oplossing.

Wanneer een gebruiker toegang tot een virtuele machine vraagt, controleert Security Center of de gebruiker [op rollen gebaseerde Access Control (RBAC)-](../articles/role-based-access-control/role-assignments-portal.md) machtigingen voor die VM heeft. Als de aanvraag is goedgekeurd, Security Center automatisch de netwerk beveiligings groepen (Nsg's) en Azure Firewall zodanig configureren dat inkomend verkeer naar de geselecteerde poorten en aangevraagde bron-IP-adressen of-bereiken worden toegestaan voor de opgegeven hoeveelheid tijd. Nadat de tijd is verstreken, wordt de Nsg's door Security Center teruggezet naar de vorige status. Deze verbindingen die al tot stand zijn gebracht, worden echter niet onderbroken.

 > [!NOTE]
 > Als een JIT-toegangs aanvraag wordt goedgekeurd voor een virtuele machine achter een Azure Firewall, wijzigt Security Center automatisch de beleids regels NSG en firewall. Voor de hoeveelheid tijd die is opgegeven, staan de regels binnenkomend verkeer naar de geselecteerde poorten toe en aangevraagde IP-adressen of bereiken van de bron. Nadat de tijd is overschreden Security Center, worden de firewall-en NSG-regels teruggezet naar de vorige status.


## <a name="permissions-needed-to-configure-and-use-jit"></a>Benodigde machtigingen voor het configureren en gebruiken van JIT

| Een gebruiker in staat stellen: | Machtigingen om in te stellen|
| --- | --- |
| Een JIT-beleid voor een virtuele machine configureren of bewerken | *Wijs deze acties toe aan de rol:*  <ul><li>Binnen het bereik van een abonnement of resource groep die is gekoppeld aan de virtuele machine:<br/> `Microsoft.Security/locations/jitNetworkAccessPolicies/write` </li><li> Binnen het bereik van een abonnement of resource groep van de VM: <br/>`Microsoft.Compute/virtualMachines/write`</li></ul> | 
|JIT-toegang aanvragen voor een virtuele machine | *Deze acties toewijzen aan de gebruiker:*  <ul><li>Binnen het bereik van een abonnement of resource groep die is gekoppeld aan de virtuele machine:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action` </li><li>Binnen het bereik van een abonnement of resource groep die is gekoppeld aan de virtuele machine:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/*/read` </li><li>  Binnen het bereik van een abonnement of resource groep of VM:<br/> `Microsoft.Compute/virtualMachines/read` </li><li>  Binnen het bereik van een abonnement of resource groep of VM:<br/> `Microsoft.Network/networkInterfaces/*/read` </li></ul>|