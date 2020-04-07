---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/03/2020
ms.author: dacoulte
ms.openlocfilehash: 6497a1444b3f502d3e5169ff8ace2884e4e045c9
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758860"
---
|Name |Beschrijving |Effect(en) |Versie |GitHub |
|---|---|---|---|---|
|[Containerregisters moeten worden versleuteld met een door de klant beheerde sleutel (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Controleer containerregisters die geen versleuteling hebben ingeschakeld met CMK (Customer-Managed Keys). Voor meer informatie over CMK [https://aka.ms/acr/CMK](https://aka.ms/acr/CMK)encryptie, u terecht op:. |Audit, Uitgeschakeld |1.0.0-preview |[Koppeling](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json)
|[Containerregisters mogen geen onbeperkte netwerktoegang toestaan](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Controleer containerregisters die geen Netwerk- of VNET-regels hebben geconfigureerd en die standaard alle netwerktoegang toestaan. Containerregisters met ten minste één IP/ Firewall-regel of geconfigureerd virtueel netwerk worden als compatibel beschouwd. Ga voor meer informatie over de [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet)regels van het Container Registry Network naar: . |Audit, Uitgeschakeld |1.0.0-preview |[Koppeling](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json)
