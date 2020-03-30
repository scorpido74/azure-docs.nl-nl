---
title: Toegang verhogen om alle Azure-abonnementen en beheergroepen te beheren
description: Beschrijft hoe u de toegang voor een globale beheerder verhogen om alle abonnementen en beheergroepen in Azure Active Directory te beheren met behulp van de Azure-portal of REST API.
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
ms.date: 12/03/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 1731c630cb98ac542ebcdc7aedf07f7bb63eaec0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77137465"
---
# <a name="elevate-access-to-manage-all-azure-subscriptions-and-management-groups"></a>Toegang verhogen om alle Azure-abonnementen en beheergroepen te beheren

Als globale beheerder in Azure Active Directory (Azure AD) hebt u mogelijk geen toegang tot alle abonnementen en beheergroepen in uw directory. In dit artikel worden de manieren beschreven waarop u uw toegang tot alle abonnementen en beheergroepen verhogen.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="why-would-you-need-to-elevate-your-access"></a>Waarom zou je je toegang moeten verhogen?

Als u een globale beheerder bent, kunnen er momenten zijn waarop u het volgende wilt doen:

- Weer toegang krijgen tot een Azure-abonnement of beheergroep wanneer een gebruiker geen toegang meer heeft
- Een andere gebruiker of uzelf toegang te verlenen tot een Azure-abonnement of -beheergroep
- Alle Azure-abonnementen of beheergroepen in een organisatie weergeven
- Een automatiserings-app (zoals een facturerings- of controle-app) toegang geven tot alle Azure-abonnementen of beheergroepen

## <a name="how-does-elevated-access-work"></a>Hoe werkt verhoogde toegang?

Azure AD- en Azure-bronnen worden onafhankelijk van elkaar beveiligd. Dat wil zeggen dat Azure AD-roltoewijzingen geen toegang verlenen tot Azure-resources en Azure-roltoewijzingen geen toegang verlenen tot Azure AD. Als u echter een [globale beheerder](../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator-permissions) in Azure AD bent, u uzelf toegang toewijzen tot alle Azure-abonnementen en beheergroepen in uw directory. Gebruik deze mogelijkheid als u geen toegang hebt tot Azure-abonnementsbronnen, zoals virtuele machines of opslagaccounts, en u uw bevoegdheid voor globale beheerder wilt gebruiken om toegang te krijgen tot deze bronnen.

