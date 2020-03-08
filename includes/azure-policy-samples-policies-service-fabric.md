---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/05/2020
ms.author: dacoulte
ms.openlocfilehash: 6dde1eab8bc6d7dcc925efebabf365fe86ecac65
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78668375"
---
|Naam |Beschrijving |Effect (s) |Version |GitHub |
|---|---|---|---|---|
|[Voor Service Fabric clusters moet de eigenschap ClusterProtectionLevel zijn ingesteld op EncryptAndSign](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |Service Fabric biedt drie niveaus van beveiliging (geen, aanmelding en EncryptAndSign) voor communicatie van knooppunt-naar-knooppunt met behulp van een primaire clustercertificaat. Stel het beveiligings niveau in om ervoor te zorgen dat alle knoop punt-naar-knooppunt-berichten worden versleuteld en digitaal worden ondertekend |Controle, uitgeschakeld |1.0.0 |[Koppeling](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json)
|[Service Fabric-clusters mogen alleen gebruikmaken van Azure Active Directory voor client verificatie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |Het gebruik van client authenticatie alleen controleren via Azure Active Directory in Service Fabric |Controle, uitgeschakeld |1.0.0 |[Koppeling](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json)
