---
title: Azure-beveiligings beheer-inventarisatie en Asset Management
description: Beveiliging van de inventaris en het beheer van bedrijfs middelen
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 2f24012a69a7673426644a8ded6162cd1021938e
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930066"
---
# <a name="security-control-inventory-and-asset-management"></a>Beveiligings beheer: inventarisatie en activa beheer

Aanbevelingen voor inventarisatie en Asset Management richten zich op het adresseren van problemen met betrekking tot het actief beheren (inventariseren, bijhouden en corrigeren) van alle Azure-resources, zodat alleen geautoriseerde resources toegang krijgen en niet-geautoriseerde en onbeheerde bronnen geïdentificeerd en verwijderd.

## <a name="61-use-azure-asset-discovery"></a>6,1: Azure Asset Discovery gebruiken

| Azure-ID | CIS-Id's | Verantwoordelijkheid |
|--|--|--|
| 6.1 | 1,1, 1,2, 1,3, 1,4, 9,1, 12,1 | Klant |

Gebruik Azure resource Graph voor het opvragen/detecteren van alle resources (zoals compute, opslag, netwerk, poorten en protocollen enz.) binnen uw abonnement (en).  Zorg ervoor dat de juiste (Lees-) machtigingen voor uw Tenant en alle Azure-abonnementen en resources in uw abonnementen inventariseren.

Hoewel klassieke Azure-resources kunnen worden gedetecteerd via resource grafiek, is het raadzaam om Azure Resource Manager resources te maken en te gebruiken.

Query's maken met Azure resource Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Uw Azure-abonnementen weer geven:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Meer informatie over Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

## <a name="62-maintain-asset-metadata"></a>6,2: meta gegevens van activa onderhouden

| Azure-ID | CIS-Id's | Verantwoordelijkheid |
|--|--|--|
| 6.2 | 1.5 | Klant |

Pas Tags toe op Azure-resources die meta gegevens geven om ze logisch in een taxonomie te organiseren.

Tags maken en gebruiken:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="63-delete-unauthorized-azure-resources"></a>6,3: niet-geautoriseerde Azure-resources verwijderen

| Azure-ID | CIS-Id's | Verantwoordelijkheid |
|--|--|--|
| 6.3 | 1.6 | Klant |

Gebruik Tags, beheer groepen en afzonderlijke abonnementen, waar nodig, om assets te organiseren en bij te houden. Sluit de inventaris regel matig af en zorg ervoor dat niet-geautoriseerde resources tijdig worden verwijderd uit het abonnement.

Aanvullende Azure-abonnementen maken:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Beheergroepen maken:

https://docs.microsoft.com/azure/governance/management-groups/create

Tags maken en gebruiken:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: een inventaris van goedgekeurde Azure-resources en software titels onderhouden

| Azure-ID | CIS-Id's | Verantwoordelijkheid |
|--|--|--|
| 6.4 | 2.1 | Klant |

Definieer goedgekeurde Azure-resources en goedgekeurde software voor reken resources.

## <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

| Azure-ID | CIS-Id's | Verantwoordelijkheid |
|--|--|--|
| 6.5 | 2,3, 2,4 | Klant |

Gebruik Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in uw abonnement (en).

Gebruik Azure resource Graph voor het opvragen/detecteren van resources binnen hun abonnement (en). &nbsp;ervoor te zorgen dat alle Azure-resources die in de omgeving aanwezig zijn, zijn goedgekeurd.

Azure Policy configureren en beheren:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Query's maken met Azure Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

## <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitor voor niet-goedgekeurde software toepassingen binnen reken resources

| Azure-ID | CIS-Id's | Verantwoordelijkheid |
|--|--|--|
| 6.6 | 2.3/2.4 | Klant |

Gebruik Azure virtual machine Inventory om het verzamelen van informatie over alle software op Virtual Machines te automatiseren. De software naam, versie, uitgever en tijd van vernieuwen zijn beschikbaar via de Azure Portal. Als u toegang wilt krijgen tot installatie datum en andere informatie, schakelt u Diagnostische gegevens op gast niveau in en brengt u de Windows-gebeurtenis Logboeken in een Log Analytics-werk ruimte.

Inventarisatie van Azure virtual machine inschakelen:

https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software

## <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: niet-goedgekeurde Azure-resources en software toepassingen verwijderen

| Azure-ID | CIS-Id's | Verantwoordelijkheid |
|--|--|--|
| 6.7 | 2,5 | Klant |

Gebruik Azure Security Center File Integrity Monitoring (Wijzigingen bijhouden) en virtual machine Inventory om alle software te identificeren die op Virtual Machines is geïnstalleerd. U kunt uw eigen proces voor het verwijderen van onbevoegde software implementeren. U kunt ook een oplossing van derden gebruiken om niet-goedgekeurde software te identificeren.

File Integrity Monitoring gebruiken:

https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring#using-file-integrity-monitoring

Meer informatie over Azure Wijzigingen bijhouden:

https://docs.microsoft.com/azure/automation/change-tracking

Inventarisatie van Azure virtual machine inschakelen:

https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software

## <a name="68-use-only-approved-applications"></a>6,8: alleen goedgekeurde toepassingen gebruiken

| Azure-ID | CIS-Id's | Verantwoordelijkheid |
|--|--|--|
| 6.8 | 2,6 | Klant |

Gebruik Azure Security Center adaptieve toepassings controles om ervoor te zorgen dat alleen geautoriseerde software wordt uitgevoerd en alle niet-geautoriseerde software wordt geblokkeerd voor uitvoering op Azure Virtual Machines.

Azure Security Center adaptieve toepassings besturings elementen gebruiken:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-application

## <a name="69-use-only-approved-azure-services"></a>6,9: alleen goedgekeurde Azure-Services gebruiken

| Azure-ID | CIS-Id's | Verantwoordelijkheid |
|--|--|--|
| 6.9 | 2,6 | Klant |

Gebruik Azure Policy om te beperken welke services u in uw omgeving kunt inrichten.

Azure Policy configureren en beheren:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Een specifiek resource type weigeren met Azure Policy:

https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

## <a name="610-implement-approved-application-list"></a>6,10: lijst met goedgekeurde toepassingen implementeren

| Azure-ID | CIS-Id's | Verantwoordelijkheid |
|--|--|--|
| 6.1 | 2.7 | Klant |

Gebruik Azure Security Center adaptieve toepassings besturings elementen om op te geven welke bestands typen een regel kan of niet van toepassing is op.

Implementeer een oplossing van derden als deze niet aan de vereiste voldoet.

Azure Security Center adaptieve toepassings besturings elementen gebruiken:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-application

## <a name="611-limit-users-ability-to-interact-with-azure-resource-manager-via-scripts"></a>6,11: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager via scripts beperken

| Azure-ID | CIS-Id's | Verantwoordelijkheid |
|--|--|--|
| 6,11 | 2.8 | Klant |

Gebruik voorwaardelijke toegang van Azure om gebruikers de mogelijkheid te bieden om te communiceren met Azure Resource Manager door het configureren van &quot;&quot; voor blok keren van toegang tot de Microsoft Azure-app voor &quot;&quot; beheer.

Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager te blok keren:

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

## <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: de mogelijkheid van gebruikers om scripts uit te voeren binnen reken bronnen beperken

| Azure-ID | CIS-Id's | Verantwoordelijkheid |
|--|--|--|
| 6,12 | 2.8 | Klant |

Gebruik specifieke configuraties van besturings systemen of bronnen van derden om de mogelijkheid van gebruikers om scripts uit te voeren binnen Azure Compute-resources te beperken.

Bijvoorbeeld hoe u de uitvoering van Power shell-scripts kunt beheren in Windows-omgevingen:

https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6

## <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: toepassingen met een hoog risico fysiek of logisch scheiden

| Azure-ID | CIS-Id's | Verantwoordelijkheid |
|--|--|--|
| 6,13 | 2.9 | Klant |

Software die vereist is voor bedrijfs activiteiten, maar die een groter risico kan opleveren voor de organisatie, moet worden geïsoleerd in een eigen virtuele machine en/of virtueel netwerk en voldoende beveiligd zijn met een Azure Firewall of een netwerk beveiligings groep.

Een virtueel netwerk maken:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Een NSG maken met een beveiligings configuratie:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

## <a name="next-steps"></a>Volgende stappen

Zie het volgende beveiligings beheer: [beveiligde configuratie](security-control-secure-configuration.md)
