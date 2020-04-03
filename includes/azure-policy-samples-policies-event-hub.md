---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/02/2020
ms.author: dacoulte
ms.openlocfilehash: 0b44908973425378ee5a95f817675125183871db
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80624330"
---
|Name |Beschrijving |Effect(en) |Versie |GitHub |
|---|---|---|---|---|
|[Alle autorisatieregels behalve RootManageSharedAccessKey moeten worden verwijderd uit de naamruimte van gebeurtenishub](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb278e460-7cfc-4451-8294-cccc40a940d7) |Gebeurtenishubclients mogen geen toegangsbeleid op naamruimteniveau gebruiken dat toegang biedt tot alle wachtrijen en onderwerpen in een naamruimte. Als u wilt afstemmen op het beveiligingsmodel voor de minste bevoegdheden, moet u toegangsbeleid op entiteitsniveau maken voor wachtrijen en onderwerpen om alleen toegang te bieden tot de specifieke entiteit |Controleren, Weigeren, Uitgeschakeld |1.0.1 |[Koppeling](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditNamespaceAccessRules_Audit.json)
|[Autorisatieregels voor de instantie Gebeurtenishub moeten worden gedefinieerd](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4826e5f-6a27-407c-ae3e-9582eb39891d) |Controle over het bestaan van autorisatieregels voor event hub-entiteiten om de minste bevoorrechte toegang te verlenen |AuditIfNotExists, Uitgeschakeld |1.0.0 |[Koppeling](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditEventHubAccessRules_Audit.json)
|[Diagnostische logboeken in gebeurtenishub moeten zijn ingeschakeld](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |Controle inschakelen van diagnostische logboeken. Hiermee u activiteitspaden opnieuw maken om te gebruiken voor onderzoeksdoeleinden; wanneer zich een beveiligingsincident voordoet of wanneer uw netwerk wordt gecompromitteerd |AuditIfNotExists, Uitgeschakeld |2.0.0 |[Koppeling](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json)
