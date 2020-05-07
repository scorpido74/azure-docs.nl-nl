---
title: Azure-roltoewijzingen toevoegen of verwijderen met behulp van de REST API-Azure RBAC
description: Meer informatie over het verlenen van toegang tot Azure-resources voor gebruikers, groepen, service-principals of beheerde identiteiten met behulp van de REST API en Azure op rollen gebaseerd toegangs beheer (Azure RBAC).
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
ms.openlocfilehash: b73de61834a3ab20cab5e664ed307ad63e087608
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/03/2020
ms.locfileid: "82735637"
---
# <a name="add-or-remove-azure-role-assignments-using-the-rest-api"></a>Azure-roltoewijzingen toevoegen of verwijderen met behulp van de REST API

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]In dit artikel wordt beschreven hoe u rollen toewijst met behulp van de REST API.

## <a name="prerequisites"></a>Vereisten

Om roltoewijzingen toe te voegen of te verwijderen, hebt u het volgende nodig:

- `Microsoft.Authorization/roleAssignments/write`en `Microsoft.Authorization/roleAssignments/delete` machtigingen, zoals beheerder of [eigenaar](built-in-roles.md#owner) van [gebruikers toegang](built-in-roles.md#user-access-administrator)

## <a name="add-a-role-assignment"></a>Een roltoewijzing toevoegen

In azure RBAC kunt u een roltoewijzing toevoegen om toegang te verlenen. Als u een roltoewijzing wilt toevoegen, gebruikt u de roltoewijzingen [-rest API maken](/rest/api/authorization/roleassignments/create) en geeft u de beveiligingsprincipal, roldefinitie en bereik op. U moet toegang hebben tot de `Microsoft.Authorization/roleAssignments/write` bewerking om deze API aan te roepen. Van de ingebouwde rollen wordt alleen de beheerder van de [eigenaar](built-in-roles.md#owner) en de [gebruiker toegang](built-in-roles.md#user-access-administrator) verleend tot deze bewerking.

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

In azure RBAC kunt u een roltoewijzing verwijderen om de toegang te verwijderen. Als u een roltoewijzing wilt verwijderen, gebruikt u de [roltoewijzingen rest API verwijderen](/rest/api/authorization/roleassignments/delete) . U moet toegang hebben tot de `Microsoft.Authorization/roleAssignments/delete` bewerking om deze API aan te roepen. Van de ingebouwde rollen wordt alleen de beheerder van de [eigenaar](built-in-roles.md#owner) en de [gebruiker toegang](built-in-roles.md#user-access-administrator) verleend tot deze bewerking.

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

- [Azure-roltoewijzingen weer geven met behulp van de REST API](role-assignments-list-rest.md)
- [Resources implementeren met Resource Manager-sjablonen en Resource Manager REST API](../azure-resource-manager/templates/deploy-rest.md)
- [Naslag informatie over Azure REST API](/rest/api/azure/)
- [Aangepaste Azure-rollen maken of bijwerken met behulp van de REST API](custom-roles-rest.md)
