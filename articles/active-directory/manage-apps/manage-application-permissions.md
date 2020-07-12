---
title: Gebruikers-en beheerders machtigingen beheren-Azure Active Directory | Microsoft Docs
description: Meer informatie over het controleren en beheren van machtigingen voor de toepassing in azure AD. Bijvoorbeeld, als u alle machtigingen wilt intrekken die aan een toepassing zijn verleend.
services: active-directory
author: mimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 7/10/2020
ms.author: mimart
ms.reviewer: luleonpla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00d878c7b2f78d037e89235f3bb30c02fd11a7ae
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/11/2020
ms.locfileid: "86277631"
---
# <a name="take-action-on-overpriviledged-or-suspicious-application-in-azure-active-directory"></a>Actie ondernemen op overpriviledged of verdachte toepassing in Azure Active Directory

Meer informatie over het controleren en beheren van toepassings machtigingen. Op basis van het scenario biedt dit artikel verschillende acties die u kunt uitvoeren om uw toepassing te beveiligen. Dit geldt voor alle toepassingen die zijn toegevoegd aan uw Azure Active Directory-Tenant (Azure AD) via toestemming van de gebruiker of beheerder.

Zie [Azure Active Directory instemming-Framework](../develop/consent-framework.md)voor meer informatie over het inzenden van toepassingen.

## <a name="prerequisites"></a>Vereisten

Als u de onderstaande acties wilt uitvoeren, moet u zich aanmelden als globale beheerder, een toepassings beheerder of een Cloud toepassings beheerder.

Als u de toegang tot toepassingen wilt beperken, moet u de gebruikers toewijzing vereisen en vervolgens gebruikers of groepen toewijzen aan de toepassing.  Zie [methoden voor het toewijzen van gebruikers en groepen](methods-for-assigning-users-and-groups.md)voor meer informatie.

U kunt toegang krijgen tot de Azure AD-Portal om context afhankelijke Power shell-scripts te krijgen om de acties uit te voeren.
 
1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als een globale beheerder, een toepassings beheerder of een Cloud toepassings beheerder.
2. Selecteer **Azure Active Directory**  >  **bedrijfs toepassingen**.
3. Selecteer de toepassing waarvoor u de toegang wilt beperken.
4. Selecteer **Machtigingen**. Selecteer in de opdracht balk de optie **Controleer machtigingen**.

![Machtigingen controleren](./media/manage-application-permissions/review-permissions.png)

## <a name="i-want-to-control-access-to-an-application"></a>Ik wil de toegang tot een toepassing beheren

U kunt het beste de toegang tot deze toepassing beperken door de instelling gebruikers toewijzing in te scha kelen.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als een globale beheerder, een toepassings beheerder of een Cloud toepassings beheerder.
2. Selecteer **Azure Active Directory**  >  **bedrijfs toepassingen**.
3. Selecteer de toepassing waarvoor u de toegang wilt beperken.
4. Selecteer **Eigenschappen** en stel de vereiste optie voor gebruikers vereisten in op Ja.
5. Selecteer **gebruikers en groepen** en verwijder ongewenste gebruikers die zijn toegewezen aan de toepassing.
6. Gebruiker (s) of groep (en) toewijzen aan de toepassing.

Optioneel, u kunt alle gebruikers die zijn toegewezen aan de toepassing, verwijderen met behulp van Power shell.

## <a name="i-want-to-revoke-all-permissions-for-an-application"></a>Ik wil alle machtigingen voor een toepassing intrekken

Met behulp van de Power shell worden alle machtigingen ingetrokken die aan deze toepassing zijn verleend.

> [!NOTE]
> Als de huidige verleende machtiging wordt ingetrokken, worden gebruikers niet meer voor reconseing voor de toepassingen. Als u wilt voor komen dat gebruikers worden geblokkeerd voor de toepassing, lees dan [configureren hoe eind gebruikers toestemming geven voor toepassingen](configure-user-consent.md).

Optioneel kunt u de toepassing uitschakelen om te voor komen dat gebruikers toegang krijgen tot de app en de toepassing heeft geen toegang tot uw gegevens.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als een globale beheerder, een toepassings beheerder of een Cloud toepassings beheerder.
2. Selecteer **Azure Active Directory**  >  **bedrijfs toepassingen**.
3. Selecteer de toepassing waarvoor u de toegang wilt beperken.
4. **Eigenschappen** selecteren en vervolgens ingeschakeld instellen dat gebruikers zich kunnen aanmelden? op Nee.

## <a name="application-is-suspicious-and-i-want-to-investigate"></a>Toepassing is verdacht en ik wil onderzoek doen

