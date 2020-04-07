---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/03/2020
ms.author: dacoulte
ms.openlocfilehash: 074d28f8144245247944f9c3409507d331c81166
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758809"
---
|Name |Beschrijving |Beleidsregels |Versie |
|---|---|---|---|
|[Azure-monitor inschakelen voor virtuele machineschaalsets](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Azure Monitor inschakelen voor de virtuele machineschaalsets in het opgegeven bereik (beheergroep, abonnement of resourcegroep). Neemt de werkruimte Log Analytics als parameter. Opmerking: als uw upgradebeleid voor schaalset is ingesteld op Handmatig, moet u de extensie toepassen op alle VM's in de set door er een upgrade op aan te roepen. In CLI zou dit az vmss update-instances. |6 |1.0.1 |
|[Azure-monitor voor VM's inschakelen](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Azure Monitor voor de Virtuele Machines (VM's) inschakelen in het opgegeven bereik (beheergroep, abonnement of resourcegroep). Neemt de werkruimte Log Analytics als parameter. |6 |1.0.1 |