Wanneer u uw toegang verheft, krijgt u de rol [Gebruikerstoegangsbeheerder](built-in-roles.md#user-access-administrator) in Azure toegewezen op basisgebied (`/`).Hiermee u alle bronnen bekijken en toegang toewijzen in een abonnement of beheergroep in de map. Functietoewijzingen voor gebruikerstoegangsbeheerders kunnen worden verwijderd met Azure PowerShell, Azure CLI of de REST-API.

U moet deze verhoogde toegang verwijderen zodra u de wijzigingen hebt aangebracht die u moet aanbrengen bij de hoofdscope.

![Toegang verhogen](./media/elevate-access-global-admin/elevate-access.png)

## <a name="azure-portal"></a>Azure Portal

### <a name="elevate-access-for-a-global-administrator"></a>Toegang voor een globale beheerder verhogen

Volg deze stappen om de toegang voor een globale beheerder te verhogen met behulp van de Azure-portal.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) of het [Azure Active Directory-beheercentrum](https://aad.portal.azure.com) als globale beheerder.

1. Zoek naar **Azure Active Directory** en selecteer deze optie.

   ![Azure Active Directory selecteren - schermafbeelding](./media/elevate-access-global-admin/search-for-azure-active-directory.png)

1. Selecteer **Eigenschappen** **onder Beheren**.

   ![Eigenschappen voor Azure Active Directory-eigenschappen selecteren - schermafbeelding](./media/elevate-access-global-admin/azure-active-directory-properties.png)

1. Stel **onder Access-beheer voor Azure-resources**de schakel in op **Ja**.

   ![Toegangsbeheer voor Azure-bronnen - schermafbeelding](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   Wanneer u de schakel instelt op **Ja,** krijgt u de rol Gebruikerstoegangsbeheerder toegewezen in Azure RBAC bij hoofdbereik (/). Hiermee u rollen toewijzen in alle Azure-abonnementen en beheergroepen die zijn gekoppeld aan deze Azure AD-map. Deze wisselknop is alleen beschikbaar voor gebruikers die de rol Globale beheerder in Azure AD hebben toegewezen.

   Wanneer u de schakel instelt op **Nee,** wordt de rol Gebruikerstoegangsbeheer in Azure RBAC uit uw gebruikersaccount verwijderd. U geen rollen meer toewijzen in alle Azure-abonnementen en beheergroepen die zijn gekoppeld aan deze Azure AD-map. U alleen de Azure-abonnementen en beheergroepen waartoe u toegang hebt gekregen, bekijken en beheren.

    > [!NOTE]
    > Als u [Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-configure.md)gebruikt, wordt deze schakelfunctie niet gewijzigd in **Nee**. Als u de minste bevoorrechte toegang wilt behouden, raden we u aan deze schakel in te stellen op **Nee** voordat u de roltoewijzing deactiveert.
    
1. Klik **op Opslaan** om de instelling op te slaan.

   Deze instelling is geen globale eigenschap en is alleen van toepassing op de momenteel aangemelde gebruiker. U de toegang niet voor alle leden van de rol Globale beheerder verhogen.

1. Meld u af en meld u weer aan om uw toegang te vernieuwen.

    U moet nu toegang hebben tot alle abonnementen en beheergroepen in uw directory. Wanneer u het deelvenster Toegangsbeheer (IAM) bekijkt, ziet u dat u de rol Gebruikerstoegangsbeheerder bij het hoofdbereik hebt gekregen.

   ![Toewijzingen van abonnementsrollen met hoofdbereik - schermafbeelding](./media/elevate-access-global-admin/iam-root.png)

1. Breng de wijzigingen aan die u moet aanbrengen bij de verhoogde toegang.

    Zie Toegang beheren met [RBAC en de Azure-portal](role-assignments-portal.md)voor informatie over het toewijzen van rollen. Als u Azure AD Privileged Identity Management (PIM) gebruikt, raadpleegt u [Azure-resources ontdekken om te beheren in PIM](../active-directory/privileged-identity-management/pim-resource-roles-discover-resources.md) of [Azure-bronrollen toewijzen in PIM](../active-directory/privileged-identity-management/pim-resource-roles-assign-roles.md).

### <a name="remove-elevated-access"></a>Verhoogde toegang verwijderen

Volg deze stappen om de functietoewijzing`/`voor gebruikerstoegangbeheerders bij het hoofdbereik te verwijderen.

1. Meld u aan als dezelfde gebruiker die is gebruikt om de toegang te verhogen.

1. Klik in de navigatielijst op **Azure Active Directory** en klik vervolgens op **Eigenschappen**.

1. Stel het **Access-beheer voor Azure-resources** in om terug te schakelen naar **Nee**. Aangezien dit een instelling per gebruiker is, moet u zijn aangemeld als dezelfde gebruiker als is gebruikt om de toegang te verhogen.

    Als u de functietoewijzing voor gebruikerstoegangbeheerders probeert te verwijderen in het deelvenster Toegangsbeheer (IAM), ziet u het volgende bericht. Als u de roltoewijzing wilt verwijderen, moet u de schakeling instellen op **Nee** of Azure PowerShell, Azure CLI of de REST-API gebruiken.

    ![Roltoewijzingen met hoofdbereik verwijderen](./media/elevate-access-global-admin/iam-root-remove.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

### <a name="list-role-assignment-at-root-scope-"></a>Roltoewijzing op maken bij het hoofdbereik (/)

Als u de functietoewijzing voor gebruikerstoegangbeheerders`/`voor een gebruiker op basisgebied () wilt weergeven, gebruikt u de opdracht [Get-AzRoleAssignment.](/powershell/module/az.resources/get-azroleassignment)

```azurepowershell
Get-AzRoleAssignment | where {$_.RoleDefinitionName -eq "User Access Administrator" `
  -and $_.SignInName -eq "<username@example.com>" -and $_.Scope -eq "/"}
```

```Example
RoleAssignmentId   : /providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111
Scope              : /
DisplayName        : username
SignInName         : username@example.com
RoleDefinitionName : User Access Administrator
RoleDefinitionId   : 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9
ObjectId           : 22222222-2222-2222-2222-222222222222
ObjectType         : User
CanDelegate        : False
```

### <a name="remove-elevated-access"></a>Verhoogde toegang verwijderen

Volg deze stappen als u de functietoewijzing voor`/`gebruikerstoegangvoor uzelf of een andere gebruiker wilt verwijderen in het hoofdbereik ( )

1. Meld u aan als gebruiker die verhoogde toegang kan verwijderen. Dit kan dezelfde gebruiker zijn die is gebruikt om de toegang te verhogen of een andere globale beheerder met verhoogde toegang bij het hoofdbereik.

1. Gebruik de opdracht [Verwijderen-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) om de functietoewijzing voor gebruikerstoegangsbeheerder te verwijderen.

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName <username@example.com> `
      -RoleDefinitionName "User Access Administrator" -Scope "/"
    ```

## <a name="azure-cli"></a>Azure-CLI

### <a name="list-role-assignment-at-root-scope-"></a>Roltoewijzing op maken bij het hoofdbereik (/)

Als u de functietoewijzing voor gebruikerstoegangbeheerders`/`voor een gebruiker op basisgebied ( ) wilt weergeven, gebruikt u de [opdracht lijst met az-roltoewijzing.](/cli/azure/role/assignment#az-role-assignment-list)

```azurecli
az role assignment list --role "User Access Administrator" --scope "/"
```

```Example
[
  {
    "canDelegate": null,
    "id": "/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
    "name": "11111111-1111-1111-1111-111111111111",
    "principalId": "22222222-2222-2222-2222-222222222222",
    "principalName": "username@example.com",
    "principalType": "User",
    "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "roleDefinitionName": "User Access Administrator",
    "scope": "/",
    "type": "Microsoft.Authorization/roleAssignments"
  }
]

```

### <a name="remove-elevated-access"></a>Verhoogde toegang verwijderen

Volg deze stappen als u de functietoewijzing voor`/`gebruikerstoegangvoor uzelf of een andere gebruiker wilt verwijderen in het hoofdbereik ( )

1. Meld u aan als gebruiker die verhoogde toegang kan verwijderen. Dit kan dezelfde gebruiker zijn die is gebruikt om de toegang te verhogen of een andere globale beheerder met verhoogde toegang bij het hoofdbereik.

1. Gebruik de opdracht [Verwijderen van de AZ-roltoewijzing](/cli/azure/role/assignment#az-role-assignment-delete) om de functietoewijzing voor gebruikerstoegangsbeheerders te verwijderen.

    ```azurecli
    az role assignment delete --assignee username@example.com --role "User Access Administrator" --scope "/"
    ```

## <a name="rest-api"></a>REST API

### <a name="elevate-access-for-a-global-administrator"></a>Toegang voor een globale beheerder verhogen

Gebruik de volgende basisstappen om de toegang voor een globale beheerder te verhogen met behulp van de REST-API.

1. Met BEHULP `elevateAccess`van REST, aanroepen , die u`/`de user access administrator rol op root scope ( ).

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. Maak een [roltoewijzing](/rest/api/authorization/roleassignments) om een rol toe te wijzen aan elk bereik. In het volgende voorbeeld worden de eigenschappen weergegeven voor het`/`toewijzen van de rol {roleDefinitionID} aan het hoofdbereik :

   ```json
   { 
     "properties": {
       "roleDefinitionId": "providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionID}",
       "principalId": "{objectID}",
       "scope": "/"
     },
     "id": "providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
     "type": "Microsoft.Authorization/roleAssignments",
     "name": "11111111-1111-1111-1111-111111111111"
   }
   ```

1. Terwijl u een gebruikerstoegangsbeheerder bent, u`/`ook roltoewijzingen op basisgebied verwijderen ( ).

1. Verwijder de beheerdersrechten voor gebruikerstoegang totdat ze opnieuw nodig zijn.

### <a name="list-role-assignments-at-root-scope-"></a>Roltoewijzingen aanbieden bij hoofdbereik (/)

U alle roltoewijzingen voor een gebruiker`/`op basis van het hoofdbereik weergeven ().

- Call [GET-rolToewijzingen](/rest/api/authorization/roleassignments/listforscope) waar `{objectIdOfUser}` is de object-id van de gebruiker wiens roltoewijzingen u wilt ophalen.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectIdOfUser}'
   ```

### <a name="list-deny-assignments-at-root-scope-"></a>Toewijzingen weigeren op basisvan het hoofdbereik (/)

U alle weigeringstoewijzingen voor een gebruiker`/`op basis van het hoofdbereik vermelden ( ).

- Oproep GET Weigert `{objectIdOfUser}` waar is de object-ID van de gebruiker wiens weigeringstoewijzingen u wilt ophalen.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter=gdprExportPrincipalId+eq+'{objectIdOfUser}'
   ```

### <a name="remove-elevated-access"></a>Verhoogde toegang verwijderen

Wanneer u `elevateAccess`aanroept, maakt u een roltoewijzing voor uzelf, dus om de bevoegdheden in te`/`trekken die u nodig hebt om de roltoewijzing voor gebruikerstoegangsbeheerders voor uzelf te verwijderen bij het hoofdbereik ( )

1. Call [GET-rolDefinities](/rest/api/authorization/roledefinitions/get) zijn `roleName` gelijk aan gebruikerstoegangsbeheerder om de naam-id van de functie Gebruikerstoegangsbeheerder te bepalen.

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

    Sla de ID `name` op van `18d7d88d-d35e-4fb5-a5c3-7773c20a72d9`de parameter, in dit geval .

1. U moet ook de roltoewijzing voor de directorybeheerder vermelden bij directoryscope. Vermeld alle toewijzingen in `principalId` directoryscope voor de directorybeheerder die het elevate access-gesprek heeft uitgevoerd. Hiermee worden alle toewijzingen in de map voor de objectid vermeld.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
        
    >[!NOTE] 
    >Een directorybeheerder mag niet veel toewijzingen hebben, als de vorige query te veel toewijzingen retourneert, u ook query's voor alle toewijzingen alleen op directoryscopeniveau en vervolgens de resultaten filteren:`GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
            
1. De vorige oproepen retourneren een lijst met roltoewijzingen. Zoek de roltoewijzing waar `"/"` het `roleDefinitionId` bereik zich bevindt en de `principalId` uiteinden met de rolnaam-id die u in stap 1 hebt gevonden en die overeenkomt met de objectId van de directorybeheerder. 
    
    Voorbeeldroltoewijzing:
    
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
            "createdBy": "22222222-2222-2222-2222-222222222222",
            "updatedBy": "22222222-2222-2222-2222-222222222222"
          },
          "id": "/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
          "type": "Microsoft.Authorization/roleAssignments",
          "name": "11111111-1111-1111-1111-111111111111"
        }
      ],
      "nextLink": null
    }
    ```
    
    Sla de ID opnieuw `name` op van de parameter, in dit geval 1111111-1111-1111-1111-1111111111111.

1. Tot slot gebruik u de taaktoewijzings-id om de toewijzing te verwijderen die is toegevoegd door: `elevateAccess`

    ```http
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111?api-version=2015-07-01
    ```

## <a name="next-steps"></a>Volgende stappen

- [Inzicht in de verschillende rollen](rbac-and-directory-admin-roles.md)
- [Toegang tot Azure-bronnen beheren met RBAC en de REST API](role-assignments-rest.md)
