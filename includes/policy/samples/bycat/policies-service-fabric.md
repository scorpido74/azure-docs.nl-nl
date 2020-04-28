---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/26/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 55ec6f97784fc6c8dc34b67000e9b8f5f67f783c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82191131"
---
|Naam |Beschrijving |Effect (s) |Versie |GitHub |
|---|---|---|---|---|
|[Voor Service Fabric clusters moet de eigenschap ClusterProtectionLevel zijn ingesteld op EncryptAndSign](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |Service Fabric biedt drie beveiligings niveaus (geen, ondertekening en EncryptAndSign) voor communicatie tussen knoop punten met behulp van een primair cluster certificaat. Stel het beveiligings niveau in om ervoor te zorgen dat alle knoop punt-naar-knooppunt-berichten worden versleuteld en digitaal worden ondertekend |Controleren, weigeren, uitgeschakeld |1.1.0 |[Koppeling](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |
|[Service Fabric-clusters mogen alleen gebruikmaken van Azure Active Directory voor client verificatie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |Het gebruik van client authenticatie alleen controleren via Azure Active Directory in Service Fabric |Controleren, weigeren, uitgeschakeld |1.1.0 |[Koppeling](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |
