---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 8fe3dca69974f1df09ffb9ca4e1ece5a614f61f5
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172966"
---
|Naam |Beschrijving |Beleidsregels |Version |
|---|---|---|---|
|[Azure Monitor inschakelen voor VM Scale Sets (VMSS)](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Azure Monitor in te scha kelen voor de VM Scale Sets in het opgegeven bereik (beheer groep, abonnement of resource groep). Hiermee wordt Log Analytics werk ruimte als para meter gebruikt. Opmerking: als uw schaalset upgrade Policy is ingesteld op hand matig, moet u de uitbrei ding Toep assen op de virtuele machines in de set door een upgrade voor deze Vm's aan te roepen. In CLI zou dit AZ vmss update-instances zijn. |6 |1.0.0-Preview |
|[Azure Monitor voor VM's inschakelen](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Schakel Azure Monitor in voor de Virtual Machines (Vm's) in het opgegeven bereik (beheer groep, abonnement of resource groep). Hiermee wordt Log Analytics werk ruimte als para meter gebruikt. |6 |1.0.0-Preview |
