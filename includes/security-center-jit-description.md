---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 02/24/2020
ms.topic: include
ms.openlocfilehash: c77849b2285283a34e6adf84dc3845a4076407af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597934"
---
## <a name="attack-scenario"></a>Aanvalsscenario

Brute force-aanvallen richten vaak op beheerpoorten als een middel om toegang te krijgen tot een VM. Als dit lukt, kan een aanvaller de controle over de VM overnemen en voet aan de grond krijgen in uw omgeving.

Een manier om de blootstelling aan een brute force-aanval te verminderen, is door de hoeveelheid tijd die een poort opent te beperken. Beheerpoorten hoeven niet altijd open te zijn. Ze hoeven alleen open te zijn als u bent verbonden met de VM, bijvoorbeeld om beheer- of onderhoudstaken uit te voeren. Wanneer just-in-time is ingeschakeld, maakt Security Center gebruik van NSG-regels [(Network Security Group)](../articles/virtual-network/security-overview.md#security-rules) en Azure Firewall, die de toegang tot beheerpoorten beperken, zodat ze niet door aanvallers kunnen worden getarget.

![Just-in-time scenario](../articles/security-center/media/security-center-just-in-time/just-in-time-scenario.png)

## <a name="how-does-jit-access-work"></a>Hoe werkt JIT-toegang?

Wanneer just-in-time is ingeschakeld, vergrendelt Security Center binnenkomend verkeer naar uw Azure VM's door een NSG-regel te maken. U selecteert de poorten op de VM waaraan binnenkomend verkeer wordt vergrendeld. Deze poorten worden gecontroleerd door de just-in-time oplossing.

Wanneer een gebruiker toegang vraagt tot een VM, controleert beveiligingscentrum of de gebruiker [RBAC-machtigingen (Role-Based Access Control)](../articles/role-based-access-control/role-assignments-portal.md) heeft voor die VM. Als de aanvraag is goedgekeurd, configureert Security Center automatisch de Network Security Groups (NSGs) en Azure Firewall om binnenkomend verkeer toe te staan naar de geselecteerde poorten en gevraagde bron-IP-adressen of -bereiken, voor de hoeveelheid tijd die is opgegeven. Nadat de tijd is verstreken, herstelt Security Center de NSG's naar hun vorige staten. De reeds bestaande verbindingen worden echter niet onderbroken.

 > [!NOTE]
 > Als een JIT-toegangsaanvraag is goedgekeurd voor een VM achter een Azure Firewall, wijzigt Beveiligingscentrum automatisch zowel de NSG- als de firewallbeleidsregelregels. Voor de opgegeven tijd staan de regels binnenkomend verkeer naar de geselecteerde poorten en gevraagde bron-IP-adressen of bereiken toe. Nadat de tijd voorbij is, herstelt Security Center de firewall- en NSG-regels naar hun vorige status.


## <a name="permissions-needed-to-configure-and-use-jit"></a>Machtigingen die nodig zijn voor het configureren en gebruiken van JIT

| Een gebruiker inschakelen om: | Machtigingen voor het instellen|
| --- | --- |
| Een JIT-beleid voor een virtuele machine configureren of bewerken | *Wijs deze acties toe aan de rol:*  <ul><li>Over het bereik van een abonnement of resourcegroep die is gekoppeld aan de VM:<br/> `Microsoft.Security/locations/jitNetworkAccessPolicies/write` </li><li> Over het bereik van een abonnement of resourcegroep van VM: <br/>`Microsoft.Compute/virtualMachines/write`</li></ul> | 
|JIT-toegang tot een vm aanvragen | *Wijs deze acties toe aan de gebruiker:*  <ul><li>Over het bereik van een abonnement of resourcegroep die is gekoppeld aan de VM:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action` </li><li>Over het bereik van een abonnement of resourcegroep die is gekoppeld aan de VM:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/*/read` </li><li>  Over het bereik van een abonnement of resourcegroep of VM:<br/> `Microsoft.Compute/virtualMachines/read` </li><li>  Over het bereik van een abonnement of resourcegroep of VM:<br/> `Microsoft.Network/networkInterfaces/*/read` </li></ul>|