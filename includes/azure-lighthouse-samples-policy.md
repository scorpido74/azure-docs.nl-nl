---
title: bestand opnemen
description: bestand opnemen
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 12/19/2019
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 55eee87ed1e07b61a7f8287e86ddc0a7a6aa4df9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456747"
---
Deze voorbeelden laten zien hoe u Azure Policy kunt gebruiken met abonnementen waarvoor het beheer van gedelegeerde Azure-resources is vrijgegeven.

| **Sjabloon** | **Beschrijving** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-add-or-replace-tag) | Hiermee wijst u een beleid toe waarmee een tag (met behulp van het wijzigingseffect) wordt toegevoegd aan of verwijderd uit een gedelegeerd abonnement. Zie [Een beleid implementeren dat kan worden hersteld binnen een gedelegeerd abonnement](../articles/lighthouse/how-to/deploy-policy-remediation.md) voor meer informatie. |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-audit-delegation) | Hiermee wordt een beleid toegewezen waarmee delegatietoewijzingen worden gecontroleerd. |
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-keyvault-monitoring) | Hiermee wijst u een beleid toe dat diagnostische gegevens inschakelt op Azure Key Vault-resources in een gedelegeerd abonnement (met behulp van het deployIfNotExists-effect). Zie [Een beleid implementeren dat kan worden hersteld binnen een gedelegeerd abonnement](../articles/lighthouse/how-to/deploy-policy-remediation.md) voor meer informatie. |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-sub-monitoring) | Hiermee wijst u diverse beleidsregels toe voor het inschakelen van diagnostische gegevens op een gedelegeerd abonnement en verbindt u alle Windows- en Linux-VM's met de Log Analytics-werkruimte die door het beleid is gemaakt. Zie [Een beleid implementeren dat kan worden hersteld binnen een gedelegeerd abonnement](../articles/lighthouse/how-to/deploy-policy-remediation.md) voor meer informatie. |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-initiative) | Hiermee wordt een initiatief (meerdere gerelateerde beleidsdefinities) toegepast op een gedelegeerd abonnement. |

