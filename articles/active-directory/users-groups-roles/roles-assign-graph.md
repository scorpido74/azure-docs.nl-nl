---
title: Azure AD-beheerders rollen toewijzen met Microsoft Graph-API | Microsoft Docs
description: Azure AD-beheerders rollen toewijzen en verwijderen met Graph API in Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/29/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ac6f06a936893f26c634951e50a3579401c15d5
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83869607"
---
# <a name="assign-custom-admin-roles-using-the-microsoft-graph-api-in-azure-active-directory"></a>Wijs aangepaste beheerders rollen toe met behulp van de Microsoft Graph-API in Azure Active Directory 

U kunt automatiseren hoe u rollen aan gebruikers accounts toewijst met behulp van de Microsoft Graph-API. Dit artikel behandelt POST-, GET-en DELETE-bewerkingen op roleAssignments.

## <a name="required-permissions"></a>Vereiste machtigingen

Maak verbinding met uw Azure AD-organisatie met behulp van een algemeen beheerders account of een bevoegde identiteits beheerder om rollen toe te wijzen of te verwijderen.

## <a name="post-operations-on-roleassignment"></a>POST-bewerkingen op RoleAssignment

HTTP-aanvraag voor het maken van een roltoewijzing tussen een gebruiker en een functie definitie.

POST

``` HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
Content-type: application/json
```

Hoofdtekst

``` HTTP
{
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "resourceScopes":"/"
}
```

Antwoord

``` HTTP
HTTP/1.1 201 Created
```

HTTP-aanvraag voor het maken van een roltoewijzing waarbij de principal of roldefinitie niet bestaat

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Hoofdtekst

``` HTTP
{
    "principalId":" 2142743c-a5b3-4983-8486-4532ccba12869",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "resourceScopes":"/"
}
```

Antwoord

``` HTTP
HTTP/1.1 404 Not Found
```

HTTP-aanvraag voor het maken van een functie toewijzing met één resource bereik voor een ingebouwde roldefinitie.

> [!NOTE] 
> Voor ingebouwde rollen geldt een beperking waarbij deze alleen kan worden beperkt tot het bereik '/' organisatie-breed of het bereik '/AU/* '. Het bereik van één resource werkt niet voor ingebouwde rollen, maar werkt voor aangepaste rollen.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Hoofdtekst

``` HTTP
{
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "resourceScopes":"/ab2e1023-bddc-4038-9ac1-ad4843e7e539"
}
```

Antwoord

``` HTTP
HTTP/1.1 400 Bad Request
{
    "odata.error":
    {
        "code":"Request_BadRequest",
        "message":
        {
            "lang":"en",
            "value":"Provided authorization scope is not supported for built-in role definitions."},
            "values":
            [
                {
                    "item":"scope",
                    "value":"/ab2e1023-bddc-4038-9ac1-ad4843e7e539"
                }
            ]
        }
    }
}
```

## <a name="get-operations-on-roleassignment"></a>GET-bewerkingen op RoleAssignment

HTTP-aanvraag voor het ophalen van een roltoewijzing voor een bepaalde principal

GET

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments&$filter=principalId eq ‘<object-id-of-principal>’
```

Antwoord

``` HTTP
HTTP/1.1 200 OK
{ 
    "id":"mhxJMipY4UanIzy2yE-r7JIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "resourceScopes":"/"
} ,
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":"/"
}
```

HTTP-aanvraag voor het ophalen van een roltoewijzing voor een bepaalde roldefinitie.

GET

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments&$filter=roleDefinitionId eq ‘<object-id-or-template-id-of-role-definition>’
```

Antwoord

``` HTTP
HTTP/1.1 200 OK
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":"/"
}
```

HTTP-aanvraag voor het ophalen van een roltoewijzing per ID.

GET

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Antwoord

``` HTTP
HTTP/1.1 200 OK
{ 
    "id":"mhxJMipY4UanIzy2yE-r7JIiSDKQoTVJrLE9etXyrY0-1",
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "resourceScopes":"/"
}
```

## <a name="delete-operations-on-roleassignment"></a>Verwijder bewerkingen op RoleAssignment

HTTP-aanvraag voor het verwijderen van een roltoewijzing tussen een gebruiker en een roldefinitie.

DELETE

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Antwoord
``` HTTP
HTTP/1.1 204 No Content
```

HTTP-aanvraag voor het verwijderen van een roltoewijzing die niet meer bestaat

DELETE

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Antwoord

``` HTTP
HTTP/1.1 404 Not Found
```

HTTP-aanvraag voor het verwijderen van een roltoewijzing tussen de definitie zelf en de ingebouwde rol

DELETE

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Antwoord

``` HTTP
HTTP/1.1 400 Bad Request
{
    "odata.error":
    {
        "code":"Request_BadRequest",
        "message":
        {
            "lang":"en",
            "value":"Cannot remove self from built-in role definitions."},
            "values":null
        }
    }
}
```

## <a name="next-steps"></a>Volgende stappen

* U kunt graag delen met ons op het [forum van Azure AD-beheerders](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Zie [beheerders rollen toewijzen](directory-assign-admin-roles.md)voor meer informatie over functies en de toewijzing van beheerdersrol.
* Zie voor standaard gebruikers machtigingen een [vergelijking van de standaard machtigingen voor gast-en gebruikers rechten](../fundamentals/users-default-permissions.md).
