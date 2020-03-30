---
title: Azure AD-beheerrollen toewijzen met Microsoft Graph API | Microsoft Documenten
description: Azure AD-beheerdersrollen toewijzen en verwijderen met Graph API in Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3632f8a360df8837569104232b7380fdc8383953
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77559144"
---
# <a name="assign-custom-admin-roles-using-the-microsoft-graph-api-in-azure-active-directory"></a>Aangepaste beheerdersrollen toewijzen met de Microsoft Graph-API in Azure Active Directory 

U automatiseren hoe u rollen toewijst aan gebruikersaccounts met behulp van de Microsoft Graph API. Dit artikel behandelt bewerkingen post, get en delete voor rollenToewijzingen.

## <a name="required-permissions"></a>Vereiste machtigingen

Maak verbinding met uw Azure AD-tenant via een globale beheerdersaccount of een beheerder van een geprivilegieerde identiteit om rollen toe te wijzen of te verwijderen.

## <a name="post-operations-on-roleassignment"></a>POST-bewerkingen op roleassignment

HTTP-aanvraag om een roltoewijzing tussen een gebruiker en een roldefinitie te maken.

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
    "resourceScopes":["/"]
}
```

Antwoord

``` HTTP
HTTP/1.1 201 Created
```

HTTP-verzoek om een roltoewijzing te maken waarbij de hoofd- of roldefinitie niet bestaat

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Hoofdtekst

``` HTTP
{
    "principalId":" 2142743c-a5b3-4983-8486-4532ccba12869",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "resourceScopes":["/"]
}
```

Antwoord

``` HTTP
HTTP/1.1 404 Not Found
```

HTTP-aanvraag om één roltoewijzing met een resourcebereik te maken op een ingebouwde roldefinitie.

> [!NOTE] 
> Ingebouwde rollen hebben vandaag de dag een beperking waarbij ze alleen kunnen worden ondergebracht bij het bereik "/" van de organisatie of het bereik "/AU/*". Single resource scoping werkt niet voor ingebouwde rollen, maar werkt voor aangepaste rollen.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Hoofdtekst

``` HTTP
{
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "resourceScopes":["/ab2e1023-bddc-4038-9ac1-ad4843e7e539"]
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

## <a name="get-operations-on-roleassignment"></a>GET-bewerkingen op roleassignment

HTTP-verzoek om een roltoewijzing voor een bepaalde opdrachtgever te krijgen

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
    "resourceScopes":["/"]
} ,
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":["/"]
}
```

HTTP-aanvraag om een roltoewijzing voor een bepaalde roldefinitie te krijgen.

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
    "resourceScopes":["/"]
}
```

HTTP-aanvraag om een roltoewijzing per id te krijgen.

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
    "resourceScopes":["/"]
}
```

## <a name="delete-operations-on-roleassignment"></a>Bewerkingen verwijderen op roltoewijzing

HTTP-verzoek om een roltoewijzing tussen een gebruiker en een roldefinitie te verwijderen.

DELETE

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Antwoord
``` HTTP
HTTP/1.1 204 No Content
```

HTTP-verzoek om een roltoewijzing te verwijderen die niet meer bestaat

DELETE

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Antwoord

``` HTTP
HTTP/1.1 404 Not Found
```

HTTP-verzoek om een roltoewijzing tussen zelf- en ingebouwde roldefinitie te verwijderen

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

* Voel je vrij om met ons te delen op het [Azure AD-forum voor beheerfuncties](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Zie [Beheerdersrollen toewijzen](directory-assign-admin-roles.md)voor meer informatie over rollen en toewijzing van beheerders.
* Zie een vergelijking van [standaardmachtigingen voor gasten en leden](../fundamentals/users-default-permissions.md)voor standaardgebruikersmachtigingen voor standaardgebruikers.
