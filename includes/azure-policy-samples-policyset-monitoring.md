---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/12/2020
ms.author: dacoulte
ms.openlocfilehash: f06a78039217d7bc6b03c9196ac6824477ea977e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79382098"
---
|Name |Beschrijving |Beleidsregels |Versie |
|---|---|---|---|
|[Azure-monitor inschakelen voor virtuele machineschaalsets](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Azure Monitor inschakelen voor de virtuele machineschaalsets in het opgegeven bereik (beheergroep, abonnement of resourcegroep). Neemt de werkruimte Log Analytics als parameter. Opmerking: als uw upgradebeleid voor schaalset is ingesteld op Handmatig, moet u de extensie toepassen op alle VM's in de set door er een upgrade op aan te roepen. In CLI zou dit az vmss update-instances. |6 |1.0.1 |
|[Azure-monitor voor VM's inschakelen](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Azure Monitor voor de Virtuele Machines (VM's) inschakelen in het opgegeven bereik (beheergroep, abonnement of resourcegroep). Neemt de werkruimte Log Analytics als parameter. |6 |1.0.1 |
