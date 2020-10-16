---
title: bestand opnemen
description: bestand opnemen
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 10/12/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 20d80fe1a09d388552b6289f8a9b23c878672f94
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91974279"
---
Deze voorbeelden laten zien hoe u Azure Policy gebruikt met abonnementen die zijn toegevoegd aan Azure Lighthouse.

| **Sjabloon** | **Beschrijving** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag) | Hiermee wijst u een beleid toe waarmee een tag (met behulp van het wijzigingseffect) wordt toegevoegd aan of verwijderd uit een gedelegeerd abonnement. Zie [Een beleid implementeren dat kan worden hersteld binnen een gedelegeerd abonnement](../articles/lighthouse/how-to/deploy-policy-remediation.md) voor meer informatie. |
| [policy-allow-certain-managing-tenants](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-allow-certain-managing-tenants) | Hiermee wijst u een beleid toe dat Azure Lighthouse-delegaties beperkt tot het beheer van specifieke tenants. |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-audit-delegation) | Hiermee wordt een beleid toegewezen waarmee delegatietoewijzingen worden gecontroleerd. |
| [policy-delegate-management-groups](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-delegate-management-groups) | Hiermee wordt beleid toegewezen om te bevestigen dat abonnementen binnen een beheergroep zijn gedelegeerd aan een beheertenant. Indien dat niet zo is, wordt de toewijzing gemaakt.
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring) | Hiermee wijst u een beleid toe dat diagnostische gegevens inschakelt op Azure Key Vault-resources in een gedelegeerd abonnement (met behulp van het deployIfNotExists-effect). Zie [Een beleid implementeren dat kan worden hersteld binnen een gedelegeerd abonnement](../articles/lighthouse/how-to/deploy-policy-remediation.md) voor meer informatie. |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-sub-monitoring) | Hiermee wijst u diverse beleidsregels toe voor het inschakelen van diagnostische gegevens op een gedelegeerd abonnement en verbindt u alle Windows- en Linux-VM's met de Log Analytics-werkruimte die door het beleid is gemaakt. Zie [Een beleid implementeren dat kan worden hersteld binnen een gedelegeerd abonnement](../articles/lighthouse/how-to/deploy-policy-remediation.md) voor meer informatie. |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-initiative) | Hiermee wordt een initiatief (meerdere gerelateerde beleidsdefinities) toegepast op een gedelegeerd abonnement. |

