---
title: Een lijst met de REST API voor het weigeren van toewijzingen voor Azure-resources
description: Meer informatie over het weigeren van toewijzingen voor gebruikers, groepen en toepassingen die gebruikmaken van op rollen gebaseerd toegangs beheer (RBAC) voor Azure-resources en de REST API.
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
ms.date: 06/10/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 9e6214b3cb2cdca2d80ebae43771b206e3396d8b
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137326"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-rest-api"></a>Toewijzing van toewijzingen voor Azure-resources met behulp van de REST API weer geven

[Toewijzingen weigeren](deny-assignments.md) blok keren dat gebruikers specifieke Azure-resource acties kunnen uitvoeren, zelfs als een roltoewijzing deze toegang verleent. In dit artikel wordt beschreven hoe u toewijzingen voor weigeren kunt weer geven met behulp van de REST API.

> [!NOTE]
> U kunt niet rechtstreeks uw eigen weigerings toewijzingen maken. Zie [toewijzingen weigeren](deny-assignments.md)voor meer informatie over hoe weigerings toewijzingen worden gemaakt.

## <a name="prerequisites"></a>Vereisten

Als u informatie wilt ophalen over een weiger toewijzing, hebt u het volgende nodig:

- `Microsoft.Authorization/denyAssignments/read` machtiging, die is opgenomen in de meeste [ingebouwde rollen voor Azure-resources](built-in-roles.md).

## <a name="list-a-single-deny-assignment"></a>Een enkele weigerings toewijzing weer geven

1. Begin met de volgende aanvraag:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments/{deny-assignment-id}?api-version=2018-07-01-preview
    ```

1. Vervang *{Scope}* in de URI door het bereik waarvoor u de weigerings toewijzingen wilt weer geven.

    | Bereik | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Abonnement |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resourcegroep |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

1. Vervang *{deny-Assignment-id}* door de id van de weigerings toewijzing die u wilt ophalen.

## <a name="list-multiple-deny-assignments"></a>Meerdere weigerings toewijzingen weer geven

1. Begin met een van de volgende aanvragen:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview
    ```

    Met optionele para meters:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Vervang *{Scope}* in de URI door het bereik waarvoor u de weigerings toewijzingen wilt weer geven.

    | Bereik | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Abonnement |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resourcegroep |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

1. Vervang *{filter}* door de voor waarde die u wilt Toep assen om de lijst met geweigerde toewijzingen te filteren.

    | Filteren | Beschrijving |
    | --- | --- |
    | (geen filter) | Een lijst met alle weigerings toewijzingen op hierboven en onder het opgegeven bereik. |
    | `$filter=atScope()` | Alleen toewijzingen voor het opgegeven bereik en de bovenstaande lijst weigeren. Omvat niet de weigerings toewijzingen in subbereiken. |
    | `$filter=denyAssignmentName%20eq%20'{deny-assignment-name}'` | Weiger toewijzingen met de opgegeven naam. |

## <a name="list-deny-assignments-at-the-root-scope-"></a>Lijst met geweigerde toewijzingen in het hoofd bereik (/)

1. Breid uw toegang uit, zoals wordt beschreven in [toegang verhogen voor een globale beheerder in azure Active Directory](elevate-access-global-admin.md).

1. Gebruik de volgende aanvraag:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Vervang *{filter}* door de voor waarde die u wilt Toep assen om de lijst met geweigerde toewijzingen te filteren. U moet een filter opgeven.

    | Filteren | Beschrijving |
    | --- | --- |
    | `$filter=atScope()` | Lijst weigeren alleen toewijzingen voor het hoofd bereik. Omvat niet de weigerings toewijzingen in subbereiken. |
    | `$filter=denyAssignmentName%20eq%20'{deny-assignment-name}'` | Weiger toewijzingen met de opgegeven naam. |

1. Verwijder verhoogde toegang.

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over het weigeren van toewijzingen voor Azure-resources](deny-assignments.md)
- [Toegangsbevoegdheid voor een globale beheerder verhogen in Azure Active Directory](elevate-access-global-admin.md)
- [Azure REST API-naslaginformatie](/rest/api/azure/)
