---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/12/2020
ms.author: dacoulte
ms.openlocfilehash: a20ba49901b2dd768351ae06d7c25179d5cf7586
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192844"
---
|Naam |Beschrijving |Effect (s) |Version |
|---|---|---|---|
|[Alle autorisatie regels behalve RootManageSharedAccessKey moeten worden verwijderd uit de Event hub-naam ruimte](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditNamespaceAccessRules_Audit.json) |Event Hub-clients moeten een beleid voor het toegangsniveau van naamruimte die toegang tot alle wachtrijen en onderwerpen in een naamruimte biedt niet gebruiken. Als u wilt uitlijnen met het beveiligings model met minimale bevoegdheden, moet u toegangs beleid maken op het niveau van de entiteit voor wacht rijen en onderwerpen om alleen toegang te verlenen aan de specifieke entiteit |Controleren, weigeren, uitgeschakeld |1.0.1 |
|[Autorisatie regels voor het event hub-exemplaar moeten worden gedefinieerd](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditEventHubAccessRules_Audit.json) |Aanwezigheid van autorisatie regels controleren op Event hub-entiteiten om beperkte toegang te verlenen |AuditIfNotExists, uitgeschakeld |1.0.0 |
|[Diagnostische logboeken in Event hub moeten worden ingeschakeld](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |Het inschakelen van Diagnostische logboeken controleren. Zo kunt u een activiteit spoor opnieuw maken om te gebruiken voor onderzoek doeleinden; Wanneer er een beveiligings incident optreedt of wanneer uw netwerk is aangetast |AuditIfNotExists, uitgeschakeld |2.0.0 |
