---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 667d017e8febcf1abcc1cfe21ecfaa43ae75ea6d
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172749"
---
|Naam |Beschrijving |Effect (s) |Version |
|---|---|---|---|
|[Toegestane Sku's voor virtuele machines](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMSkusAllowed_Deny.json) |Met dit beleid kunt u een set SKU's voor virtuele machines opgeven die uw organisatie mag implementeren. |Weigeren |1.0.0 |
|[Virtuele machines controleren zonder nood herstel zijn geconfigureerd](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |Controleer de virtuele machines waarvoor herstel na nood geval niet is geconfigureerd. Ga naar https://aka.ms/asr-docvoor meer informatie over herstel na nood gevallen. |auditIfNotExists |1.0.0 |
|[Virtuele machines controleren die geen beheerde schijven gebruiken](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMRequireManagedDisk_Audit.json) |Met dit beleid worden Vm's gecontroleerd die geen beheerde schijven gebruiken |controle |1.0.0 |
|[Standaard micro soft IaaSAntimalware-extensie voor Windows Server implementeren](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMAntimalwareExtension_Deploy.json) |Met dit beleid wordt een micro soft IaaSAntimalware-extensie geïmplementeerd met een standaard configuratie wanneer een virtuele machine niet is geconfigureerd met de uitbrei ding voor antimalware. |deployIfNotExists |1.0.0 |
|[Diagnostische logboeken in Virtual Machine Scale Sets moeten worden ingeschakeld](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |Het is raadzaam om Logboeken in te scha kelen, zodat de activiteit Trail opnieuw kan worden gemaakt wanneer er onderzoeken vereist zijn in het geval van een incident of een inbreuk. |AuditIfNotExists, uitgeschakeld |1.0.0 |
|[Micro soft antimalware voor Azure moet zo worden geconfigureerd dat beveiligings handtekeningen automatisch worden bijgewerkt](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VirtualMachines_AntiMalwareAutoUpdate_AuditIfNotExists.json) |Met dit beleid wordt een virtuele Windows-machine gecontroleerd die niet is geconfigureerd met automatische updates van hand tekeningen van micro soft antimalware-beveiliging. |AuditIfNotExists, uitgeschakeld |1.0.0 |
|[Micro soft IaaSAntimalwaree extension moet worden geïmplementeerd op Windows-servers](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/WindowsServers_AntiMalware_AuditIfNotExists.json) |Met dit beleid wordt een Windows Server-VM gecontroleerd zonder dat de micro soft IaaSAntimalware-extensie is geïmplementeerd. |AuditIfNotExists, uitgeschakeld |1.0.0 |
|[Alleen goedgekeurde VM-uitbrei dingen moeten worden geïnstalleerd](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VirtualMachines_ApprovedExtensions_Audit.json) |Dit beleid is van toepassing op de extensies van de virtuele machine die niet zijn goedgekeurd. |Controleren, weigeren, uitgeschakeld |1.0.0 |
|[Automatische patching van besturingssysteem installatie kopieën vereisen op Virtual Machine Scale Sets](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMSSOSUpgradeHealthCheck_Deny.json) |Met dit beleid wordt het inschakelen van automatische installatie kopie patches voor het besturings systeem op Virtual Machine Scale Sets altijd veilig Virtual Machines beveiligen door de nieuwste beveiligings patches elke maand veilig toe te passen. |toestaan |1.0.0 |
|[Niet-gekoppelde schijven moeten worden versleuteld](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |Met dit beleid wordt een niet-gekoppelde schijf gecontroleerd zonder dat versleuteling is ingeschakeld. |Controle, uitgeschakeld |1.0.0 |
|[Virtuele machines moeten worden gemigreerd naar nieuwe Azure Resource Manager-resources](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/ClassicCompute_Audit.json) |Gebruik nieuwe Azure Resource Manager voor uw virtuele machines om beveiligings uitbreidingen te bieden zoals: sterker toegangs beheer (RBAC), betere controle, implementatie en beheer op basis van ARM, toegang tot beheerde identiteiten, toegang tot de sleutel kluis voor geheimen, Azure Op AD gebaseerde verificatie en ondersteuning voor Tags en resource groepen voor eenvoudiger beveiligings beheer |Controleren, weigeren, uitgeschakeld |1.0.0 |
