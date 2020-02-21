---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/19/2020
ms.author: dacoulte
ms.openlocfilehash: c8f232fcbec183eaffd79fb8fcd9330f5088c39d
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77495245"
---
|Name |Beschrijving |Effect (s) |Version |Bron |
|---|---|---|---|
|[Alleen beveiligde verbindingen met uw Redis Cache moeten worden ingeschakeld](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Het inschakelen van alleen verbindingen via SSL naar Redis Cache controleren. Gebruik van beveiligde verbindingen zorgt voor verificatie tussen de server en de service en beveiligt de door Voer van gegevens via netwerklaag aanvallen, zoals man-in-the-Middle, inkomend en inkomend en sessie overname |Controleren, weigeren, uitgeschakeld |1.0.0 |[GitHub](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json)
