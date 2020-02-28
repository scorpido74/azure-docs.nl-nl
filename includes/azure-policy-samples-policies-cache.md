---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/26/2020
ms.author: dacoulte
ms.openlocfilehash: f3ceb931fa3f99da4ca5003e7162053ff9d91341
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77780266"
---
|Name |Beschrijving |Effect (s) |Version |Bron |
|---|---|---|---|---|
|[Alleen beveiligde verbindingen met uw Redis Cache moeten worden ingeschakeld](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Het inschakelen van alleen verbindingen via SSL naar Redis Cache controleren. Gebruik van beveiligde verbindingen zorgt voor verificatie tussen de server en de service en beveiligt de door Voer van gegevens via netwerklaag aanvallen, zoals man-in-the-Middle, inkomend en inkomend en sessie overname |Controleren, weigeren, uitgeschakeld |1.0.0 |[GitHub](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json)
