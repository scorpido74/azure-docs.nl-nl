---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/05/2020
ms.author: dacoulte
ms.openlocfilehash: e6ac52694dd4a786714b571f50dc5adaf79dff06
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78668814"
---
|Naam |Beschrijving |Beleidsregels |Version |
|---|---|---|---|
|[Azure Monitor voor Virtual Machine Scale Sets inschakelen](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Azure Monitor in te scha kelen voor de Virtual Machine Scale Sets in het opgegeven bereik (beheer groep, abonnement of resource groep). Hiermee wordt Log Analytics werk ruimte als para meter gebruikt. Opmerking: als uw schaalset upgrade Policy is ingesteld op hand matig, moet u de uitbrei ding Toep assen op de virtuele machines in de set door een upgrade voor deze Vm's aan te roepen. In CLI zou dit AZ vmss update-instances zijn. |6 |1.0.0-Preview |
|[Azure Monitor voor VM's inschakelen](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Schakel Azure Monitor in voor de Virtual Machines (Vm's) in het opgegeven bereik (beheer groep, abonnement of resource groep). Hiermee wordt Log Analytics werk ruimte als para meter gebruikt. |6 |1.0.0-Preview |
