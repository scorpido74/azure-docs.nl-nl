---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/24/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: afc6c91b81c1c822b4204c3698f4d758fc3a2805
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82147182"
---
|Naam |Beschrijving |Effect (s) |Versie |GitHub |
|---|---|---|---|---|
|[Alle autorisatie regels behalve RootManageSharedAccessKey moeten worden verwijderd uit de Event hub-naam ruimte](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb278e460-7cfc-4451-8294-cccc40a940d7) |Event hub-clients mogen geen toegangs beleid op naam ruimte niveau gebruiken dat toegang biedt tot alle wacht rijen en onderwerpen in een naam ruimte. Als u wilt uitlijnen met het beveiligings model met minimale bevoegdheden, moet u toegangs beleid maken op het niveau van de entiteit voor wacht rijen en onderwerpen om alleen toegang te verlenen aan de specifieke entiteit |Controleren, weigeren, uitgeschakeld |1.0.1 |[Koppeling](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditNamespaceAccessRules_Audit.json)
|[Autorisatie regels voor het event hub-exemplaar moeten worden gedefinieerd](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4826e5f-6a27-407c-ae3e-9582eb39891d) |Aanwezigheid van autorisatie regels controleren op Event hub-entiteiten om beperkte toegang te verlenen |AuditIfNotExists, uitgeschakeld |1.0.0 |[Koppeling](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditEventHubAccessRules_Audit.json)
|[Diagnostische logboeken in Event hub moeten worden ingeschakeld](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |Het inschakelen van Diagnostische logboeken controleren. Zo kunt u een activiteit spoor opnieuw maken om te gebruiken voor onderzoek doeleinden; Wanneer er een beveiligings incident optreedt of wanneer uw netwerk is aangetast |AuditIfNotExists, uitgeschakeld |2.0.0 |[Koppeling](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json)
