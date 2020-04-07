---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/03/2020
ms.author: dacoulte
ms.openlocfilehash: 156f253e9559f493ba6cccde4de4744068a32fc4
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758845"
---
|Name |Beschrijving |Effect(en) |Versie |GitHub |
|---|---|---|---|---|
|[Azure Backup moet zijn ingeschakeld voor virtuele machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Met dit beleid wordt gecontroleerd of azure backup-service is ingeschakeld voor alle virtuele machines. Azure Backup is een kosteneffectieve back-upoplossing met één klik die het herstel van gegevens vereenvoudigt en is eenvoudiger in te schakelen dan andere cloudback-upservices. |AuditIfNotExists, Uitgeschakeld |1.0.0 |[Koppeling](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json)
|[Back-up configureren op VM's van een locatie naar een bestaande centrale Vault op dezelfde locatie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09ce66bc-1220-4153-8104-e3f51c936913) |Met dit beleid configureert Azure Backup-beveiliging op VM's op een bepaalde locatie naar een bestaande centrale kluis op dezelfde locatie. Het is alleen van toepassing op vm's die nog niet zijn geconfigureerd voor back-up. Het wordt aanbevolen dat dit beleid wordt toegewezen aan niet meer dan 200 VM's. Als het beleid is toegewezen voor meer dan 200 VM's, kan dit ertoe leiden dat de back-up een paar uur buiten het gedefinieerde schema wordt geactiveerd. Dit beleid wordt verbeterd om meer VM-afbeeldingen te ondersteunen. |deployIfNotExists, auditIfNotExists, uitgeschakeld |1.0.0 |[Koppeling](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineBackup_Backup_DeployIfNotExists.json)
