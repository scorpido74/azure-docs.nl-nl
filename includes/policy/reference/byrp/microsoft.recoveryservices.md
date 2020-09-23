---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/16/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: ddb6402e4250ca9b361bcb40a393c125d271fdf6
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90985103"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Backup moet zijn ingeschakeld voor virtuele machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Zorg ervoor dat uw Azure-Virtual Machines worden beveiligd door Azure Backup in te scha kelen. Azure Backup is een veilige en voordelige oplossing voor gegevens bescherming voor Azure. |AuditIfNotExists, uitgeschakeld |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|[Instellen dat VM's die op een locatie worden gemaakt naar een bestaande centrale kluis op dezelfde locatie een back-up krijgen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09ce66bc-1220-4153-8104-e3f51c936913) |Met dit beleid configureert u Azure Backup-beveiliging op VM's op een bepaalde locatie naar een bestaande centrale kluis op dezelfde locatie. Dit geldt alleen voor VM’s die nog niet zijn geconfigureerd voor back-up. Het is raadzaam dat dit beleid aan niet meer dan 200 VM's wordt toegewezen. Als het beleid is toegewezen aan meer dan 200 Vm's, kan dit ertoe leiden dat de back-up enkele uren na het gedefinieerde schema wordt geactiveerd. Dit beleid zal worden uitgebreid om meer VM-installatiekopieën te ondersteunen. |deployIfNotExists, auditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineBackup_Backup_DeployIfNotExists.json) |
|[Diagnostische instellingen voor Recovery Services-kluis implementeren in Log Analytics-werkruimten voor resource-specifieke categorieën.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc717fb0c-d118-4c43-ab3d-ece30ac81fb3) |Diagnostische instellingen voor Recovery Services-kluis implementeren om te streamen naar Log Analytics-werkruimten voor resource-specifieke categorieën. Als een van de resource-specifieke categorieën niet is ingeschakeld, wordt er een nieuwe diagnostische instelling gemaakt. |deployIfNotExists |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/EnableRecoveryServiceVaultDiagnosticSetting_Backup_DeployIfNotExist.json) |
