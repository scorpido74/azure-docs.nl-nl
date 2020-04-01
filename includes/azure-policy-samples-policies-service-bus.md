---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/12/2020
ms.author: dacoulte
ms.openlocfilehash: 10cd98a350ceb622f07fcb6c85477260b2c7cb60
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79382025"
---
|Name |Beschrijving |Effect(en) |Versie |GitHub |
|---|---|---|---|---|
|[Alle autorisatieregels behalve RootManageSharedAccessKey moeten worden verwijderd uit de naamruimte van servicebus](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa1817ec0-a368-432a-8057-8371e17ac6ee) |Servicebusclients mogen geen toegangsbeleid op naamruimteniveau gebruiken dat toegang biedt tot alle wachtrijen en onderwerpen in een naamruimte. Als u wilt afstemmen op het beveiligingsmodel voor de minste bevoegdheden, moet u toegangsbeleid op entiteitsniveau maken voor wachtrijen en onderwerpen om alleen toegang te bieden tot de specifieke entiteit |Controleren, Weigeren, Uitgeschakeld |1.0.1 |[Koppeling](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditNamespaceAccessRules_Audit.json)
|[Diagnostische logboeken in servicebus moeten zijn ingeschakeld](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |Controle inschakelen van diagnostische logboeken. Hiermee u activiteitspaden opnieuw maken om te gebruiken voor onderzoeksdoeleinden; wanneer zich een beveiligingsincident voordoet of wanneer uw netwerk wordt gecompromitteerd |AuditIfNotExists, Uitgeschakeld |2.0.0 |[Koppeling](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json)
