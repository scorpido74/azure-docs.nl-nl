---
title: Azure Security Bench Mark v2-Asset Management
description: Asset Management van Azure Security Bench Mark v2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 845c4f3194f35c68306e74734e5ecc6109d9c103
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318021"
---
# <a name="security-control-v2-asset-management"></a>Beveiligings controle v2: beheer van middelen

Asset Management bestrijkt de controles om de zicht baarheid van de beveiliging en het beheer van Azure-resources te garanderen. Dit omvat aanbevelingen voor machtigingen voor beveiligings personeel, beveiliging van de inventaris en het beheren van goed keuringen voor services en resources (inventaris, bijhouden en corrigeren).

## <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: controleren of het beveiligings team inzicht heeft in Risico's voor assets

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP800-53 R4-ID ('s) |
|--|--|--|--|
| AM-1 | 1,1, 1,2 | CM-8, PM-5 |

Zorg ervoor dat aan beveiligings teams machtigingen voor beveiligings lezers worden verleend in uw Azure-Tenant en-abonnementen zodat ze kunnen controleren op beveiligings Risico's met behulp van Azure Security Center. 

Afhankelijk van de manier waarop de verantwoordelijkheden van het beveiligings team zijn gestructureerd, kan bewaking voor beveiligings Risico's de verantwoordelijkheid zijn van een centraal beveiligings team of een lokaal team. Dat wil zeggen dat beveiligings inzichten en-risico's altijd centraal moeten worden geaggregeerd in een organisatie. 

Machtigingen voor beveiligings lezers kunnen breed worden toegepast op een hele Tenant (hoofd beheer groep) of in het bereik van beheer groepen of specifieke abonnementen. 

Opmerking: er zijn mogelijk extra machtigingen vereist om inzicht te krijgen in werk belastingen en services. 

- [Overzicht van de rol van beveiligings lezer](../../role-based-access-control/built-in-roles.md#security-reader)

- [Overzicht van Azure Beheergroepen](../../governance/management-groups/overview.md)

**Verantwoordelijkheid**: klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastructuur- en eindpuntbeveiliging](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Beheer van beveiligings naleving](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: controleren of het beveiligings team toegang heeft tot de inventaris en meta gegevens van de Asset

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP800-53 R4-ID ('s) |
|--|--|--|--|
| AM-2 | 1,1, 1,2, 1,4, 1,5, 9,1, 12,1 | CM-8, PM-5 |

Zorg ervoor dat beveiligings teams toegang hebben tot een voortdurend bijgewerkte inventaris van assets in Azure. Beveiligings teams hebben deze inventaris vaak nodig om de potentiële bloot stelling van hun organisatie aan opkomende Risico's te evalueren, en als een invoer voor voortdurend verbeterde beveiligings verbeteringen. 

Met de functie voor Azure Security Center Inventory en Azure resource Graph kunt u alle resources in uw abonnementen opvragen en detecteren, inclusief Azure-Services,-toepassingen en-netwerk bronnen.  

Organiseer op logische wijze activa op basis van de taxonomie van uw organisatie met behulp van tags en andere meta gegevens in azure (naam, beschrijving en categorie).  

- [Query's maken met Azure resource Graph Explorer](../../governance/resource-graph/first-query-portal.md)

- [Azure Security Center Asset Inventory Management](../../security-center/asset-inventory.md)

- [Voor meer informatie over het labelen van assets raadpleegt u de hand leiding resource naamgeving en Tags toevoegen](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Verantwoordelijkheid**: klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastructuur- en eindpuntbeveiliging](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Beheer van beveiligings naleving](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-3-use-only-approved-azure-services"></a>AM-3: alleen goedgekeurde Azure-Services gebruiken

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP800-53 R4-ID ('s) |
|--|--|--|--|
| AM-3 | 2,3, 2,4 | CM-7, CM-8 |

Gebruik Azure Policy om te controleren welke Services gebruikers in uw omgeving kunnen inrichten en beperken. Gebruik Azure resource Graph om bronnen in hun abonnementen op te vragen en te detecteren.  U kunt ook Azure Monitor gebruiken om regels te maken voor het activeren van waarschuwingen wanneer een niet-goedgekeurde service wordt gedetecteerd.

- [Azure Policy configureren en beheren](../../governance/policy/tutorials/create-and-manage.md)

- [Een specifiek resource type weigeren met Azure Policy](/azure/governance/policy/samples/not-allowed-resource-types)

- [Query's maken met Azure resource Graph Explorer](../../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Beheer van beveiligings naleving](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Postuurbeheer](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

## <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: zorg voor de beveiliging van levenscyclus beheer van middelen

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP800-53 R4-ID ('s) |
|--|--|--|--|
| AM-4 | 2,3, 2,4, 2,5 | CM-7, CM-8, CM-10, CM-11 |

Een beveiligings beleid voor het beheer van de levens duur van asset-processen instellen of bijwerken voor mogelijk grote veranderingen. Deze wijzigingen omvatten wijzigingen in: id-providers en toegang, gegevens gevoeligheid, netwerk configuratie en toewijzing van beheerders bevoegdheden.

Verwijder Azure-resources wanneer ze niet meer nodig zijn.

- [Azure-resource groep en-resource verwijderen](../../azure-resource-manager/management/delete-resource-group.md)

**Verantwoordelijkheid**: klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastructuur- en eindpuntbeveiliging](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Postuurbeheer](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

- [Beheer van beveiligings naleving](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP800-53 R4-ID ('s) |
|--|--|--|--|
| AM-5 | 2.9 | AC-3 |

Gebruik voorwaardelijke toegang van Azure AD om de interactie van gebruikers met Azure Resource Manager te beperken door ' blok toegang ' te configureren voor de app Microsoft Azure management.

- [Voorwaardelijke toegang configureren om de toegang tot Azure-bronnen beheer te blok keren](../../role-based-access-control/conditional-access-azure-management.md)

**Verantwoordelijkheid**: klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Postuurbeheer](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

- [Infrastructuur- en eindpuntbeveiliging](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="am-6-use-only-approved-applications-in-compute-resources"></a>AM-6: alleen goedgekeurde toepassingen in reken bronnen gebruiken

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP800-53 R4-ID ('s) |
|--|--|--|--|
| AM-6 | 2,6, 2,7 | AC-3, CM-7, CM-8, CM-10, CM-11 |

Zorg ervoor dat alleen geautoriseerde software wordt uitgevoerd en dat alle niet-geautoriseerde software wordt geblokkeerd voor het uitvoeren van Azure Virtual Machines.

Gebruik Azure Security Center (ASC) adaptieve toepassings besturings elementen voor het detecteren en genereren van een lijst met toegestane toepassingen. U kunt ook ASC-besturings elementen voor adaptieve toepassingen gebruiken om ervoor te zorgen dat alleen geautoriseerde software wordt uitgevoerd en alle niet-geautoriseerde software wordt geblokkeerd voor het uitvoeren van Azure Virtual Machines.

Gebruik Azure Automation Wijzigingen bijhouden en inventaris om het verzamelen van inventaris gegevens van uw Windows-en Linux-Vm's te automatiseren. De software naam, versie, uitgever en tijd van vernieuwen zijn beschikbaar via de Azure Portal. Als u de software-installatie datum en andere informatie wilt ophalen, schakelt u Diagnostische gegevens op gast niveau in en stuurt u de Windows-gebeurtenis logboeken naar Log Analytics werk ruimte.

Afhankelijk van het type scripts kunt u specifieke configuraties van het besturings systeem of bronnen van derden gebruiken om de mogelijkheid van gebruikers om scripts uit te voeren in azure Compute-resources te beperken. 

U kunt ook een oplossing van derden gebruiken om niet-goedgekeurde software te detecteren en te identificeren.

- [Azure Security Center adaptieve toepassings besturings elementen gebruiken](../../security-center/security-center-adaptive-application.md)

- [Meer informatie over Azure Automation Wijzigingen bijhouden en inventaris](../../automation/change-tracking.md)

- [De uitvoering van Power shell-scripts beheren in Windows-omgevingen](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

**Verantwoordelijkheid**: klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastructuur- en eindpuntbeveiliging](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Postuurbeheer](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

- [Beheer van beveiligings naleving](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

