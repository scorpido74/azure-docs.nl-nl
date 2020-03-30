---
title: Limieten voor het maken van app-registraties verwijderen - Azure AD | Microsoft Documenten
description: Een aangepaste rol toewijzen om onbeperkte app-registraties toe te kennen in de Azure AD Active Directory
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
ms.openlocfilehash: 7acd76ff45f783f614b2a1d3f0d5c10d800a1ea9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77559042"
---
# <a name="quickstart-grant-permission-to-create-unlimited-app-registrations"></a>Snelstart: Geef toestemming om onbeperkte app-registraties te maken

In deze quickstart maakt u een aangepaste rol met toestemming om een onbeperkt aantal app-registraties te maken en wijst u die rol vervolgens toe aan een gebruiker. De toegewezen gebruiker kan vervolgens de Azure AD-portal, Azure AD PowerShell of Microsoft Graph API gebruiken om toepassingsregistraties te maken. In tegenstelling tot de ingebouwde rol Application Developer, deze aangepaste rol verleent de mogelijkheid om een onbeperkt aantal toepassingsregistraties te maken. De rol Toepassingsontwikkelaar verleent de mogelijkheid, maar het totale aantal gemaakte objecten is beperkt tot 250 om te voorkomen dat [u het objectquotum voor de hele map](directory-service-limits-restrictions.md)raakt.

Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisite"></a>Vereiste

De minst bevoorrechte rol die nodig is voor het maken en toewijzen van aangepaste Azure AD-rollen is de beheerder van de bevoorrechte rol.

## <a name="create-a-new-custom-role-using-the-azure-ad-portal"></a>Een nieuwe aangepaste rol maken met de Azure AD-portal

1. Meld u aan bij het [Azure AD-beheercentrum](https://aad.portal.azure.com) met beheerders van bevoorrechte rollen of machtigingen voor globale beheerders in de Azure AD-organisatie.
1. Selecteer **Azure Active Directory,** selecteer **Rollen en beheerders**en selecteer Vervolgens Nieuwe **aangepaste rol**.

    ![Rollen maken of bewerken op de pagina Rollen en beheerders](./media/roles-create-custom/new-custom-role.png)

1. Geef op het tabblad **Basisbeginselen** 'Maker van toepassingsregistratie' op voor de naam van de rol en 'Kan een onbeperkt aantal toepassingsregistraties maken' voor de rolbeschrijving en selecteer **Volgende**.

    ![een naam en beschrijving opgeven voor een aangepaste rol op het tabblad Basisbeginselen](./media/roles-quickstart-app-registration-limits/basics-tab.png)

1. Typ op het tabblad **Machtigingen** 'microsoft.directory/applications/create' in het zoekvak en schakel vervolgens de selectievakjes naast de gewenste machtigingen in en selecteer **Volgende**.

    ![De machtigingen voor een aangepaste rol selecteren op het tabblad Machtigingen](./media/roles-quickstart-app-registration-limits/permissions-tab.png)

1. Controleer op het tabblad **Controleren + maken** de machtigingen en selecteer **Maken**.

### <a name="assign-the-role-to-a-user-using-the-azure-ad-portal"></a>De rol toewijzen aan een gebruiker met de Azure AD-portal

1. Meld u aan bij het [Azure AD-beheercentrum](https://aad.portal.azure.com) met machtigingen voor bevoorrechte rollen of machtigingen voor globale beheerders in uw Azure AD-organisatie.
1. Selecteer **Azure Active Directory** en selecteer rollen en **beheerders**.
1. Selecteer de rol Maker van maker van toepassingsregistratie en selecteer **Toewijzing toevoegen**.
1. Selecteer de gewenste gebruiker en klik op **Selecteren** om de gebruiker aan de rol toe te voegen.

Klaar! In deze quickstart hebt u een aangepaste rol gemaakt met toestemming om een onbeperkt aantal app-registraties te maken en deze rol vervolgens aan een gebruiker toe te wijzen.

> [!TIP]
> Als u de rol wilt toewijzen aan een toepassing met de Azure AD-portal, voert u de naam van de toepassing in in het zoekvak van de toewijzingspagina. Toepassingen worden niet standaard in de lijst weergegeven, maar worden geretourneerd in zoekresultaten.

### <a name="app-registration-permissions"></a>Machtigingen voor app-registratie

Er zijn twee machtigingen beschikbaar voor het verlenen van de mogelijkheid om toepassingsregistraties te maken, elk met verschillend gedrag.

- microsoft.directory/applications/createAsOwner: het toewijzen van deze machtiging resulteert in de maker wordt toegevoegd als de eerste eigenaar van de gemaakte app-registratie, en de gemaakte app registratie telt mee voor de maker 250 gemaakte objecten quotum.
- microsoft.directory/applicationPolicies/create: Door deze machtiging toe te voegen, wordt de maker niet toegevoegd als de eerste eigenaar van de gemaakte app-registratie en telt de gemaakte app-registratie niet mee voor het quotum van de maker van 250 gemaakte objecten. Gebruik deze toestemming zorgvuldig, omdat er niets is dat de cessionaris ervan weerhoudt app-registraties te maken totdat het quotum op directoryniveau is bereikt. Als beide machtigingen zijn toegewezen, heeft deze toestemming voorrang.

## <a name="create-a-custom-role-using-azure-ad-powershell"></a>Een aangepaste rol maken met Azure AD PowerShell

Maak een nieuwe rol met het volgende PowerShell-script:

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

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>De aangepaste rol toewijzen met Azure AD PowerShell

#### <a name="prepare-powershell"></a>PowerShell voorbereiden

Installeer eerst de Azure AD PowerShell-module vanuit de [PowerShell-galerie.](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17) Importeer vervolgens de Azure AD PowerShell-voorbeeldmodule met de volgende opdracht:

```powershell
import-module azureadpreview
```

Als u wilt controleren of de module klaar is voor gebruik, moet u de versie die met de volgende opdracht wordt geretourneerd, koppelen aan de versie die hier wordt vermeld:

```powershell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADAdministrati...}
```

#### <a name="assign-the-custom-role"></a>De aangepaste rol toewijzen

Wijs de rol toe met het onderstaande PowerShell-script:

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

### <a name="create-a-custom-role-using-microsoft-graph-api"></a>Een aangepaste rol maken met Microsoft Graph API

HTTP-verzoek om de aangepaste rol te maken.

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

### <a name="assign-the-custom-role-using-microsoft-graph-api"></a>De aangepaste rol toewijzen met microsoft graph-api

De roltoewijzing combineert een beveiligingshoofd-id (die een gebruiker of serviceprincipal kan zijn), een functiedefinitie-id (rol) en een Azure AD-bronbereik.

HTTP-verzoek om een aangepaste rol toe te wijzen.

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

- Voel je vrij om met ons te delen op het [Azure AD-forum voor beheerfuncties](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Zie [Beheerdersrollen toewijzen](directory-assign-admin-roles.md)voor meer informatie over rollen en toewijzing van beheerders.
- Zie een vergelijking van [standaardmachtigingen voor gasten en leden](../fundamentals/users-default-permissions.md)voor standaardgebruikersmachtigingen voor standaardgebruikers.
