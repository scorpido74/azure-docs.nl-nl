---
title: Toegang verhogen om alle Azure-abonnementen en-beheer groepen te beheren | Microsoft Docs
description: Hierin wordt beschreven hoe u de toegang tot een globale beheerder uitbreidt om alle abonnementen en beheer groepen te beheren in Azure Active Directory met behulp van de Azure Portal of REST API.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: bagovind
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/17/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 2fec017f80758dbcf2a155c3535b9a3e028e4bd9
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/18/2019
ms.locfileid: "72592697"
---
# <a name="elevate-access-to-manage-all-azure-subscriptions-and-management-groups"></a>Toegang verhogen om alle Azure-abonnementen en-beheer groepen te beheren

Als globale beheerder in Azure Active Directory (Azure AD) hebt u mogelijk geen toegang tot alle abonnementen en beheer groepen in uw Directory. In dit artikel worden de manieren beschreven waarop u de toegang tot alle abonnementen en beheer groepen kunt verhogen.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="why-would-you-need-to-elevate-your-access"></a>Waarom moet u uw toegang verhogen?

Als u een globale beheerder bent, is het mogelijk dat u het volgende wilt doen:

- Opnieuw toegang krijgen tot een Azure-abonnement of-beheer groep wanneer een gebruiker de toegang kwijtraakt
- Een andere gebruiker of uzelf toegang verlenen tot een Azure-abonnement of beheer groep
- Alle Azure-abonnementen of-beheer groepen in een organisatie weer geven
- Een Automation-app (zoals een facturerings-of controle-app) toestaan om toegang te krijgen tot alle Azure-abonnementen of-beheer groepen

## <a name="how-does-elevate-access-work"></a>Hoe werkt de toegang met verhoogde bevoegdheden?

Azure AD en Azure-resources worden onafhankelijk van elkaar beveiligd. Dat wil zeggen dat Azure AD-roltoewijzingen geen toegang verlenen tot Azure-resources en Azure-roltoewijzingen geen toegang verlenen tot Azure AD. Als u echter een [globale beheerder](../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator-permissions) in azure AD bent, kunt u uzelf toegang tot alle Azure-abonnementen en-beheer groepen in uw directory toewijzen. Gebruik deze mogelijkheid als u geen toegang hebt tot Azure-abonnements resources, zoals virtuele machines of opslag accounts, en u uw globale beheerders bevoegdheden wilt gebruiken om toegang te krijgen tot deze resources.

