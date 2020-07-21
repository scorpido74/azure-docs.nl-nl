---
title: Meer informatie over just-in-time toegang tot virtuele machines in Azure Security Center
description: In dit document wordt uitgelegd hoe just-in-time-VM-toegang in Azure Security Center u de toegang tot uw virtuele Azure-machines kunt beheren
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: 50398632f47d889ecb79b32faef94c9c5923789c
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531907"
---
# <a name="understanding-just-in-time-jit-vm-access"></a>Meer informatie over just-in-time-VM-toegang

Op deze pagina worden de principes van de toegang tot de VM-functie just-in-time (JIT) van Azure Security Center en de logica achter de aanbeveling uitgelegd.

Zie [How to Beveilig your beheer ports with JIT](security-center-just-in-time.md)(Engelstalig) voor meer informatie over het Toep assen van JIT op uw virtuele machines met behulp van de Azure Portal (Security Center of Azure virtual machines) of via een programma.


## <a name="the-risk-of-open-management-ports-on-a-virtual-machine"></a>Het risico van open-beheer poorten op een virtuele machine

Threat actors kunnen met open-beheer poorten, zoals RDP of SSH, actieve apparaten actief opsporen. Al uw virtuele machines zijn potentiële doelen voor een aanval. Wanneer een virtuele machine is aangetast, wordt deze als toegangs punt gebruikt om verdere resources in uw omgeving aan te vallen.



## <a name="why-jit-vm-access-is-the-solution"></a>Waarom de JIT-VM-toegang de oplossing is 

Net als bij alle technieken voor het voor komen van Cyber beveiliging moet uw doel stelling de kwets baarheid voor aanvallen verminderen. In dit geval betekent dat minder open poorten, met name beheer poorten.

Uw rechtmatige gebruikers gebruiken deze poorten ook, dus het is niet praktisch om ze te sluiten.

Azure Security Center biedt JIT om deze dilemma op te lossen. Met JIT kunt u het inkomende verkeer naar uw Vm's vergren delen, waardoor de bloot stelling aan aanvallen wordt verkleind, terwijl u eenvoudig toegang hebt tot virtuele machines wanneer dat nodig is.



## <a name="how-jit-operates-with-network-security-groups-and-azure-firewall"></a>Hoe JIT werkt met netwerk beveiligings groepen en Azure Firewall

Wanneer u just-in-time-VM-toegang inschakelt, kunt u de poorten op de VM selecteren waarop binnenkomend verkeer wordt geblokkeerd. Security Center zorgt ervoor dat de regels voor het weigeren van binnenkomend verkeer voor de geselecteerde poorten bestaan in de [netwerk beveiligings groep](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) (NSG) en [Azure firewall regels](https://docs.microsoft.com/azure/firewall/rule-processing). Deze regels beperken de toegang tot de beheer poorten van uw Azure-Vm's en beschermen ze tegen aanvallen. 

Als er al andere regels bestaan voor de geselecteerde poorten, hebben die bestaande regels voor rang op de nieuwe regels ' alle inkomende verkeer weigeren '. Als er geen bestaande regels zijn op de geselecteerde poorten, hebben de nieuwe regels de hoogste prioriteit in de NSG en Azure Firewall.

Wanneer een gebruiker toegang tot een virtuele machine vraagt, controleert Security Center of de gebruiker [op rollen gebaseerde Access Control (RBAC)-](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) machtigingen voor die VM heeft. Als de aanvraag is goedgekeurd, Security Center configureert de Nsg's en Azure Firewall om binnenkomend verkeer naar de geselecteerde poorten van het betreffende IP-adres (of bereik) toe te staan voor de opgegeven tijd. Nadat de tijd is verstreken, wordt de Nsg's door Security Center teruggezet naar de vorige status. Verbindingen die al zijn gemaakt, worden niet onderbroken.

> [!NOTE]
> JIT ondersteunt geen Vm's die worden beveiligd door Azure-firewalls die worden beheerd door [Azure firewall Manager](https://docs.microsoft.com/azure/firewall-manager/overview).




## <a name="how-security-center-identifies-which-vms-should-have-jit-applied"></a>Hoe Security Center identificeert welke Vm's JIT moeten Toep assen

In het onderstaande diagram ziet u de logica die Security Center van toepassing is wanneer u bepaalt hoe u uw ondersteunde Vm's wilt categoriseren: 

[![Just-in-time (JIT) logische stroom van virtuele machine (VM)](media/just-in-time-explained/jit-logic-flow.png)](media/just-in-time-explained/jit-logic-flow.png#lightbox)

Wanneer Security Center een computer vindt die kan profiteren van JIT, wordt deze computer toegevoegd aan het tabblad **slechte resources** van de aanbeveling. 

![Aanbeveling voor toegang tot virtuele machines met Just-in-time (JIT)](./media/just-in-time-explained/unhealthy-resources.png)


## <a name="faq---questions-about-just-in-time-virtual-machine-access"></a>Veelgestelde vragen: vragen over just-in-time-toegang tot virtuele machines

### <a name="what-permissions-are-needed-to-configure-and-use-jit"></a>Welke machtigingen zijn er nodig om JIT te configureren en gebruiken?

Als u aangepaste rollen wilt maken die met JIT kunnen werken, hebt u de volgende gegevens nodig:

| Een gebruiker in staat stellen: | Machtigingen om in te stellen|
| --- | --- |
| Een JIT-beleid voor een virtuele machine configureren of bewerken | *Wijs deze acties toe aan de rol:*  <ul><li>Binnen het bereik van een abonnement of resource groep die is gekoppeld aan de virtuele machine:<br/> `Microsoft.Security/locations/jitNetworkAccessPolicies/write` </li><li> Binnen het bereik van een abonnement of resource groep van de VM: <br/>`Microsoft.Compute/virtualMachines/write`</li></ul> | 
|JIT-toegang aanvragen voor een virtuele machine | *Deze acties toewijzen aan de gebruiker:*  <ul><li>Binnen het bereik van een abonnement of resource groep die is gekoppeld aan de virtuele machine:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action` </li><li>Binnen het bereik van een abonnement of resource groep die is gekoppeld aan de virtuele machine:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/*/read` </li><li>  Binnen het bereik van een abonnement of resource groep of VM:<br/> `Microsoft.Compute/virtualMachines/read` </li><li>  Binnen het bereik van een abonnement of resource groep of VM:<br/> `Microsoft.Network/networkInterfaces/*/read` </li></ul>|
|JIT-beleid lezen| *Deze acties toewijzen aan de gebruiker:*  <ul><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/read`</li><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action`</li><li>`Microsoft.Security/policies/read`</li><li>`Microsoft.Compute/virtualMachines/read`</li><li>`Microsoft.Network/*/read`</li>|





## <a name="next-steps"></a>Volgende stappen

Op deze pagina wordt uitgelegd _Waarom_ de toegang tot virtuele machines (just-in-time) (JIT) van de VM moet worden gebruikt. 

Ga naar het artikel over procedures voor meer informatie over het inschakelen van JIT en het aanvragen van toegang tot uw met JIT ingeschakelde Vm's:

> [!div class="nextstepaction"]
> [Uw beheer poorten beveiligen met JIT](security-center-just-in-time.md)
