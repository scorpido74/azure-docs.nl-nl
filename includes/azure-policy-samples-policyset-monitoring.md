---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/26/2020
ms.author: dacoulte
ms.openlocfilehash: 1bc83a4c93d4fbd252a99cb801e7484a8e15a689
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77780672"
---
|Name |Beschrijving |Beleidsregels |Version |
|---|---|---|---|
|[Azure Monitor voor Virtual Machine Scale Sets inschakelen](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Azure Monitor in te scha kelen voor de Virtual Machine Scale Sets in het opgegeven bereik (beheer groep, abonnement of resource groep). Hiermee wordt Log Analytics werk ruimte als para meter gebruikt. Opmerking: als uw schaalset upgrade Policy is ingesteld op hand matig, moet u de uitbrei ding Toep assen op de virtuele machines in de set door een upgrade voor deze Vm's aan te roepen. In CLI zou dit AZ vmss update-instances zijn. |6 |1.0.0-Preview |
|[Azure Monitor voor VM's inschakelen](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Schakel Azure Monitor in voor de Virtual Machines (Vm's) in het opgegeven bereik (beheer groep, abonnement of resource groep). Hiermee wordt Log Analytics werk ruimte als para meter gebruikt. |6 |1.0.0-Preview |
