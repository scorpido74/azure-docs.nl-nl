---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/03/2020
ms.author: dacoulte
ms.openlocfilehash: 77b011300d7673d4d96d89fda3f19dacf9c635e9
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758352"
---
|Name |Beschrijving |Effect(en) |Versie |GitHub |
|---|---|---|---|---|
|[Clusterfabricclusters moeten de eigenschap ClusterProtectionLevel hebben ingesteld op EncryptAndSign](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |Service Fabric biedt drie beschermingsniveaus (Geen, Ondertekenen en Versleuteling) voor knooppuntcommunicatie met behulp van een primair clustercertificaat. Stel het beschermingsniveau in om ervoor te zorgen dat alle knooppuntberichten worden versleuteld en digitaal ondertekend |Audit, Uitgeschakeld |1.0.0 |[Koppeling](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json)
|[Servicesfabricclusters mogen alleen Azure Active Directory gebruiken voor clientverificatie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |Het gebruik van clientverificatie alleen controleren via Azure Active Directory in Service Fabric |Audit, Uitgeschakeld |1.0.0 |[Koppeling](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json)
