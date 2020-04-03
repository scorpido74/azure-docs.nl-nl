---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/02/2020
ms.author: dacoulte
ms.openlocfilehash: 0447d2e3a5a8f8d39ece7b44522d4a14227360f1
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80624131"
---
|Name |Beschrijving |Effect(en) |Versie |GitHub |
|---|---|---|---|---|
|[Azure Backup moet zijn ingeschakeld voor virtuele machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Met dit beleid wordt gecontroleerd of azure backup-service is ingeschakeld voor alle virtuele machines. Azure Backup is een kosteneffectieve back-upoplossing met één klik die het herstel van gegevens vereenvoudigt en is eenvoudiger in te schakelen dan andere cloudback-upservices. |AuditIfNotExists, Uitgeschakeld |1.0.0 |[Koppeling](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json)
|[Back-up configureren op VM's van een locatie naar een bestaande centrale Vault op dezelfde locatie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09ce66bc-1220-4153-8104-e3f51c936913) |Met dit beleid configureert Azure Backup-beveiliging op VM's op een bepaalde locatie naar een bestaande centrale kluis op dezelfde locatie. Het is alleen van toepassing op vm's die nog niet zijn geconfigureerd voor back-up. Het wordt aanbevolen dat dit beleid wordt toegewezen aan niet meer dan 200 VM's. Als het beleid is toegewezen voor meer dan 200 VM's, kan dit ertoe leiden dat de back-up een paar uur buiten het gedefinieerde schema wordt geactiveerd. Dit beleid wordt verbeterd om meer VM-afbeeldingen te ondersteunen. |deployIfNotExists, auditIfNotExists, uitgeschakeld |1.0.0 |[Koppeling](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineBackup_Backup_DeployIfNotExists.json)
