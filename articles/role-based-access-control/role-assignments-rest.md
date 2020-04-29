---
title: Roltoewijzingen toevoegen of verwijderen met RBAC en de REST API
description: Meer informatie over het verlenen van toegang tot Azure-resources voor gebruikers, groepen, service-principals of beheerde identiteiten met behulp van op rollen gebaseerd toegangs beheer (RBAC) en de REST API van Azure.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80062998"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-the-rest-api"></a>Roltoewijzingen toevoegen of verwijderen met behulp van Azure RBAC en de REST API

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]In dit artikel wordt beschreven hoe u rollen toewijst met behulp van de REST API.

## <a name="prerequisites"></a>Vereisten

Om roltoewijzingen toe te voegen of te verwijderen, hebt u het volgende nodig:

- `Microsoft.Authorization/roleAssignments/write`en `Microsoft.Authorization/roleAssignments/delete` machtigingen, zoals beheerder of [eigenaar](built-in-roles.md#owner) van [gebruikers toegang](built-in-roles.md#user-access-administrator)

## <a name="add-a-role-assignment"></a>Een roltoewijzing toevoegen

Als u in RBAC toegang wilt verlenen, voegt u een roltoewijzing toe. Als u een roltoewijzing wilt toevoegen, gebruikt u de roltoewijzingen [-rest API maken](/rest/api/authorization/roleassignments/create) en geeft u de beveiligingsprincipal, roldefinitie en bereik op. U moet toegang hebben tot de `Microsoft.Authorization/roleAssignments/write` bewerking om deze API aan te roepen. Van de ingebouwde rollen wordt alleen de beheerder van de [eigenaar](built-in-roles.md#owner) en de [gebruiker toegang](built-in-roles.md#user-access-administrator) verleend tot deze bewerking.

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

    > [!div class="mx-tableFixed"]
    > | Bereik | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Beheergroep |
    > | `subscriptions/{subscriptionId1}` | Abonnement |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resourcegroep |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Resource |

    In het vorige voor beeld is micro soft. web een resource provider die verwijst naar een App Service-exemplaar. U kunt ook andere resource providers gebruiken en het bereik opgeven. Zie voor meer informatie [Azure-resource providers en-typen](../azure-resource-manager/management/resource-providers-and-types.md) en ondersteunde Azure Resource Manager van de [resource provider](resource-provider-operations.md).  

1. Vervang *{roleAssignmentName}* door de GUID-id van de roltoewijzing.

1. Vervang *{Scope}* in de hoofd tekst van de aanvraag door het bereik voor de roltoewijzing.

    > [!div class="mx-tableFixed"]
    > | Bereik | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Beheergroep |
    > | `subscriptions/{subscriptionId1}` | Abonnement |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resourcegroep |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Resource |

1. Vervang *{roledefinitionid hebben}* door de roldefinitie-id.

1. Vervang *{principalId}* door de object-id van de gebruiker, groep of service-principal die wordt toegewezen aan de rol.

## <a name="remove-a-role-assignment"></a>Roltoewijzing verwijderen

Als u in RBAC de toegang wilt intrekken voor een rol, verwijdert u de roltoewijzing. Als u een roltoewijzing wilt verwijderen, gebruikt u de [roltoewijzingen rest API verwijderen](/rest/api/authorization/roleassignments/delete) . U moet toegang hebben tot de `Microsoft.Authorization/roleAssignments/delete` bewerking om deze API aan te roepen. Van de ingebouwde rollen wordt alleen de beheerder van de [eigenaar](built-in-roles.md#owner) en de [gebruiker toegang](built-in-roles.md#user-access-administrator) verleend tot deze bewerking.

1. Haal de roltoewijzings-id (GUID) op. Deze id wordt geretourneerd wanneer u de roltoewijzing voor het eerst maakt of u kunt deze ophalen door de roltoewijzingen weer te geven.

1. Begin met de volgende aanvraag:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. Vervang *{Scope}* in de URI door het bereik voor het verwijderen van de roltoewijzing.

    > [!div class="mx-tableFixed"]
    > | Bereik | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Beheergroep |
    > | `subscriptions/{subscriptionId1}` | Abonnement |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resourcegroep |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Resource |

1. Vervang *{roleAssignmentName}* door de GUID-id van de roltoewijzing.

## <a name="next-steps"></a>Volgende stappen

- [Roltoewijzingen weer geven met behulp van Azure RBAC en de REST API](role-assignments-list-rest.md)
- [Resources implementeren met Resource Manager-sjablonen en Resource Manager REST API](../azure-resource-manager/templates/deploy-rest.md)
- [Naslag informatie over Azure REST API](/rest/api/azure/)
- [Aangepaste rollen maken voor Azure-resources met behulp van de REST API](custom-roles-rest.md)
