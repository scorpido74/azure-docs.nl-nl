---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/05/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8611eed3502eab77f775914333186f69c259456e
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82837262"
---
|Naam |Beschrijving |Effect (s) |Versie |GitHub |
|---|---|---|---|---|
|[Diagnostische instellingen voor batch-account implementeren in Event hub](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdb51110f-0865-4a6e-b274-e2e07a5b2cd7) |Hiermee worden de diagnostische instellingen voor batch-account geïmplementeerd voor het streamen naar een regionale Event hub wanneer een batch-account wordt gemaakt waarvoor deze diagnostische instellingen ontbreken. |DeployIfNotExists, uitgeschakeld |2.0.0 |[Koppeling](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Batch_DeployDiagnosticLog_Deploy_EventHub.json) |
|[Diagnostische instellingen voor batch-account implementeren naar Log Analytics werk ruimte](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc84e5349-db6d-4769-805e-e14037dab9b5) |Hiermee worden de diagnostische instellingen voor batch-account geïmplementeerd voor het streamen naar een regionale Log Analytics werkruimte wanneer een batch-account dat deze diagnostische instellingen mist, wordt gemaakt of bijgewerkt. |DeployIfNotExists, uitgeschakeld |1.0.0 |[Koppeling](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Batch_DeployDiagnosticLog_Deploy_LogAnalytics.json) |
|[Diagnostische logboeken in batch-accounts moeten worden ingeschakeld](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |Het inschakelen van Diagnostische logboeken controleren. Zo kunt u een activiteit spoor opnieuw maken om te gebruiken voor onderzoek doeleinden; Wanneer er een beveiligings incident optreedt of wanneer uw netwerk is aangetast |AuditIfNotExists, uitgeschakeld |2.0.0 |[Koppeling](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |
|[Metrische waarschuwings regels moeten worden geconfigureerd voor batch-accounts](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26ee67a2-f81a-4ba8-b9ce-8550bd5ee1a7) |De configuratie van regels voor metrische waarschuwingen voor een batch-account controleren om de vereiste metriek in te scha kelen |AuditIfNotExists, uitgeschakeld |1.0.0 |[Koppeling](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditMetricAlerts_Audit.json) |
