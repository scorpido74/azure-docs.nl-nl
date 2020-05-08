---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/05/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 1e2837da421c52d80e7144d16706e5b9f1b91728
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82966132"
---
|Naam |Beschrijving |Effect (s) |Versie |GitHub |
|---|---|---|---|---|
|[Diagnostische instellingen voor Data Lake Analytics implementeren in Event hub](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4daddf25-4823-43d4-88eb-2419eb6dcc08) |Hiermee worden de diagnostische instellingen voor de Data Lake Analytics geïmplementeerd om te streamen naar een regionale Event hub wanneer een Data Lake Analytics waarvoor deze diagnostische instellingen ontbreken, wordt gemaakt of bijgewerkt. |DeployIfNotExists, uitgeschakeld |2.0.0 |[Koppeling](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DataLakeAnalytics_DeployDiagnosticLog_Deploy_EventHub.json) |
|[Diagnostische instellingen voor Data Lake Analytics implementeren op Log Analytics werk ruimte](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd56a5a7c-72d7-42bc-8ceb-3baf4c0eae03) |Hiermee worden de diagnostische instellingen voor de Data Lake Analytics geïmplementeerd om te streamen naar een regionale Log Analytics-werk ruimte wanneer een Data Lake Analytics waarvoor deze diagnostische instellingen ontbreken, wordt gemaakt of bijgewerkt. |DeployIfNotExists, uitgeschakeld |1.0.0 |[Koppeling](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DataLakeAnalytics_DeployDiagnosticLog_Deploy_LogAnalytics.json) |
|[Diagnostische logboeken in Data Lake Analytics moeten worden ingeschakeld](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |Het inschakelen van Diagnostische logboeken controleren. Zo kunt u een activiteit spoor opnieuw maken om te gebruiken voor onderzoek doeleinden; Wanneer er een beveiligings incident optreedt of wanneer uw netwerk is aangetast |AuditIfNotExists, uitgeschakeld |2.0.0 |[Koppeling](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |
