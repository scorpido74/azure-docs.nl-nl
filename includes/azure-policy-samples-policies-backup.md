---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/12/2020
ms.author: dacoulte
ms.openlocfilehash: 4a6c0cad4a93d0d99573a348070823b909c75c7e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79299410"
---
|Name |Beschrijving |Effect(en) |Versie |GitHub |
|---|---|---|---|---|
|[Azure Backup moet zijn ingeschakeld voor virtuele machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Met dit beleid wordt gecontroleerd of azure backup-service is ingeschakeld voor alle virtuele machines. Azure Backup is een kosteneffectieve back-upoplossing met één klik die het herstel van gegevens vereenvoudigt en is eenvoudiger in te schakelen dan andere cloudback-upservices. |AuditIfNotExists, Uitgeschakeld |1.0.0 |[Koppeling](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json)
|[Back-up configureren op VM's van een locatie naar een bestaande centrale Vault op dezelfde locatie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09ce66bc-1220-4153-8104-e3f51c936913) |Met dit beleid configureert Azure Backup-beveiliging op VM's op een bepaalde locatie naar een bestaande centrale kluis op dezelfde locatie. Het is alleen van toepassing op vm's die nog niet zijn geconfigureerd voor back-up. Het wordt aanbevolen dat dit beleid wordt toegewezen aan niet meer dan 200 VM's. Als het beleid is toegewezen voor meer dan 200 VM's, kan dit ertoe leiden dat de back-up een paar uur buiten het gedefinieerde schema wordt geactiveerd. Dit beleid wordt verbeterd om meer VM-afbeeldingen te ondersteunen. |deployIfNotExists, auditIfNotExists, uitgeschakeld |1.0.0 |[Koppeling](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineBackup_Backup_DeployIfNotExists.json)
