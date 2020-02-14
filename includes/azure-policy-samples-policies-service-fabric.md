---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/12/2020
ms.author: dacoulte
ms.openlocfilehash: 221633fcb459e39a4c11e869b9214d985cd5ef0f
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192732"
---
|Naam |Beschrijving |Effect (s) |Version |
|---|---|---|---|
|[Voor Service Fabric clusters moet de eigenschap ClusterProtectionLevel zijn ingesteld op EncryptAndSign](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |Service Fabric biedt drie niveaus van beveiliging (geen, aanmelding en EncryptAndSign) voor communicatie van knooppunt-naar-knooppunt met behulp van een primaire clustercertificaat. Stel het beveiligings niveau in om ervoor te zorgen dat alle knoop punt-naar-knooppunt-berichten worden versleuteld en digitaal worden ondertekend |Controle, uitgeschakeld |1.0.0 |
|[Service Fabric-clusters mogen alleen gebruikmaken van Azure Active Directory voor client verificatie](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |Het gebruik van client authenticatie alleen controleren via Azure Active Directory in Service Fabric |Controle, uitgeschakeld |1.0.0 |
