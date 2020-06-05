---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/13/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 22f6f040d333a526940a688c16277be568520f50
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83651207"
---
|Naam |Beschrijving |Gevolg(en) |Versie |GitHub |
|---|---|---|---|---|
|[Voor Service Fabric-clusters moet de eigenschap ClusterProtectionLevel zijn ingesteld op EncryptAndSign](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |Service Fabric kent drie niveaus van beveiliging (None, Sign en EncryptAndSign) voor communicatie tussen knooppunten en gebruikt hierbij een primair clustercertificaat. Stel het beveiligingsniveau in om te zorgen dat alle berichten van en naar knooppunten worden versleuteld en digitaal worden ondertekend. |Controleren, Weigeren, Uitgeschakeld |1.1.0 |[Koppeling](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |
|[Service Fabric-clusters mogen alleen gebruikmaken van Azure Active Directory voor clientverificatie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |Exclusief gebruik van clientverificatie via Azure Active Directory in Service Fabric controleren |Controleren, Weigeren, Uitgeschakeld |1.1.0 |[Koppeling](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |
