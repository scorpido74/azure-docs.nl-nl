---
title: Voorbeelden en sjablonen van Azure Lighthouse
description: In deze voorbeelden en Azure Resource Manager-sjablonen ziet u hoe u klanten kunt onboarden voor in Azure gedelegeerd resourcebeheer en ondersteuning voor Azure Lighthouse-scenario’s.
ms.topic: sample
ms.date: 10/17/2019
ms.openlocfilehash: 20c1fc62c83202f97ed9cff68fd285280d94a321
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132164"
---
# <a name="azure-lighthouse-samples"></a>Azure Lighthouse-voorbeelden

De volgende tabel bevat koppelingen naar belangrijke Azure Resource Manager-sjablonen voor Azure Lighthouse. U vindt deze bestanden en meer ook in de [opslagplaats met Azure Lighthouse-voorbeelden](https://github.com/Azure/Azure-Lighthouse-samples/).

## <a name="onboarding-customers-to-azure-delegated-resource-management"></a>Klanten in gedelegeerd Azure-resourcebeheer vrijgeven

We bieden verschillende sjablonen voor het oplossen van specifieke onboardingscenario's. Kies de optie die het beste werkt en zorg ervoor dat u het parameterbestand wijzigt, zodat dit overeenkomt met uw omgeving. Zie [Een klant in gedelegeerd Azure-resourcebeheer vrijgeven](../how-to/onboard-customer.md) voor meer informatie over het gebruik van deze bestanden in uw implementatie.

| **Sjabloon** | **Beschrijving** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management) | Plaatst het abonnement van een klant in gedelegeerd Azure-resourcebeheer. Voor elk abonnement moet een afzonderlijke implementatie worden uitgevoerd. |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) | Plaatst een of meer resourcegroepen van een klant in gedelegeerd Azure-resourcebeheer. Gebruik **rgDelegatedResourceManagement** voor één resourcegroep of **multipleRgDelegatedResourceManagement**  om meerdere resourcegroepen in hetzelfde abonnement vrij te geven. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management) | Als u [een aanbieding voor beheerde services hebt gepubliceerd naar Azure Marketplace](../how-to/publish-managed-services-offers.md), kunt u deze sjabloon eventueel gebruiken om resources vrij te geven voor klanten die de aanbieding hebben geaccepteerd. De Marketplace-waarden in het parameterbestand moeten overeenkomen met de waarden die u hebt gebruikt bij het publiceren van uw aanbieding. |

Normaal gesproken is een afzonderlijke implementatie vereist voor elk abonnement dat wordt vrijgegeven, maar u kunt ook sjablonen voor meerdere abonnementen implementeren.

| **Sjabloon** | **Beschrijving** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-subscription-deployment) | Implementeert Azure Resource Manager-sjablonen in meerdere abonnementen. |

## <a name="azure-policy"></a>Azure Policy

Deze voorbeelden laten zien hoe u Azure Policy kunt gebruiken met abonnementen waarvoor het beheer van gedelegeerde Azure-resources is vrijgegeven.

| **Sjabloon** | **Beschrijving** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-add-or-replace-tag) | Hiermee wijst u een beleid toe waarmee een tag (met behulp van het wijzigingseffect) wordt toegevoegd aan of verwijderd uit een gedelegeerd abonnement. Zie [Een beleid implementeren dat kan worden hersteld binnen een gedelegeerd abonnement](../how-to/deploy-policy-remediation.md) voor meer informatie. |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-audit-delegation) | Hiermee wordt een beleid toegewezen waarmee delegatietoewijzingen worden gecontroleerd. |
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-keyvault-monitoring) | Hiermee wijst u een beleid toe dat diagnostische gegevens inschakelt op Azure Key Vault-resources in een gedelegeerd abonnement (met behulp van het deployIfNotExists-effect). Zie [Een beleid implementeren dat kan worden hersteld binnen een gedelegeerd abonnement](../how-to/deploy-policy-remediation.md) voor meer informatie. |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-sub-monitoring) | Hiermee wijst u diverse beleidsregels toe voor het inschakelen van diagnostische gegevens op een gedelegeerd abonnement en verbindt u alle Windows- en Linux-VM's met de Log Analytics-werkruimte die door het beleid is gemaakt. Zie [Een beleid implementeren dat kan worden hersteld binnen een gedelegeerd abonnement](../how-to/deploy-policy-remediation.md) voor meer informatie. |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-initiative) | Hiermee wordt een initiatief (meerdere gerelateerde beleidsdefinities) toegepast op een gedelegeerd abonnement. |

## <a name="azure-monitor"></a>Azure Monitor

Deze voorbeelden laten zien hoe u Azure Monitor kunt gebruiken om waarschuwingen te maken voor abonnementen waarvoor het beheer van gedelegeerde Azure-resources is vrijgegeven.

| **Sjabloon** | **Beschrijving** |
|---------|---------|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/alert-using-actiongroup) | Met deze sjabloon maakt u een Azure-waarschuwing en maakt u verbinding met een bestaande actiegroep.|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/multiple-loganalytics-alerts) | Hiermee maakt u meerdere waarschuwingen voor een logboek op basis van Kusto-query's.|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegation-alert-for-customer) | Hiermee wordt een waarschuwing in een tenant geïmplementeerd wanneer een gebruiker een abonnement naar een beheertenant delegeert.|

## <a name="additional-cross-tenant-scenarios"></a>Aanvullende scenario's voor meerdere tenants

Deze voorbeelden illustreren verschillende taken die kunnen worden uitgevoerd in scenario's voor het beheer van meerdere tenants.

| **Sjabloon** | **Beschrijving** |
|---------|---------|
| [cross-rg-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-rg-deployment) | Hiermee worden opslagaccounts geïmplementeerd in twee verschillende resourcegroepen.|
| [deploy-azure-mgmt-services](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-mgmt-services) | Maakt Azure-beheerservices, koppelt deze en implementeert aanvullende oplossingen. Gebruik de sjabloon **rgWithAzureMgmt.json** voor een end-to-end-implementatie. |
| [deploy-azure-security-center](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-security-center) | Configureert en schakelt Azure Security Center in binnen het Azure-doelabonnement. |
| [deploy-azure-sentinel](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-sentinel) | Hiermee wordt Azure Sentinel geïmplementeerd en ingeschakeld op een bestaande Log Analytics-werkruimte in een gedelegeerd abonnement. |
| [deploy-log-analytics-vm-extensions](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-log-analytics-vm-extensions) | Met deze sjablonen kunt u Log Analytics VM-extensies implementeren op uw Windows- en Linux-VM's en deze verbinden met de toegewezen Log Analytics-werkruimte |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [gedelegeerd Azure-resourcebeheer](../concepts/azure-delegated-resource-management.md).
- Verken de [Opslagplaats met Azure Lighthouse-voorbeelden](https://github.com/Azure/Azure-Lighthouse-samples/).
