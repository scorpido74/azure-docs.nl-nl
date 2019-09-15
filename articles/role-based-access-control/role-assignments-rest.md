---
title: Toegang tot Azure-resources beheren met RBAC en de REST API-Azure | Microsoft Docs
description: Informatie over het beheren van toegang tot Azure-resources voor gebruikers, groepen en toepassingen met behulp van op rollen gebaseerd toegangs beheer (RBAC) en de REST API. U vindt hier instructies voor het weergeven van toegang, het verlenen van toegang en het intrekken van toegang.
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
ms.date: 09/11/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 86ee030e8c97cf3033b9d2d76b8125c64ecf8065
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996472"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-the-rest-api"></a>Toegang tot Azure-resources beheren met RBAC en de REST API

[Op rollen gebaseerd toegangsbeheer (RBAC)](overview.md) is de manier waarop u de toegang tot Azure-resources beheert. In dit artikel wordt beschreven hoe u de toegang beheert voor gebruikers, groepen en toepassingen met RBAC en de REST API.

## <a name="list-access"></a>Toegang opvragen

In RBAC kunt u de roltoewijzingen weer geven om toegang weer te geven. Als u roltoewijzingen wilt weer geven, gebruikt u een van de [roltoewijzingen-lijst rest-](/rest/api/authorization/roleassignments/list) api's. Als u uw resultaten wilt verfijnen, geeft u een bereik en een optioneel filter op.

1. Begin met de volgende aanvraag:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. Vervang *{Scope}* in de URI door het bereik waarvoor u de roltoewijzingen wilt weer geven.

    | Scope | type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Beheergroep |
    | `subscriptions/{subscriptionId1}` | Subscription |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

    In het vorige voor beeld is micro soft. web een resource provider die verwijst naar een App Service-exemplaar. U kunt ook andere resource providers gebruiken en het bereik opgeven. Zie voor meer informatie [Azure-resource providers en-typen](../azure-resource-manager/resource-manager-supported-services.md) en ondersteunde Azure Resource Manager van de [resource provider](resource-provider-operations.md).  
     
1. Vervang *{filter}* door de voor waarde die u wilt Toep assen om de roltoewijzings lijst te filteren.

    | Filteren | Description |
    | --- | --- |
    | `$filter=atScope()` | Hier worden roltoewijzingen voor alleen het opgegeven bereik weer gegeven, met inbegrip van de roltoewijzingen in subbereiken. |
    | `$filter=principalId%20eq%20'{objectId}'` | Hier worden roltoewijzingen voor een opgegeven gebruiker, groep of Service-Principal weer gegeven. |
    | `$filter=assignedTo('{objectId}')` | Hier worden roltoewijzingen voor een opgegeven gebruiker of Service-Principal weer gegeven. Als de gebruiker lid is van een groep die een roltoewijzing heeft, wordt die roltoewijzing ook weer gegeven. Dit filter is transitief voor groepen. Dit betekent dat als de gebruiker lid is van een groep en die groep lid is van een andere groep die een roltoewijzing heeft, die roltoewijzing ook wordt vermeld. Dit filter accepteert alleen een object-ID voor een gebruiker of een service-principal. U kunt geen object-ID door geven voor een groep. |

## <a name="grant-access"></a>Toegang verlenen

In RBAC verleent u toegang door een roltoewijzing te maken. Als u een roltoewijzing wilt maken, gebruikt u de roltoewijzingen [-rest API maken](/rest/api/authorization/roleassignments/create) en geeft u de beveiligingsprincipal, roldefinitie en bereik op. U moet toegang hebben tot de `Microsoft.Authorization/roleAssignments/write` bewerking om deze API aan te roepen. Van de ingebouwde rollen wordt alleen de beheerder van de [eigenaar](built-in-roles.md#owner) en de [gebruiker toegang](built-in-roles.md#user-access-administrator) verleend tot deze bewerking.

1. Gebruik de [roldefinities-lijst](/rest/api/authorization/roledefinitions/list) rest API of Zie [ingebouwde rollen](built-in-roles.md) om de id op te halen voor de roldefinitie die u wilt toewijzen.

1. Gebruik een GUID-hulp programma voor het genereren van een unieke id die wordt gebruikt voor de toewijzings-id van de rol. De id heeft de volgende indeling:`00000000-0000-0000-0000-000000000000`

1. Beginnen met de volgende aanvraag en hoofd tekst:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

    ```json
    {
      "properties": {
        "roleDefinitionId": "/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}"
      }
    }
    ```

1. Vervang *{Scope}* in de URI door het bereik voor de roltoewijzing.

    | Scope | type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Beheergroep |
    | `subscriptions/{subscriptionId1}` | Subscription |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Resource |

1. Vervang *{roleAssignmentName}* door de GUID-id van de roltoewijzing.

1. Vervang *{Scope}* in de hoofd tekst van de aanvraag door het bereik voor de roltoewijzing.

    | Scope | type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Beheergroep |
    | `subscriptions/{subscriptionId1}` | Subscription |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Resource |

1. Vervang *{roledefinitionid hebben}* door de roldefinitie-id.

1. Vervang *{principalId}* door de object-id van de gebruiker, groep of service-principal die wordt toegewezen aan de rol.

## <a name="remove-access"></a>Remove access

Als u in RBAC de toegang wilt intrekken voor een rol, verwijdert u de roltoewijzing. Als u een roltoewijzing wilt verwijderen, gebruikt u de [roltoewijzingen rest API verwijderen](/rest/api/authorization/roleassignments/delete) . U moet toegang hebben tot de `Microsoft.Authorization/roleAssignments/delete` bewerking om deze API aan te roepen. Van de ingebouwde rollen wordt alleen de beheerder van de [eigenaar](built-in-roles.md#owner) en de [gebruiker toegang](built-in-roles.md#user-access-administrator) verleend tot deze bewerking.

1. Haal de roltoewijzings-id (GUID) op. Deze id wordt geretourneerd wanneer u de roltoewijzing voor het eerst maakt of u kunt deze ophalen door de roltoewijzingen weer te geven.

1. Begin met de volgende aanvraag:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. Vervang *{Scope}* in de URI door het bereik voor het verwijderen van de roltoewijzing.

    | Scope | type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Beheergroep |
    | `subscriptions/{subscriptionId1}` | Subscription |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Resource |

1. Vervang *{roleAssignmentName}* door de GUID-id van de roltoewijzing.

## <a name="next-steps"></a>Volgende stappen

- [Resources implementeren met Resource Manager-sjablonen en Resource Manager REST API](../azure-resource-manager/resource-group-template-deploy-rest.md)
- [Azure REST API-naslaginformatie](/rest/api/azure/)
- [Aangepaste rollen maken voor Azure-resources met behulp van de REST API](custom-roles-rest.md)
