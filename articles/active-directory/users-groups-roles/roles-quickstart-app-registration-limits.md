---
title: Limieten voor het maken van app-registraties verwijderen-Azure AD | Microsoft Docs
description: Een aangepaste rol toewijzen voor het verlenen van onbeperkte app-registraties in de Azure AD-Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5acfa98636f54f87facf9771beb7d94dbd2b324
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84731729"
---
# <a name="quickstart-grant-permission-to-create-unlimited-app-registrations"></a>Snelstartgids: machtiging verlenen voor het maken van onbeperkte app-registraties

In deze Quick Start maakt u een aangepaste rol met machtigingen voor het maken van een onbeperkt aantal app-registraties en vervolgens wijst u die rol toe aan een gebruiker. De toegewezen gebruiker kan vervolgens de Azure AD-Portal, Azure AD Power shell of de Microsoft Graph-API gebruiken om toepassings registraties te maken. In tegens telling tot de ingebouwde rol ontwikkelaar van de toepassing verleent deze aangepaste rol de mogelijkheid om een onbeperkt aantal toepassings registraties te maken. De ontwikkelaar van de toepassing verleent de mogelijkheid, maar het totale aantal gemaakte objecten is beperkt tot 250 om te voor komen dat [het object quotum voor het hele directory](directory-service-limits-restrictions.md)wordt bereikt.

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisite"></a>Vereiste

De rol van de meest bevoegde rol is vereist voor het maken en toewijzen van aangepaste rollen van Azure AD.

## <a name="create-a-new-custom-role-using-the-azure-ad-portal"></a>Een nieuwe aangepaste rol maken met behulp van de Azure AD-Portal

