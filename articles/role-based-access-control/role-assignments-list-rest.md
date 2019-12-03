---
title: Roltoewijzingen weer geven met behulp van Azure RBAC en de REST API
description: Meer informatie over hoe u kunt bepalen welke resources gebruikers, groepen, service-principals of beheerde identiteiten hebben toegang tot het gebruik van op rollen gebaseerd toegangs beheer (RBAC) en de REST API.
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
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: e20edcb5e2406c216711a2e0f696ef06e19fe21e
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74710397"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-rest-api"></a>Roltoewijzingen weer geven met behulp van Azure RBAC en de REST API

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] in dit artikel wordt beschreven hoe u roltoewijzingen kunt weer geven met behulp van de REST API.

## <a name="list-role-assignments"></a>Roltoewijzingen weer geven

In RBAC kunt u de roltoewijzingen weer geven om toegang weer te geven. Als u roltoewijzingen wilt weer geven, gebruikt u een van de [roltoewijzingen-lijst rest-](/rest/api/authorization/roleassignments/list) api's. Als u uw resultaten wilt verfijnen, geeft u een bereik en een optioneel filter op.

1. Begin met de volgende aanvraag:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. Vervang *{Scope}* in de URI door het bereik waarvoor u de roltoewijzingen wilt weer geven.

    | Scope | Type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Beheergroep |
    | `subscriptions/{subscriptionId1}` | Abonnement |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resourcegroep |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Bron |

    In het vorige voor beeld is micro soft. web een resource provider die verwijst naar een App Service-exemplaar. U kunt ook andere resource providers gebruiken en het bereik opgeven. Zie voor meer informatie [Azure-resource providers en-typen](../azure-resource-manager/resource-manager-supported-services.md) en ondersteunde Azure Resource Manager van de [resource provider](resource-provider-operations.md).  
     
1. Vervang *{filter}* door de voor waarde die u wilt Toep assen om de roltoewijzings lijst te filteren.

    | Filter | Beschrijving |
    | --- | --- |
    | `$filter=atScope()` | Hier worden roltoewijzingen voor alleen het opgegeven bereik weer gegeven, met inbegrip van de roltoewijzingen in subbereiken. |
    | `$filter=principalId%20eq%20'{objectId}'` | Hier worden roltoewijzingen voor een opgegeven gebruiker, groep of Service-Principal weer gegeven. |
    | `$filter=assignedTo('{objectId}')` | Hier worden roltoewijzingen voor een opgegeven gebruiker of Service-Principal weer gegeven. Als de gebruiker lid is van een groep die een roltoewijzing heeft, wordt die roltoewijzing ook weer gegeven. Dit filter is transitief voor groepen. Dit betekent dat als de gebruiker lid is van een groep en die groep lid is van een andere groep die een roltoewijzing heeft, die roltoewijzing ook wordt vermeld. Dit filter accepteert alleen een object-ID voor een gebruiker of een service-principal. U kunt geen object-ID door geven voor een groep. |

## <a name="next-steps"></a>Volgende stappen

- [Roltoewijzingen toevoegen of verwijderen met behulp van Azure RBAC en de REST API](role-assignments-rest.md)
- [Azure REST API-naslaginformatie](/rest/api/azure/)
