---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 06/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 2977b4028772790c1a4e5358969dc1eaf089b08d
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/11/2020
ms.locfileid: "84709126"
---
|Naam |Beschrijving |Beleidsregels |Versie |
|---|---|---|---|
|[Azure Monitor voor virtuele-machineschaalsets inschakelen](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Schakel Azure Monitor in voor de virtuele-machineschaalsets in het opgegeven bereik (beheergroep, abonnement of resourcegroep). De Log Analytics-werkruimte wordt als parameter gebruikt. Opmerking: als voor uw schaalset de waarde van upgradePolicy is ingesteld op Handmatig, moet u de extensie op alle VM's in de set toepassen door een aanroep voor het upgraden van de VM's uit te voeren. In CLI doet u dit met az vmss update-instances. |6 |1.0.1 |
|[Azure Monitor voor VM's inschakelen](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Schakel Azure Monitor in voor de virtuele machines (VM's) in het opgegeven bereik (beheergroep, abonnement of resourcegroep). De Log Analytics-werkruimte wordt als parameter gebruikt. |10 |2.0.0 |
