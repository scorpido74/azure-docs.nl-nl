---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/29/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 3dabcb0b27a19b275556135f73a0c504a8ad1648
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/31/2020
ms.locfileid: "84234855"
---
|Naam |Beschrijving |Gevolg(en) |Versie |GitHub |
|---|---|---|---|---|
|[Diagnostische instellingen implementeren voor Zoekservices naar Event Hub](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3d5da587-71bd-41f5-ac95-dd3330c2d58d) |Hiermee worden de diagnostische instellingen voor Zoekservices geïmplementeerd en naar een regionale Event Hub gestreamd wanneer een Zoekservice waarvoor deze diagnostische instellingen ontbreken, wordt gemaakt of bijgewerkt. |DeployIfNotExists, uitgeschakeld |2.0.0 |[Koppeling](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Search_DeployDiagnosticLog_Deploy_EventHub.json) |
|[Diagnostische instellingen implementeren voor Zoekservices naar Log Analytics-werkruimte](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08ba64b8-738f-4918-9686-730d2ed79c7d) |Hiermee worden de diagnostische instellingen voor Zoekservices geïmplementeerd en naar een regionale Log Analytics-werkruimte gestreamd wanneer een Zoekservice waarvoor deze diagnostische instellingen ontbreken, wordt gemaakt of bijgewerkt. |DeployIfNotExists, uitgeschakeld |1.0.0 |[Koppeling](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Search_DeployDiagnosticLog_Deploy_LogAnalytics.json) |
|[Diagnostische logboeken in zoekservices moeten zijn ingeschakeld](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |Inschakeling van diagnostische logboeken controleren. Hiermee kunt u een activiteitenspoor opnieuw maken om te gebruiken voor onderzoeksdoeleinden wanneer een beveiligingsincident optreedt of wanneer uw netwerk is aangetast |AuditIfNotExists, uitgeschakeld |2.0.0 |[Koppeling](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json) |
