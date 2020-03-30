---
title: Azure-beveiligingsbeheer - Voorraad- en assetbeheer
description: Voorraad beheer van beveiligingsbeheer en beheer van activa
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 2f24012a69a7673426644a8ded6162cd1021938e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75930066"
---
# <a name="security-control-inventory-and-asset-management"></a>Beveiligingsbeheer: voorraad- en vermogensbeheer

Aanbevelingen voor voorraad- en vermogensbeheer zijn gericht op het aanpakken van problemen die verband houden met het actief beheren (inventariseren, bijhouden en corrigeren) van alle Azure-resources, zodat alleen geautoriseerde resources toegang krijgen en ongeautoriseerde en onbeheerde resources toegang krijgen geïdentificeerd en verwijderd.

## <a name="61-use-azure-asset-discovery"></a>6.1: Azure Asset Discovery gebruiken

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 6.1 | 1.1, 1.2, 1.3, 1.4, 9.1, 12.1 | Klant |

Gebruik Azure Resource Graph om alle bronnen (zoals compute, storage, netwerk, poorten en protocollen, enz.) in uw abonnement(en) op te vragen/te ontdekken.  Zorg voor de juiste (lees)machtigingen in uw tenant en opsommen van alle Azure-abonnementen en resources binnen uw abonnementen.

Hoewel klassieke Azure-bronnen kunnen worden ontdekt via Resource Graph, is het ten zeerste aan te raden om azure resource manager-resources in de toekomst te maken en te gebruiken.

Query's maken met Azure Resource Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Uw Azure-abonnementen weergeven:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Azure RBAC begrijpen:

https://docs.microsoft.com/azure/role-based-access-control/overview

## <a name="62-maintain-asset-metadata"></a>6.2: Metagegevens van activa onderhouden

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 6.2 | 1.5 | Klant |

Tags toepassen op Azure-resources met metagegevens om ze logisch te ordenen in een taxonomie.

Tags maken en gebruiken:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="63-delete-unauthorized-azure-resources"></a>6.3: Ongeautoriseerde Azure-bronnen verwijderen

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 6.3 | 1.6 | Klant |

Gebruik tagging, beheergroepen en afzonderlijke abonnementen, waar nodig, om assets te ordenen en bij te houden. Verzoen de voorraad regelmatig en zorg ervoor dat ongeautoriseerde bronnen tijdig uit het abonnement worden verwijderd.

Extra Azure-abonnementen maken:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Managementgroepen maken:

https://docs.microsoft.com/azure/governance/management-groups/create

Tags maken en gebruiken:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Een inventaris bijhouden van goedgekeurde Azure-resources en softwaretitels

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 6.4 | 2.1 | Klant |

Definieer goedgekeurde Azure-resources en goedgekeurde software voor rekenbronnen.

## <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor voor niet-goedgekeurde Azure-bronnen

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 6.5 | 2.3, 2.4 | Klant |

Gebruik Azure Policy om beperkingen op te leggen aan het type resources dat in uw abonnement(en) kan worden gemaakt.

Gebruik Azure Resource Graph om bronnen binnen hun abonnement(en) te zoeken/ontdekken. &nbsp;Controleer of alle Azure-resources die in de omgeving aanwezig zijn, zijn goedgekeurd.

Azure-beleid configureren en beheren:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Query's maken met Azure Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

## <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor voor niet-goedgekeurde softwaretoepassingen binnen compute resources

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 6.6 | 2.3/2.4 | Klant |

Gebruik Azure virtual machine Inventory om het verzamelen van informatie over alle software op virtuele machines te automatiseren. Softwarenaam, Versie, Uitgever en Vernieuwingstijd zijn beschikbaar via de Azure-portal. Als u toegang wilt krijgen tot de installatiedatum en andere informatie, schakelt u diagnostische gegevens op gastniveau in en brengt u de Windows-gebeurtenislogboeken naar een Logboekanalysewerkruimte.

Azure-voorraad voor virtuele machines inschakelen:

https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software

## <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Niet-goedgekeurde Azure-bronnen en -softwaretoepassingen verwijderen

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 6.7 | 2,5 | Klant |

Gebruik de file integrity monitoring (Change Tracking) van Azure Security Center en de inventaris van virtuele machines om alle software te identificeren die op virtuele machines is geïnstalleerd. U uw eigen proces implementeren voor het verwijderen van ongeautoriseerde software. U ook een oplossing van derden gebruiken om niet-goedgekeurde software te identificeren.

Bestandsintegriteitscontrole gebruiken:

https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring#using-file-integrity-monitoring

Inzicht in Azure Change Tracking:

https://docs.microsoft.com/azure/automation/change-tracking

Azure-voorraad voor virtuele machines inschakelen:

https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software

## <a name="68-use-only-approved-applications"></a>6.8: Gebruik alleen goedgekeurde aanvragen

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 6.8 | 2,6 | Klant |

Gebruik Azure Security Center Adaptive Application Controls om ervoor te zorgen dat alleen geautoriseerde software wordt uitgevoerd en dat alle ongeautoriseerde software wordt geblokkeerd voor het uitvoeren op Azure Virtual Machines.

Azure Security Center Adaptive Application Controls gebruiken:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-application

## <a name="69-use-only-approved-azure-services"></a>6.9: Alleen goedgekeurde Azure-services gebruiken

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 6.9 | 2,6 | Klant |

Gebruik Azure Policy om te beperken welke services u in uw omgeving inrichten.

Azure-beleid configureren en beheren:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Een specifiek brontype weigeren met Azure-beleid:

https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

## <a name="610-implement-approved-application-list"></a>6.10: Goedgekeurde aanvraaglijst implementeren

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 6.1 | 2.7 | Klant |

Gebruik Azure Security Center Adaptive Application Controls om op te geven op welke bestandstypen een regel al dan niet van toepassing is.

Implementeer een oplossing van derden als dit niet aan de eis voldoet.

Azure Security Center Adaptive Application Controls gebruiken:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-application

## <a name="611-limit-users-ability-to-interact-with-azure-resource-manager-via-scripts"></a>6.11: De mogelijkheid van gebruikers beperken om via scripts met Azure Resource Manager te communiceren

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 6.11 | 2,8 | Klant |

Gebruik Voorwaardelijke toegang voor Azure om de interactie van &quot;gebruikers&quot; met &quot;Azure Resource&quot; Manager te beperken door Bloktoegang voor de Microsoft Azure Management App te configureren.

Voorwaardelijke toegang configureren om toegang tot Azure Resource Manager te blokkeren:

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

## <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Beperk de mogelijkheid van gebruikers om scripts uit te voeren binnen compute resources

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 6.12 | 2,8 | Klant |

Gebruik beveiligingssysteemspecifieke configuraties of bronnen van derden om de mogelijkheid van gebruikers om scripts uit te voeren binnen Azure-compute resources te beperken.

Bijvoorbeeld hoe u powershell-scriptuitvoering in Windows-omgevingen beheert:

https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6

## <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Toepassingen met een hoog risico fysiek of logisch scheiden

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 6.13 | 2.9 | Klant |

Software die vereist is voor bedrijfsactiviteiten, maar een hoger risico voor de organisatie kan opleveren, moet worden geïsoleerd binnen zijn eigen virtuele machine en/of virtueel netwerk en voldoende zijn beveiligd met een Azure Firewall- of Netwerkbeveiligingsgroep.

Een virtueel netwerk maken:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Een NSG maken met een beveiligingsconfig:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

## <a name="next-steps"></a>Volgende stappen

Het volgende beveiligingsbesturingselement bekijken: [Beveiligde configuratie](security-control-secure-configuration.md)
