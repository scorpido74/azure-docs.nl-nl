---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 06/23/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 48467f4b28100c4225618f15417a29f20b61a1dd
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85313367"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Voor Service Fabric-clusters moet de eigenschap ClusterProtectionLevel zijn ingesteld op EncryptAndSign](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |Service Fabric kent drie niveaus van beveiliging (None, Sign en EncryptAndSign) voor communicatie tussen knooppunten en gebruikt hierbij een primair clustercertificaat. Stel het beveiligingsniveau in om te zorgen dat alle berichten van en naar knooppunten worden versleuteld en digitaal worden ondertekend |Controleren, Weigeren, Uitgeschakeld |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |
|[Service Fabric-clusters mogen alleen gebruikmaken van Azure Active Directory voor clientverificatie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |Exclusief gebruik van clientverificatie via Azure Active Directory in Service Fabric controleren |Controleren, Weigeren, Uitgeschakeld |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |
