---
title: Weigeren toewijzingen voor Azure-resources weergeven met de REST API
description: Meer informatie over het weergeven van weigeringstoewijzingen voor gebruikers, groepen en toepassingen met behulp van RBAC (Role-based access control) voor Azure-resources en de REST API.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 0f648405a3d71bf27c64dacbb3fd78f3e9801137
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063018"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-rest-api"></a>Weigeren toewijzingen voor Azure-resources weergeven met behulp van de REST API

[Toewijzingen weigeren](deny-assignments.md) blokkeren gebruikers om specifieke Azure-bronacties uit te voeren, zelfs als een roltoewijzing hen toegang verleent. In dit artikel wordt beschreven hoe u toewijzingen voor weigeren weergeven met behulp van de REST API.

> [!NOTE]
> Je niet direct je eigen weigeringsopdrachten maken. Zie Toewijzingen weigeren voor informatie over het maken van [weigeringsopdrachten.](deny-assignments.md)

## <a name="prerequisites"></a>Vereisten

Als u informatie wilt krijgen over een weigeringsopdracht, moet u het:

- `Microsoft.Authorization/denyAssignments/read`machtigingen, die is opgenomen in de meeste [ingebouwde rollen voor Azure-resources.](built-in-roles.md)

## <a name="list-a-single-deny-assignment"></a>Een enkele weigeringstoewijzing weergeven

1. Begin met de volgende aanvraag:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments/{deny-assignment-id}?api-version=2018-07-01-preview
    ```

1. Vervang *{scope}* binnen de URI door het bereik waarvoor u de weigeringstoewijzingen wilt weergeven.

    > [!div class="mx-tableFixed"]
    > | Bereik | Type |
    > | --- | --- |
    > | `subscriptions/{subscriptionId}` | Abonnement |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resourcegroep |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Resource |

1. Vervang *{deny-assignment-id}* door de toewijzings-id weigeren die u wilt ophalen.

## <a name="list-multiple-deny-assignments"></a>Meerdere weigeringstoewijzingen weergeven

1. Begin met een van de volgende aanvragen:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview
    ```

    Met optionele parameters:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Vervang *{scope}* binnen de URI door het bereik waarvoor u de weigeringstoewijzingen wilt weergeven.

    > [!div class="mx-tableFixed"]
    > | Bereik | Type |
    > | --- | --- |
    > | `subscriptions/{subscriptionId}` | Abonnement |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resourcegroep |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Resource |

1. Vervang *{filter}* door de voorwaarde die u wilt toepassen om de lijst met toewijzing weigeren te filteren.

    > [!div class="mx-tableFixed"]
    > | Filteren | Beschrijving |
    > | --- | --- |
    > | (geen filter) | Hiermee worden alle weigeringstoewijzingen op, boven en onder het opgegeven bereik weergegeven. |
    > | `$filter=atScope()` | Lijsten weigeren toewijzingen voor alleen het opgegeven bereik en hoger. Omvat niet de weigeringstoewijzingen bij subscopen. |
    > | `$filter=assignedTo('{objectId}')` | Lijsten weigeren toewijzingen voor de opgegeven gebruiker of serviceprincipal.<br/>Als de gebruiker lid is van een groep met een weigeringstoewijzing, wordt die toewijzing voor weigeren ook weergegeven. Dit filter is transitief voor groepen, wat betekent dat als de gebruiker lid is van een groep en die groep lid is van een andere groep die een weigeringstoewijzing heeft, die toewijzing voor weigeren ook wordt vermeld.<br/>Dit filter accepteert alleen een object-id voor een gebruiker of een serviceprincipal. U een object-id voor een groep niet doorgeven. |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | Lijsten weigeren toewijzingen voor de opgegeven gebruiker of serviceprincipal en op het opgegeven bereik. |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | Lijsten weigeren toewijzingen met de opgegeven naam. |
    > | `$filter=principalId+eq+'{objectId}'` | Lijsten weigeren toewijzingen voor de opgegeven gebruiker, groep of serviceprincipal. |

## <a name="list-deny-assignments-at-the-root-scope-"></a>Toewijzingen weigeren aan het hoofdbereik (/)

1. Verhoog uw toegang zoals beschreven in [Elevate access for a Global Administrator in Azure Active Directory.](elevate-access-global-admin.md)

1. Gebruik de volgende aanvraag:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Vervang *{filter}* door de voorwaarde die u wilt toepassen om de lijst met toewijzing weigeren te filteren. Er is een filter nodig.

    > [!div class="mx-tableFixed"]
    > | Filteren | Beschrijving |
    > | --- | --- |
    > | `$filter=atScope()` | Lijst weigert toewijzingen voor alleen het hoofdbereik. Omvat niet de weigeringstoewijzingen bij subscopen. |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | Lijst weigeren toewijzingen met de opgegeven naam. |

1. Verhoogde toegang verwijderen.

## <a name="next-steps"></a>Volgende stappen

- [Inzicht in weigeringstoewijzingen voor Azure-resources](deny-assignments.md)
- [Toegangsbevoegdheid voor een globale beheerder verhogen in Azure Active Directory](elevate-access-global-admin.md)
- [Azure REST API-verwijzing](/rest/api/azure/)
