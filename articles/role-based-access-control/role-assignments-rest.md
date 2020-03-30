---
title: Roltoewijzingen toevoegen of verwijderen met RBAC en de REST API
description: Meer informatie over het verlenen van toegang tot Azure-bronnen voor gebruikers, groepen, serviceprincipals of beheerde identiteiten met Behulp van RBAC (Azure Role-based Access Control) en de REST API.
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
ms.openlocfilehash: 9beda6589c03f1b14fc9756af86a9ce0711894c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062998"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-the-rest-api"></a>Roltoewijzingen toevoegen of verwijderen met Azure RBAC en de REST API

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]In dit artikel wordt beschreven hoe u rollen toewijzen met behulp van de REST API.

## <a name="prerequisites"></a>Vereisten

Als u roltoewijzingen wilt toevoegen of verwijderen, moet u het als ander beschikken:

- `Microsoft.Authorization/roleAssignments/write`en `Microsoft.Authorization/roleAssignments/delete` machtigingen, zoals [beheerder van gebruikerstoegang](built-in-roles.md#user-access-administrator) of [eigenaar](built-in-roles.md#owner)

## <a name="add-a-role-assignment"></a>Een roltoewijzing toevoegen

In RBAC voegt u een roltoewijzing toe om toegang te verlenen om toegang te verlenen. Als u een roltoewijzing wilt toevoegen, gebruikt u de [roltoewijzingen - REST-API maken](/rest/api/authorization/roleassignments/create) en geeft u de beveiligingsprincipal, roldefinitie en het bereik op. Als u deze API wilt aanroepen, moet u toegang hebben tot de `Microsoft.Authorization/roleAssignments/write` bewerking. Van de ingebouwde rollen krijgen alleen [eigenaar-](built-in-roles.md#owner) en [gebruikerstoegangsbeheerder](built-in-roles.md#user-access-administrator) toegang tot deze bewerking.

1. Gebruik de [functiedefinities - Lijst](/rest/api/authorization/roledefinitions/list) REST API of zie [Ingebouwde rollen](built-in-roles.md) om de id te krijgen voor de roldefinitie die u wilt toewijzen.

1. Gebruik een GUID-tool om een unieke id te genereren die wordt gebruikt voor de functie-id van de toewijzing van rollen. De id heeft de indeling:`00000000-0000-0000-0000-000000000000`

1. Begin met de volgende aanvraag en instantie:

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

1. Vervang *{scope}* binnen de URI door de ruimte voor de roltoewijzing.

    > [!div class="mx-tableFixed"]
    > | Bereik | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Beheergroep |
    > | `subscriptions/{subscriptionId1}` | Abonnement |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resourcegroep |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Resource |

    In het vorige voorbeeld is microsoft.web een resourceprovider die verwijst naar een app-service-exemplaar. U ook andere resourceproviders gebruiken en het bereik opgeven. Zie [Azure Resource providers en -typen en](../azure-resource-manager/management/resource-providers-and-types.md) ondersteunde Azure Resource [Manager-resourceproviderbewerkingen](resource-provider-operations.md)voor meer informatie.  

1. Vervang *{roleAssignmentName}* door de GUID-id van de roltoewijzing.

1. Vervang *{scope}* binnen de aanvraaginstantie door de ruimte voor de roltoewijzing.

    > [!div class="mx-tableFixed"]
    > | Bereik | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Beheergroep |
    > | `subscriptions/{subscriptionId1}` | Abonnement |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resourcegroep |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Resource |

1. Vervang *{roleDefinitionId}* door de functiedefinitie-id.

1. Vervang *{principalId}* door de object-id van de gebruiker, groep of serviceprincipal die de rol krijgt toegewezen.

## <a name="remove-a-role-assignment"></a>Roltoewijzing verwijderen

Als u in RBAC de toegang wilt intrekken voor een rol, verwijdert u de roltoewijzing. Als u een roltoewijzing wilt verwijderen, gebruikt u de [API Voor roltoewijzingen - REST-API verwijderen.](/rest/api/authorization/roleassignments/delete) Als u deze API wilt aanroepen, moet u toegang hebben tot de `Microsoft.Authorization/roleAssignments/delete` bewerking. Van de ingebouwde rollen krijgen alleen [eigenaar-](built-in-roles.md#owner) en [gebruikerstoegangsbeheerder](built-in-roles.md#user-access-administrator) toegang tot deze bewerking.

1. De GUID (Role Assignment Identifier) opteerd. Deze id wordt geretourneerd wanneer u de roltoewijzing voor het eerst maakt of u deze krijgen door de roltoewijzingen op te sommen.

1. Begin met de volgende aanvraag:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. Vervang *{scope}* binnen de URI door de ruimte om de roltoewijzing te verwijderen.

    > [!div class="mx-tableFixed"]
    > | Bereik | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Beheergroep |
    > | `subscriptions/{subscriptionId1}` | Abonnement |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resourcegroep |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Resource |

1. Vervang *{roleAssignmentName}* door de GUID-id van de roltoewijzing.

## <a name="next-steps"></a>Volgende stappen

- [Roltoewijzingen weergeven met Azure RBAC en de REST API](role-assignments-list-rest.md)
- [Resources implementeren met Resource Manager-sjablonen en Resource Manager REST API](../azure-resource-manager/templates/deploy-rest.md)
- [Azure REST API-verwijzing](/rest/api/azure/)
- [Aangepaste rollen voor Azure-resources maken met de REST-API](custom-roles-rest.md)
