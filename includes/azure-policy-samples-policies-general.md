---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/12/2020
ms.author: dacoulte
ms.openlocfilehash: 9054340799ac6f95a9a3064351193f0680a000c9
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77193093"
---
|Naam |Beschrijving |Effect (s) |Version |
|---|---|---|---|
|[Allowed locations](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/AllowedLocations_Deny.json) (Toegestane locaties) |Met dit beleid kunt u de locaties beperken die uw organisatie kan opgeven tijdens het implementeren van resources. Dit beleid wordt gebruikt om uw geografische nalevingsvereisten af te dwingen. Resourcegroepen, Microsoft.AzureActiveDirectory/b2cDirectories en resources die gebruikmaken van de regio 'global' worden uitgesloten. |toestaan |1.0.0 |
|[Toegestane locaties voor resource groepen](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/ResourceGroupAllowedLocations_Deny.json) |Met dit beleid kunt u de locaties beperken waarop uw organisatie resource groepen kan maken in. Dit beleid wordt gebruikt om uw geografische nalevingsvereisten af te dwingen. |toestaan |1.0.0 |
|[Allowed resource types](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/AllowedResourceTypes_Deny.json) (Toegestane resourcetypen) |Met dit beleid kunt u de resource typen opgeven die uw organisatie kan implementeren. Dit beleid is alleen van invloed op resource typen die de tags ' labels ' en ' Location ' ondersteunen. Als u alle resources wilt beperken, moet u dit beleid dupliceren en de ' mode ' wijzigen in ' all '. |toestaan |1.0.0 |
|[Locatie van de controle resource komt overeen met de locatie van de resource groep](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/ResourcesInResourceGroupLocation_Audit.json) |Controleren of de resource locatie overeenkomt met de locatie van de resource groep |controle |1.0.0 |
|[Gebruik van aangepaste RBAC-regels controleren](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |Audit ingebouwde rollen zoals ' eigenaar, Contributer, lezer ' in plaats van aangepaste RBAC-rollen, die gevoelig zijn voor fouten. Het gebruik van aangepaste rollen wordt behandeld als een uitzonde ring en vereist een rigoureuze beoordeling en bedreigings modellen |Controle, uitgeschakeld |1.0.0 |
|[De rollen van het aangepaste abonnements eigenaar mogen niet bestaan](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |Dit beleid zorgt ervoor dat er geen rollen voor aangepaste abonnements eigenaren bestaan. |Controle, uitgeschakeld |1.0.0 |
|[Not allowed resource types](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/InvalidResourceTypes_Deny.json) (Niet-toegestane resourcetypen) |Met dit beleid kunt u de resource typen opgeven die uw organisatie niet kan implementeren. |Weigeren |1.0.0 |
