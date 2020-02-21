---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/19/2020
ms.author: dacoulte
ms.openlocfilehash: b545e9a33c035ac2556c03efc5bc5c03fdc4b115
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77494937"
---
|Name |Beschrijving |Beleidsregels |Version |
|---|---|---|---|
|[Azure Monitor inschakelen voor VM Scale Sets (VMSS)](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Azure Monitor in te scha kelen voor de VM Scale Sets in het opgegeven bereik (beheer groep, abonnement of resource groep). Hiermee wordt Log Analytics werk ruimte als para meter gebruikt. Opmerking: als uw schaalset upgrade Policy is ingesteld op hand matig, moet u de uitbrei ding Toep assen op de virtuele machines in de set door een upgrade voor deze Vm's aan te roepen. In CLI zou dit AZ vmss update-instances zijn. |6 |1.0.0-Preview |
|[Azure Monitor voor VM's inschakelen](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Schakel Azure Monitor in voor de Virtual Machines (Vm's) in het opgegeven bereik (beheer groep, abonnement of resource groep). Hiermee wordt Log Analytics werk ruimte als para meter gebruikt. |6 |1.0.0-Preview |
