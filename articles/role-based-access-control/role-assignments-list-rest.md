---
title: Roltoewijzingen weergeven met Azure RBAC en de REST API
description: Meer informatie over het bepalen van welke resources gebruikers, groepen, serviceprincipals of beheerde identiteiten toegang hebben tot het gebruik van RBAC (Azure role-based access control) en de REST API.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: a494e7fd4c9fb79faa6a1d8cb2c3c871796ccdc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062157"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-rest-api"></a>Roltoewijzingen weergeven met Azure RBAC en de REST API

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]In dit artikel wordt beschreven hoe u roltoewijzingen weergeven met behulp van de REST API.

> [!NOTE]
> Als uw organisatie beheerfuncties heeft uitbesteed aan een serviceprovider die [azure-gedelegeerd resourcebeheer](../lighthouse/concepts/azure-delegated-resource-management.md)gebruikt, worden roltoewijzingen die zijn geautoriseerd door die serviceprovider, hier niet weergegeven.

## <a name="list-role-assignments"></a>Lijst met roltoewijzingen weergeven

In RBAC, om toegang te geven, geeft u een lijst van de roltoewijzingen. Als u roltoewijzingen wilt aanbieden, gebruikt u een van de [roltoewijzingen -](/rest/api/authorization/roleassignments/list) REST-API's weergeven. Als u uw resultaten wilt verfijnen, geeft u een bereik en een optioneel filter op.

1. Begin met de volgende aanvraag:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. Vervang *{scope}* binnen de URI door het bereik waarvoor u de roltoewijzingen wilt weergeven.

    > [!div class="mx-tableFixed"]
    > | Bereik | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Beheergroep |
    > | `subscriptions/{subscriptionId1}` | Abonnement |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resourcegroep |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Resource |

    In het vorige voorbeeld is microsoft.web een resourceprovider die verwijst naar een app-service-exemplaar. U ook andere resourceproviders gebruiken en het bereik opgeven. Zie [Azure Resource providers en -typen en](../azure-resource-manager/management/resource-providers-and-types.md) ondersteunde Azure Resource [Manager-resourceproviderbewerkingen](resource-provider-operations.md)voor meer informatie.  
     
1. Vervang *{filter}* door de voorwaarde die u wilt toepassen om de lijst met roltoewijzingen te filteren.

    > [!div class="mx-tableFixed"]
    > | Filteren | Beschrijving |
    > | --- | --- |
    > | `$filter=atScope()` | Hiermee worden roltoewijzingen weergegeven voor alleen het opgegeven bereik, met geen roltoewijzingen bij subscopen. |
    > | `$filter=assignedTo('{objectId}')` | Hiermee worden roltoewijzingen voor een opgegeven gebruiker of serviceprincipal weergegeven.<br/>Als de gebruiker lid is van een groep met een roltoewijzing, wordt die roltoewijzing ook weergegeven. Dit filter is transitief voor groepen, wat betekent dat als de gebruiker lid is van een groep en die groep lid is van een andere groep die een roltoewijzing heeft, die roltoewijzing ook wordt vermeld.<br/>Dit filter accepteert alleen een object-id voor een gebruiker of een serviceprincipal. U een object-id voor een groep niet doorgeven. |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | Hiermee worden roltoewijzingen weergegeven voor de opgegeven gebruiker of serviceprincipal en op het opgegeven bereik. |
    > | `$filter=principalId+eq+'{objectId}'` | Hiermee worden roltoewijzingen voor een opgegeven gebruiker, groep of serviceprincipal weergegeven. |

## <a name="next-steps"></a>Volgende stappen

- [Roltoewijzingen toevoegen of verwijderen met Azure RBAC en de REST API](role-assignments-rest.md)
- [Azure REST API-verwijzing](/rest/api/azure/)
