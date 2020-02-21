---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/19/2020
ms.author: dacoulte
ms.openlocfilehash: 4ced8d3cc3c87f686cf9722adfaefae59eb3f759
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77495073"
---
|Name |Beschrijving |Effect (s) |Version |Bron |
|---|---|---|---|
|[Alle autorisatie regels behalve RootManageSharedAccessKey moeten worden verwijderd uit de Event hub-naam ruimte](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb278e460-7cfc-4451-8294-cccc40a940d7) |Event Hub-clients moeten een beleid voor het toegangsniveau van naamruimte die toegang tot alle wachtrijen en onderwerpen in een naamruimte biedt niet gebruiken. Als u wilt uitlijnen met het beveiligings model met minimale bevoegdheden, moet u toegangs beleid maken op het niveau van de entiteit voor wacht rijen en onderwerpen om alleen toegang te verlenen aan de specifieke entiteit |Controleren, weigeren, uitgeschakeld |1.0.1 |[GitHub](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditNamespaceAccessRules_Audit.json)
|[Autorisatie regels voor het event hub-exemplaar moeten worden gedefinieerd](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4826e5f-6a27-407c-ae3e-9582eb39891d) |Aanwezigheid van autorisatie regels controleren op Event hub-entiteiten om beperkte toegang te verlenen |AuditIfNotExists, uitgeschakeld |1.0.0 |[GitHub](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditEventHubAccessRules_Audit.json)
|[Diagnostische logboeken in Event hub moeten worden ingeschakeld](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |Het inschakelen van Diagnostische logboeken controleren. Zo kunt u een activiteit spoor opnieuw maken om te gebruiken voor onderzoek doeleinden; Wanneer er een beveiligings incident optreedt of wanneer uw netwerk is aangetast |AuditIfNotExists, uitgeschakeld |2.0.0 |[GitHub](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json)
