---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/02/2020
ms.author: dacoulte
ms.openlocfilehash: e62ad440eb3f9af41f2a2cddd6d882887638e799
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78262745"
---
|Naam |Beschrijving |Beleid |Versie |
|---|---|---|---|
|[Azure Monitor voor Virtual Machine Scale Sets inschakelen](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Azure Monitor in te scha kelen voor de Virtual Machine Scale Sets in het opgegeven bereik (beheer groep, abonnement of resource groep). Hiermee wordt Log Analytics werk ruimte als para meter gebruikt. Opmerking: als uw schaalset upgrade Policy is ingesteld op hand matig, moet u de uitbrei ding Toep assen op de virtuele machines in de set door een upgrade voor deze Vm's aan te roepen. In CLI zou dit AZ vmss update-instances zijn. |6 |1.0.0-Preview |
|[Azure Monitor voor VM's inschakelen](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Schakel Azure Monitor in voor de Virtual Machines (Vm's) in het opgegeven bereik (beheer groep, abonnement of resource groep). Hiermee wordt Log Analytics werk ruimte als para meter gebruikt. |6 |1.0.0-Preview |