1. Meld u aan bij het [Azure AD-beheer centrum](https://aad.portal.azure.com)   met privileged Role Administrator of Global Administrator Permissions in de Azure AD-organisatie.
1. Selecteer **Azure Active Directory**, selecteer **rollen en beheerders**en selecteer vervolgens **nieuwe aangepaste rol**.

    ![Rollen maken of bewerken op de pagina rollen en beheerders](./media/roles-create-custom/new-custom-role.png)

1. Geef op het tabblad **basis beginselen** "toepassings registratie Maker" op voor de naam van de rol en "kan een onbeperkt aantal toepassings registraties maken" voor de beschrijving van de functie en selecteer vervolgens **volgende**.

    ![Geef een naam en beschrijving op voor een aangepaste rol op het tabblad basis beginselen](./media/roles-quickstart-app-registration-limits/basics-tab.png)

1. Voer op het tabblad **machtigingen** het volgende in: ' micro soft. Directory/toepassingen/maken ' in het zoekvak en selecteer vervolgens de selectie vakjes naast de gewenste machtigingen en selecteer **volgende**.

    ![De machtigingen voor een aangepaste rol selecteren op het tabblad Machtigingen](./media/roles-quickstart-app-registration-limits/permissions-tab.png)

1. Controleer op het tabblad **controleren en maken** de machtigingen en selecteer **maken**.

### <a name="assign-the-role-to-a-user-using-the-azure-ad-portal"></a>De rol toewijzen aan een gebruiker met behulp van de Azure AD-Portal

1. Meld u aan bij het [Azure AD-beheer centrum](https://aad.portal.azure.com)   met privileged Role Administrator of Global Administrator Permissions in uw Azure AD-organisatie.
1. Selecteer **Azure Active Directory** en selecteer vervolgens **rollen en beheerders**.
1. Selecteer de rol maker van de toepassing registreren en selecteer **toewijzing toevoegen**.
1. Selecteer de gewenste gebruiker en klik op **selecteren** om de gebruiker aan de rol toe te voegen.

Klaar! In deze Quick Start hebt u een aangepaste rol gemaakt met de machtiging om een onbeperkt aantal app-registraties te maken en vervolgens die rol toe te wijzen aan een gebruiker.

> [!TIP]
> Als u de rol wilt toewijzen aan een toepassing met behulp van de Azure AD-Portal, voert u de naam van de toepassing in het zoekvak van de pagina toewijzing in. Toepassingen worden standaard niet weer gegeven in de lijst, maar worden geretourneerd in Zoek resultaten.

### <a name="app-registration-permissions"></a>Machtigingen voor app-registratie

Er zijn twee machtigingen beschikbaar voor het verlenen van de mogelijkheid om toepassings registraties te maken, elk met verschillende gedragingen.

- micro soft. Directory/Applications/createAsOwner: als u deze machtigingen toewijst, wordt de maker toegevoegd als de eerste eigenaar van de gemaakte app-registratie en wordt de gemaakte app-registratie geteld voor het quotum van 250 gemaakte objecten van de maker.
- micro soft. Directory/applicationPolicies/create: als u deze machtiging toewijst, wordt de maker niet toegevoegd als de eerste eigenaar van de gemaakte app-registratie en wordt de gemaakte app-registratie niet meegeteld voor het 250 gemaakte object quota van de maker. Gebruik deze machtiging aandachtig door, omdat er niets is dat de toegewezen persoon app-registraties kan maken totdat de quotum op Directory niveau is bereikt. Als beide machtigingen zijn toegewezen, heeft deze machtiging voor rang.

## <a name="create-a-custom-role-using-azure-ad-powershell"></a>Een aangepaste rol maken met behulp van Azure AD Power shell

Maak een nieuwe rol met behulp van het volgende Power shell-script:

``` PowerShell
# Basic role information
$description = "Application Registration Creator"
$displayName = "Can create an unlimited number of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/createAsOwner"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>De aangepaste rol toewijzen met behulp van Azure AD Power shell

#### <a name="prepare-powershell"></a>Power shell voorbereiden

Installeer eerst de Azure AD Power shell-module van de [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Importeer vervolgens de Azure AD Power shell preview-module met de volgende opdracht:

```powershell
import-module azureadpreview
```

Als u wilt controleren of de module gereed is voor gebruik, gaat u naar de versie die wordt geretourneerd door de volgende opdracht:

```powershell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADAdministrati...}
```

#### <a name="assign-the-custom-role"></a>De aangepaste rol toewijzen

Wijs de rol toe met behulp van het onderstaande Power shell-script:

``` PowerShell
# Basic role information
$description = "Application Registration Creator"
$displayName = "Can create an unlimited number of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/create"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="create-a-custom-role-using-microsoft-graph-api"></a>Een aangepaste rol maken met behulp van Microsoft Graph-API

HTTP-aanvraag voor het maken van de aangepaste rol.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
```

Hoofdtekst

```HTTP
{
    "description":"Can create an unlimited number of application registrations.",
    "displayName":"Application Registration Creator",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "resourceActions":
            {
                "allowedResourceActions":
                [
                    "microsoft.directory/applications/create"
                ]
            },
            "condition":null
        }
    ],
    "templateId":"<PROVIDE NEW GUID HERE>",
    "version":"1"
}
```

### <a name="assign-the-custom-role-using-microsoft-graph-api"></a>De aangepaste rol toewijzen met behulp van Microsoft Graph-API

De roltoewijzing combineert een beveiligings-principal-ID (die een gebruiker of service-principal kan zijn), een roldefinitie-ID (rol) en een Azure AD-resource bereik.

HTTP-aanvraag om een aangepaste rol toe te wijzen.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Hoofdtekst

``` HTTP
{
    "principalId":"<PROVIDE OBJECTID OF USER TO ASSIGN HERE>",
    "roleDefinitionId":"<PROVIDE OBJECTID OF ROLE DEFINITION HERE>",
    "resourceScopes":["/"]
}
```

## <a name="next-steps"></a>Volgende stappen

- U kunt graag delen met ons op het [forum van Azure AD-beheerders](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Zie [beheerders rollen toewijzen](directory-assign-admin-roles.md)voor meer informatie over functies en de toewijzing van beheerdersrol.
- Zie voor standaard gebruikers machtigingen een [vergelijking van de standaard machtigingen voor gast-en gebruikers rechten](../fundamentals/users-default-permissions.md).
