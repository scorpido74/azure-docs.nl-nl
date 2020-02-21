---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/19/2020
ms.author: dacoulte
ms.openlocfilehash: 3307a697e6e7998d9023837b03b4802511ef3bf7
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77495306"
---
|Name |Beschrijving |Effect (s) |Version |Bron |
|---|---|---|---|
|[Alle autorisatie regels behalve RootManageSharedAccessKey moeten worden verwijderd uit Service Bus naam ruimte](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa1817ec0-a368-432a-8057-8371e17ac6ee) |Service Bus-clients moeten een beleid voor het toegangsniveau van naamruimte die toegang tot alle wachtrijen en onderwerpen in een naamruimte biedt niet gebruiken. Als u wilt uitlijnen met het beveiligings model met minimale bevoegdheden, moet u toegangs beleid maken op het niveau van de entiteit voor wacht rijen en onderwerpen om alleen toegang te verlenen aan de specifieke entiteit |Controleren, weigeren, uitgeschakeld |1.0.1 |[GitHub](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditNamespaceAccessRules_Audit.json)
|[Diagnostische logboeken in Service Bus moeten worden ingeschakeld](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |Het inschakelen van Diagnostische logboeken controleren. Zo kunt u een activiteit spoor opnieuw maken om te gebruiken voor onderzoek doeleinden; Wanneer er een beveiligings incident optreedt of wanneer uw netwerk is aangetast |AuditIfNotExists, uitgeschakeld |2.0.0 |[GitHub](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json)