Wanneer u de toegang uitbreidt, krijgt u de rol [beheerder voor gebruikers toegang](built-in-roles.md#user-access-administrator) in Azure in het hoofd bereik (`/`) toegewezen. Hiermee kunt u alle resources weer geven en toegang toewijzen in een abonnement of beheer groep in de Directory. De toewijzingen van beheerders rollen voor gebruikers toegang kunnen worden verwijderd met behulp van Power shell.

U moet deze verhoogde toegang verwijderen nadat u de wijzigingen hebt aangebracht die u moet aanbrengen in het hoofd bereik.

![Toegang verhogen](./media/elevate-access-global-admin/elevate-access.png)

## <a name="azure-portal"></a>Azure Portal

Volg deze stappen om de toegang tot een globale beheerder te verhogen met behulp van de Azure Portal.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) of het [beheer centrum van Azure Active Directory](https://aad.portal.azure.com) als globale beheerder.

1. Klik in de navigatie lijst op **Azure Active Directory** en klik vervolgens op **Eigenschappen**.

   ![Eigenschappen van Azure AD-scherm opname](./media/elevate-access-global-admin/aad-properties.png)

1. Stel onder **toegangs beheer voor Azure-resources**de schakel optie in op **Ja**.

   ![Toegangs beheer voor Azure-resources-scherm afbeelding](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   Wanneer u de wissel knop instelt op **Ja**, wordt de rol gebruikers toegangs beheerder toegewezen in azure RBAC in het hoofd bereik (/). Hiermee verleent u toestemming om rollen toe te wijzen aan alle Azure-abonnementen en-beheer groepen die zijn gekoppeld aan deze Azure AD-Directory. Deze schakel optie is alleen beschikbaar voor gebruikers aan wie de rol van globale beheerder is toegewezen in azure AD.

   Wanneer u de wissel knop op **Nee**instelt, wordt de rol beheerder voor gebruikers toegang in azure RBAC verwijderd uit uw gebruikers account. U kunt geen rollen meer toewijzen in alle Azure-abonnementen en-beheer groepen die zijn gekoppeld aan deze Azure AD-Directory. U kunt alleen de Azure-abonnementen en-beheer groepen weer geven en beheren waartoe u toegang hebt gekregen.

    > [!NOTE]
    > Als u [Azure AD privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-configure.md)gebruikt, is het deactiveren van de roltoewijzing niet van invloed op de wissel knop op **Nee**. Als u de minimale privileged Access wilt behouden, raden we u aan deze wissel knop in te stellen op **Nee** voordat u de roltoewijzing deactiveert.
    
1. Klik op **Opslaan** om de instelling op te slaan.

   Deze instelling is geen algemene eigenschap en is alleen van toepassing op de momenteel aangemelde gebruiker. U kunt de toegang niet verhogen voor alle leden van de rol globale beheerder.

1. Meld u af en meld u opnieuw aan om uw toegang te vernieuwen.

    U hebt nu toegang tot alle abonnementen en beheer groepen in uw Directory. U zult merken dat u de rol gebruikers toegang beheerder hebt toegewezen in het hoofd bereik.

   ![Abonnements roltoewijzingen met het hoofd bereik-scherm afbeelding](./media/elevate-access-global-admin/iam-root.png)

1. Breng de gewenste wijzigingen aan in de verhoogde toegang.

    Zie [toegang beheren met RBAC en de Azure Portal](role-assignments-portal.md)voor meer informatie over het toewijzen van rollen. Als u Azure AD Privileged Identity Management (PIM) gebruikt, raadpleegt u [Azure-resources detecteren om te beheren in PIM](../active-directory/privileged-identity-management/pim-resource-roles-discover-resources.md) of [Azure-resource rollen toewijzen in PIM](../active-directory/privileged-identity-management/pim-resource-roles-assign-roles.md).

1. Wanneer u klaar bent, stelt u het **toegangs beheer voor Azure-resources** in op **Nee**. Aangezien dit een instelling per gebruiker is, moet u zijn aangemeld als dezelfde gebruiker als waarmee de toegang wordt verhoogd.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

### <a name="list-role-assignment-at-the-root-scope-"></a>Roltoewijzing in het hoofd bereik weer geven (/)

Gebruik de opdracht [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) om de roltoewijzing van de gebruikers toegang voor een gebruiker in het hoofd bereik (`/`) weer te geven.

```azurepowershell
Get-AzRoleAssignment | where {$_.RoleDefinitionName -eq "User Access Administrator" `
  -and $_.SignInName -eq "<username@example.com>" -and $_.Scope -eq "/"}
```

```Example
RoleAssignmentId   : /providers/Microsoft.Authorization/roleAssignments/098d572e-c1e5-43ee-84ce-8dc459c7e1f0
Scope              : /
DisplayName        : username
SignInName         : username@example.com
RoleDefinitionName : User Access Administrator
RoleDefinitionId   : 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9
ObjectId           : d65fd0e9-c185-472c-8f26-1dafa01f72cc
ObjectType         : User
CanDelegate        : False
```

### <a name="remove-a-role-assignment-at-the-root-scope-"></a>Een roltoewijzing verwijderen uit het hoofd bereik (/)

Ga als volgt te werk om een toewijzing van de rol beheerder van gebruikers toegang voor een gebruiker in het hoofd bereik (`/`) te verwijderen.

1. Meld u aan als een gebruiker die verhoogde toegang kan verwijderen. Dit kan dezelfde gebruiker zijn die is gebruikt voor het verhogen van de toegang of een andere globale beheerder met verhoogde toegang in het hoofd bereik.


1. Gebruik de opdracht [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) voor het verwijderen van de toewijzing van de rol beheerder van gebruikers toegang.

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName <username@example.com> `
      -RoleDefinitionName "User Access Administrator" -Scope "/"
    ```

## <a name="rest-api"></a>REST API

### <a name="elevate-access-for-a-global-administrator"></a>Toegang verhogen voor een globale beheerder

Gebruik de volgende basis stappen om de toegang tot een globale beheerder te verhogen met behulp van de REST API.

1. Met behulp van REST, Call `elevateAccess`, waarmee u de rol van beheerder voor gebruikers toegang in het hoofd bereik (`/`) verleent.

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. Maak een [roltoewijzing](/rest/api/authorization/roleassignments) om een wille keurige rol aan elk bereik toe te wijzen. In het volgende voor beeld ziet u de eigenschappen voor het toewijzen van de rol {Roledefinitionid hebben} aan het hoofd bereik (`/`):

   ```json
   { 
     "properties": {
       "roleDefinitionId": "providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionID}",
       "principalId": "{objectID}",
       "scope": "/"
     },
     "id": "providers/Microsoft.Authorization/roleAssignments/64736CA0-56D7-4A94-A551-973C2FE7888B",
     "type": "Microsoft.Authorization/roleAssignments",
     "name": "64736CA0-56D7-4A94-A551-973C2FE7888B"
   }
   ```

1. Hoewel de beheerder van een gebruiker toegang heeft, kunt u ook roltoewijzingen verwijderen uit het hoofd bereik (`/`).

1. Verwijder de beheerders bevoegdheden voor gebruikers toegang tot ze weer nodig zijn.

### <a name="list-role-assignments-at-the-root-scope-"></a>Roltoewijzingen weer geven in het hoofd bereik (/)

U kunt alle roltoewijzingen voor een gebruiker in het hoofd bereik (`/`) weer geven.

- Roep [Get roleAssignmentse aan](/rest/api/authorization/roleassignments/listforscope) waarbij `{objectIdOfUser}` de object-id is van de gebruiker van wie u de roltoewijzingen wilt ophalen.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectIdOfUser}'
   ```

### <a name="list-deny-assignments-at-the-root-scope-"></a>Lijst met geweigerde toewijzingen in het hoofd bereik (/)

U kunt alle weiger toewijzingen voor een gebruiker in het hoofd bereik (`/`) weer geven.

- Roep GET denyAssignmentse aan waarbij `{objectIdOfUser}` de object-ID is van de gebruiker van wie u de toewijzingen die u wilt ophalen weigeren.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter=gdprExportPrincipalId+eq+'{objectIdOfUser}'
   ```

### <a name="remove-elevated-access"></a>Verhoogde toegang verwijderen

Wanneer u `elevateAccess`aanroept, maakt u een roltoewijzing voor uzelf, zodat u die bevoegdheden kunt intrekken die u nodig hebt om de toewijzing te verwijderen.

1. Roep [Get roleDefinitionse](/rest/api/authorization/roledefinitions/get) aan waarbij `roleName` gelijk is aan de beheerder van de gebruikers toegang om de naam-id van de beheerder van de gebruikers toegang te bepalen.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter=roleName+eq+'User Access Administrator'
    ```

    ```json
    {
      "value": [
        {
          "properties": {
        "roleName": "User Access Administrator",
        "type": "BuiltInRole",
        "description": "Lets you manage user access to Azure resources.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "*/read",
              "Microsoft.Authorization/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "0001-01-01T08:00:00.0000000Z",
        "updatedOn": "2016-05-31T23:14:04.6964687Z",
        "createdBy": null,
        "updatedBy": null
          },
          "id": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
          "type": "Microsoft.Authorization/roleDefinitions",
          "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9"
        }
      ],
      "nextLink": null
    }
    ```

    Sla de ID op uit de para meter `name`, in dit geval `18d7d88d-d35e-4fb5-a5c3-7773c20a72d9`.

2. U moet ook de roltoewijzing voor de Directory-beheerder in het bereik van de Directory weer geven. Een lijst met alle toewijzingen in het bereik van de Directory voor de `principalId` van de Directory-beheerder die de toegangs aanroep ter verhoging heeft gemaakt. Hiermee worden alle toewijzingen in de map voor de objectid weer geven.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
    
    >[!NOTE] 
    >Een Directory-beheerder mag niet veel toewijzingen hebben, als de vorige query te veel toewijzingen retourneert, kunt u ook een query uitvoeren voor alle toewijzingen, alleen op het niveau van een mapniveau en vervolgens de resultaten filteren: `GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
        
   1. De vorige oproepen retour neren een lijst met roltoewijzingen. Zoek de roltoewijzing op waar de scope zich bevindt `"/"` en de `roleDefinitionId` eindigt met de rolnaam-ID die u in stap 1 hebt gevonden en `principalId` overeenkomt met de objectId van de Directory-beheerder. 
    
      Toewijzing van voorbeeld functie:

       ```json
       {
         "value": [
           {
             "properties": {
               "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
               "principalId": "{objectID}",
               "scope": "/",
               "createdOn": "2016-08-17T19:21:16.3422480Z",
               "updatedOn": "2016-08-17T19:21:16.3422480Z",
               "createdBy": "93ce6722-3638-4222-b582-78b75c5c6d65",
               "updatedBy": "93ce6722-3638-4222-b582-78b75c5c6d65"
             },
             "id": "/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099",
             "type": "Microsoft.Authorization/roleAssignments",
             "name": "e7dd75bc-06f6-4e71-9014-ee96a929d099"
           }
         ],
         "nextLink": null
       }
       ```
        
      Sla de ID opnieuw op in de para meter `name`, in dit geval e7dd75bc-06f6-4e71-9014-ee96a929d099.

   1. Gebruik ten slotte de roltoewijzings-ID voor het verwijderen van de toewijzing die is toegevoegd door `elevateAccess`:

      ```http
      DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099?api-version=2015-07-01
      ```

## <a name="next-steps"></a>Volgende stappen

- [Inzicht in de verschillende rollen](rbac-and-directory-admin-roles.md)
- [Toegang tot Azure-resources beheren met RBAC en de REST API](role-assignments-rest.md)