U kunt het beste de toegang tot deze toepassing beperken door de instelling gebruikers toewijzing in te scha kelen en de machtigingen te controleren die gebruikers en beheerders hebben verleend aan de toepassing.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als een globale beheerder, een toepassings beheerder of een Cloud toepassings beheerder.
3. Selecteer **Azure Active Directory**  >  **bedrijfs toepassingen**.
5. Selecteer de toepassing waarvoor u de toegang wilt beperken.
6. Selecteer **Eigenschappen** en stel de vereiste optie voor gebruikers vereisten in op Ja.
7. Selecteer **machtigingen** en controleer de machtigingen beheerder en gebruiker gemachtigd.

Optioneel kunt u het volgende doen:

- Gebruik Power shell om alle gebruikers te verwijderen die zijn toegewezen om het aanmelden bij de toepassing te stoppen.
- Met behulp van Power shell kunnen vernieuwings tokens voor gebruikers die toegang hebben tot de toepassing, ongeldig worden gemaakt.
- Alle machtigingen voor deze toepassing intrekken met behulp van Power shell
- Schakel de toepassing uit om gebruikers toegang te blok keren en stop deze toepassingen toegang tot uw gegevens.


## <a name="application-is-malicious-and-im-compromised"></a>Toepassing is kwaad aardig en er is inbreuk

U kunt de toepassing het beste uitschakelen om te voor komen dat gebruikers toegang krijgen tot de app en de toepassing heeft geen toegang tot uw gegevens. Als u in plaats daarvan de toepassing verwijdert, kunnen eind gebruikers de toepassing opnieuw ingeven en toegang verlenen tot uw gegevens.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als een globale beheerder, een toepassings beheerder of een Cloud toepassings beheerder.
2. Selecteer **Azure Active Directory**  >  **bedrijfs toepassingen**.
3. Selecteer de toepassing waarvoor u de toegang wilt beperken.
4. Selecteer **Eigenschappen** en kopieer vervolgens de object-id.

### <a name="powershell-commands"></a>PowerShell-opdrachten


Object-ID van Service-Principal ophalen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als een globale beheerder, een toepassings beheerder of een Cloud toepassings beheerder.
2. Selecteer **Azure Active Directory**  >  **bedrijfs toepassingen**.
3. Selecteer de toepassing waarvoor u de toegang wilt beperken.
4. Selecteer **Eigenschappen** en kopieer vervolgens de object-id.

```powershell
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $sp.ObjectId
```
Verwijder alle gebruikers die zijn toegewezen aan de toepassing.
 ```powershell
    Connect-AzureAD

    # Get Service Principal using objectId
    $sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

    # Get Azure AD App role assignments using objectId of the Service Principal
    $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true

    # Remove all users and groups assigned to the application
    $assignments | ForEach-Object {
        if ($_.PrincipalType -eq "User") {
            Remove-AzureADUserAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
        } elseif ($_.PrincipalType -eq "Group") {
            Remove-AzureADGroupAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
        }
    }
 ```

Machtigingen die zijn verleend aan de toepassing intrekken

```powershell
    Connect-AzureAD

    # Get Service Principal using objectId
    $sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

    # Get all delegated permissions for the service principal
    $spOAuth2PermissionsGrants = Get-AzureADOAuth2PermissionGrant -All $true| Where-Object { $_.clientId -eq $sp.ObjectId }

    # Remove all delegated permissions
    $spOAuth2PermissionsGrants | ForEach-Object {
        Remove-AzureADOAuth2PermissionGrant -ObjectId $_.ObjectId
    }

    # Get all application permissions for the service principal
    $spApplicationPermissions = Get-AzureADServiceAppRoleAssignedTo -ObjectId $sp.ObjectId -All $true | Where-Object { $_.PrincipalType -eq "ServicePrincipal" }

    # Remove all delegated permissions
    $spApplicationPermissions | ForEach-Object {
        Remove-AzureADServiceAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.objectId
    }
```
Vernieuwings tokens ongeldig maken
```powershell
        Connect-AzureAD

        # Get Service Principal using objectId
        $sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

        # Get Azure AD App role assignments using objectID of the Service Principal
        $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true | Where-Object {$_.PrincipalType -eq "User"}

        # Revoke refresh token for all users assigned to the application
        $assignments | ForEach-Object {
            Revoke-AzureADUserAllRefreshToken -ObjectId $_.PrincipalId
        }
```
## <a name="next-steps"></a>Volgende stappen
- [Toestemming voor toepassingen beheren en aanvraag voor toestemming evalueren](manage-consent-requests.md)
- [Gebruikerstoestemming configureren](configure-user-consent.md)
- [Werk stroom voor het beheer van toestemming configureren](configure-admin-consent-workflow.md)
